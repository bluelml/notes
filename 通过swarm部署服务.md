# swarm 安装
官方文档
```
https://docs.docker.com/engine/swarm/swarm-tutorial/
```

## docker 安装
Install Docker Xenial 16.04

```
sudo apt-get remove docker docker-engine docker.io
sudo apt-get update

sudo apt-get install \
 linux-image-extra-$(uname -r) \
 linux-image-extra-virtual
 
sudo apt-get update 
sudo apt-get install \
 apt-transport-https \
 ca-certificates \
 curl \
 software-properties-common

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository \
"deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install docker-ce

sudo groupadd docker
sudo usermod -a -G docker ${USR}
sudo service docker restart
```

## 配置私有仓库
```
$ sudo docker pull registry
```  
  
默认情况下，会将仓库存放于容器内的/tmp/registry目录下，这样如果容器被删除，则存放于容器中的镜像也会丢失，所以我们一般情况下会指定本地一个目录挂载到容器内的/tmp/registry下，如下：
```
$ sudo docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
```

```
$ sudo docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
```


- 测试
```
$ sudo docker pull busybox

修改tag
$ sudo docker tag busybox 192.168.112.136:5000/busybox

push进私有仓库
$ sudo docker push 192.168.112.136:5000/busybox
```

如果有错误：原因-docker registry交互默认使用的是https，然而此处搭建的私有仓库只提供http服务  
解决方法： 创建/etc/docker/daemon.json, 写入配置
```
{ "insecure-registries":["192.168.112.136:5000"] }
```

- 重启服务  
```
$ sudo service docker restart

$ sudo docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
```
- 查询本地仓库内容  
```
curl 192.168.123.79:5000/v2/_catalog

显示： {"repositories":["mongo","ss_app"]}
```

## Work node 添加本地仓库
/etc/docker/daemon.json 添加如下内容：  
```
{
  "insecure-registries" : ["myregistrydomain.com:5000"],
  "registry-mirrors": ["myregistrydomain.com:5000"]
}
```


## swarm 部署
- manager node  
a. 初始化集群  
```
docker swarm init --advertise-addr <MANAGER-IP>
```
例子：
```
$ docker swarm init --advertise-addr 192.168.99.100
Swarm initialized: current node (dxn1zf6l61qsb1josjja83ngz) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
    192.168.99.100:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

b. 编写compose,并测试compose  
```
https://docs.docker.com/engine/swarm/stack-deploy/#test-the-app-with-compose
```
```
docker-compose up -d

docker-compose ps

 docker-compose down --volumes
```

NOTE: 
如遇到下列错误
```
ERROR: for auth  Cannot start service auth: driver failed programming external connectivity on endpoint ssfacereccontainer_auth_1 (e028df890134e2a995ccc91d7253728d153e009630720f0b4984d005ef92e39b): Error starting userland proxy: listen tcp 0.0.0.0:811: bind: address already in use
ERROR: Encountered errors while bringing up the project.
```

请尝试下面的方法
https://github.com/jwilder/nginx-proxy/issues/839
```
docker stop $(docker ps -a -q); docker rm $(docker ps -a -q); docker volume rm $(docker volume ls -qf dangling=true)

docker network rm $(docker network ls -q)

sudo service docker restart

sudo lsof -nP | grep LISTEN

结束占用端口的进程
sudo kill -9 1548
```



c. 创建服务  
```
docker stack deploy test --compose-file docker-compose.yml
```
- test:服务名称  
  
d. manger结点不运行任务  
```
docker node update --availability drain <NODE>
```


- work node
```
$ docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377

This node joined a swarm as a worker.
```
