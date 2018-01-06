---
title: [open-stf](https://github.com/openstf/stf) 安装篇（linux）
tag: open-stf
---

# 第一种 方式前置依赖如下：

## 基础命令依赖如下(以下为debian类linux命令)
```
sudo apt-get update 
sudo apt-get install -y git
sudo apt-get install -y curl
sudo apt-get install -y android-tools-adb 
sudo apt-get install -y python 
sudo apt-get install -y autoconf 
sudo apt-get install -y automake 
sudo apt-get install -y libtool 
sudo apt-get install -y build-essential 
sudo apt-get install -y ninja-build 
sudo apt-get install -y libzmq3-dev 
sudo apt-get install -y libprotobuf-dev 
sudo apt-get install -y graphicsmagick 
sudo apt-get install -y yasm 
sudo apt-get install -y stow
```
以上命令，建议从上至下逐个执行，方便查看安装提示

## 1. JDK(已安装忽略)
- [下载地址](http://www.oracle.com/technetwork/cn/java/javase/downloads/jdk8-downloads-2133151-zhs.html)
- 配置变量

## 2. 安装rethinkdb
[详细参考官方链接](https://www.rethinkdb.com/docs/install/debian/)

## 3.安装nodejs/npm
[详细参考官方链接](https://docs.npmjs.com/getting-started/installing-node)
- debian命令如下:
```
sudo apt install -y nodejs
sudo apt install -y npm
sudo apt install -y npm
```
- 更新node npm 到最新稳定版
```
npm install -g n
n stable
```

## 4.安装bower
客户端技术的软件包管理器，用于搜索、安装和卸载如JavaScript、HTML、CSS之类的网络资源
[详细参考官方链接](https://bower.io/)

## 5.安装ZeroMQ
[详细参考官方链接](http://zeromq.org/intro:get-the-software)

```
cd ~/Downloads 
wget http://download.zeromq.org/zeromq-4.2.3.tar.gz 
tar -zxvf zeromq-4.2.3.tar.gz
cd zeromq-4.2.3
sudo ./configure --without-libsodium --prefix=/usr/local/stow/zeromq-4.2.3
sudo make
sudo make install
cd /usr/local/stow
sudo stow -vv zeromq-4.2.3
```

## 6.安装 Google protobuf
```
sudo apt-get install build-essential
sudo apt-get install dh-autoreconf

cd ~/Downloads
git clone https://github.com/google/protobuf.git
cd protobuf
sudo ./autogen.sh
sudo ./configure --prefix=/usr/local/stow/protobuf-`git rev-parse --short HEAD`
sudo make
sudo make install
cd /usr/local/stow
sudo stow -vv protobuf-*
```

## 7. 更新library path
```
sudo ldconfig
```

## Installation
npm install -g stf

_ _ _
国内你也可以使用cnpm，[参考](http://npm.taobao.org/)

### 源码安装说明
如果使用npm 或 cnpm 安装失败，可以尝试用源码方式安装
#### 1. 下载stf源码
[源码地址](git@github.com:openstf/stf.git)
```
git clone git@github.com:openstf/stf.git
```

#### 2. 进入源码目录安装，命令如下
```
npm install 或者 cnpm install
```
#### 3. 安装后link全局方式
```
npm link 或者 cnpm link
```

## 验证安装是否成功
```
sft doctor
```
## 运行命令如下
```
1. rethinkdb & adb start-server
2. stf local --public-ip <ip address>
```
## 访问方式
```
http://<your_ip_address>:7100
```


# 第二种 docker安装方式
## 1.安装docker
[详见官方文档](https://docs.docker.com/engine/installation/linux/docker-ce/debian/)
## 2.拉去镜像
```
sudo docker pull openstf/stf:latest # STF镜像
sudo docker pull sorccu/adb:latest # android adb 镜像
sudo docker pull rethinkdb:latest # rethinkdb 镜像
sudo docker pull openstf/ambassador:latest
sudo docker pull nginx:latest # nginx 代理镜像
```
## 3.检查镜像
```
sudo docker images
```

## 4.启动镜像
```
- docker run -d --name rethinkdb -v /srv/rethinkdb:/data --net host rethinkdb rethinkdb --bind all --cache-size 8192 --http-port 8090
- docker run -d --name adbd --privileged -v /dev/bus/usb:/dev/bus/usb --net host sorccu/adb:latest
- docker run -d --name stf --net host openstf/stf stf local --public-ip <your-ip>

- sudo docker ps -a
```


* * *
网上文档如下：
https://my.oschina.net/u/2474096/blog/1359161