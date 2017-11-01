# smarm 安装
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

## swarm 部署
- manager node
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

- work node
```
$ docker swarm join \
  --token  SWMTKN-1-49nj1cmql0jkz5s954yi3oex3nedyz0fb0xx14ie39trti4wxv-8vxv8rssmk743ojnwacrr2e7c \
  192.168.99.100:2377

This node joined a swarm as a worker.
```
