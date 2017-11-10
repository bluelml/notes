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
$ docker pull registry
```  
  
默认情况下，会将仓库存放于容器内的/tmp/registry目录下，这样如果容器被删除，则存放于容器中的镜像也会丢失，所以我们一般情况下会指定本地一个目录挂载到容器内的/tmp/registry下，如下：
```
$ docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
```
  
- 测试  

```
$ docker pull busybox

修改tag
$ docker tag busybox 192.168.112.136:5000/busybox

push进私有仓库
$ docker push 192.168.112.136:5000/busybox
```

如果有错误：原因-docker registry交互默认使用的是https，然而此处搭建的私有仓库只提供http服务  
解决方法： 创建/etc/docker/daemon.json, 写入配置
```
{ "insecure-registries":["192.168.112.136:5000"] }
```

- 重启服务  

```
$ sudo service docker restart

$ docker run -d -p 5000:5000 -v /opt/data/registry:/tmp/registry registry
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
#### manager node  
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

NOTE:
这个步骤完成后就可以将work node加入集群。具体具体命令参考最后的work node部分。

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

push image到本地registry,   
```
docker-compose push
```  
push 完成后就可以用stack deploy创建服务了.  

c. 创建服务  
```
docker stack deploy test --compose-file docker-compose.yml

test:服务名称  
```

问题1:
```
Error response from daemon: rpc error: code = 9 desc = service needs ingress network, but no ingress network is present
```  
解决方法，请先创建ingress网络：  
```
docker network create --ingress --driver overlay ingress
```

问题2：
```
"invalid mount config for type "bind": bind source path does not exist"
```
解决方法，
```

```  
  
  
  
  
  
    
d. manger结点不运行任务  
```
docker node update --availability drain <NODE>
```


#### work node

Manager 创建初始化集群后，通过下面命令加入集群:
```
$ docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377

This node joined a swarm as a worker.
```

## 撤销部署
```
docker stack rm friendly-service //干掉我们部署的服务，但是记住swarm还在，一会儿我们要强制退出swarm
docker node ls // 查看在运行的swarm
docker swarm leave --force // 强制退出swarm
```

## Secrets的使用
- manager创建secrets
```
# 创建名字为my_secret的secrets
docker secret create my_secret my_data.txt
```
- manager查询secrets
```
docker secret ls
ID                          NAME                CREATED             UPDATED
uxy3mjd8kqktkaym1txeqlgk3   my_secret          30 minutes ago      30 minutes ago
```
- work node 挂载secrets 
  服务部署后secrets会挂载在docker的下面目录： 
```
/run/secrets
```
可以通过下列命令查看文件
```
$ docker exec $(docker ps --filter name=redis -q) ls -l /run/secrets

total 4
-r--r--r--    1 root     root            17 Dec 13 22:48 my_secret
``` 

- compile file的编写
例子
```
version: '3.2'
services:
  mysqlshort:
    image: 'mysql:5.7'
    deploy:
      mode: replicated
      replicas: 1
      update_config:
        failure_action: continue
      restart_policy:
        condition: none
    environment:
       - MYSQL_ROOT_PASSWORD=/run/secrets/my_mysql_password_v1 #配置 MySQL 应用使用 /run/secrets/my_mysql_password_v1 文件作为 root 密码
    secrets:
      - my_mysql_password_v1 #您所创建的 secret 的名称
      - my_secret_v1 #您所创建的 secret 的名称
secrets: #对 secrets 进行声明
  my_mysql_password_v1:
    external: true
  my_secret_v1:
    external: true
```

- secrets详细描述
例子
```
version: '3.2'
services:
  mysqlshort:
    image: 'mysql:5.7'
    deploy:
      mode: replicated
      replicas: 1
      update_config:
        failure_action: continue
      restart_policy:
        condition: none
    environment:
       - MYSQL_ROOT_PASSWORD=/run/secrets/my_mysql #配置 MySQL 应用使用 /run/secrets/my_mysql_password_v1 文件作为 root 密码
    secrets:
      - source: my_mysql_password_v1 #您所创建的 secret 的名称
      - target: my_mysql
      - uid: '0'
      - gid: '0'
      - mode: 444
      - source: my_secret_v1 #您所创建的 secret 的名称
      - target: my_secret
      - uid: '0'
      - gid: '0'
      - mode: 444
secrets: #对 secrets 进行声明
  my_mysql_password_v1:
    external: true
  my_secret_v1:
    external: true
```
```
* source：您在容器服务中所创建的密钥的名称。
* target：将要挂载到容器的  /run/secrets/  目录下的文件的名称。默认情况下，与 source 值相同，即容器在  /run/secrets/<secret_name>  目录下访问密钥。
* uid：拥有容器  /run/secrets/  目录下的 secret 文件的用户 ID。默认为 0。
* gid：拥有容器  /run/secrets/  目录下的 secret 文件的用户组 ID。默认为 0。
* mode：将要挂载到容器  /run/secrets/  目录下的 secret 文件的权限。格式为八进制，例如：0444 表示全局可读。由于 secrets 是挂载在临时文件系统上的，所以 secrets 不可写；因此，如果您设置了可写位，系统会忽视您的设置。如果您不熟悉 UNIX 的权限模式，可以使用 Permission Calculator。
```
