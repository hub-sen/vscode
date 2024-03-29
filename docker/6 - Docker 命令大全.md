# Docker 命令大全

1. [容器生命周期管理](#1.容器生命周期管理)

   - [run](#run)
   - [start/stop/restart](#start/stop/restart)
   - [kill](#kill)
   - [rm](#rm)
   - [pause/unpause](#pause/unpause)
   - [create](#create)
   - [exec](#exec)

2. [容器操作](#2.容器操作)

   - [ps](#ps)
   - [inspect](#inspect)
   - [top](#top)
   - [attach](#attach)
   - [events](#events)
   - [logs](#logs)
   - [wait](#wait)
   - [export](#export)
   - [port](#port)

3. [容器 rootfs 命令](#3.容器rootfs命令)

   - [commit](#commit)
   - [cp](#cp)
   - [diff](#diff)

4. [镜像仓库](#4.镜像仓库)

   - [login](#login)
   - [pull](#pull)
   - [push](#push)
   - [search](#search)

5. [本地镜像管理](#5.本地镜像管理)

   - [images](#images)
   - [rmi](#rmi)
   - [tag](#tag)
   - [build](#build)
   - [history](#history)
   - [save](#save)
   - [import](#import)

6. [info|version](#6.info|version)

   - [info](#info)
   - [version](#version)

---

## 1.容器生命周期管理

### run

docker run : 创建一个新的容器并运行一个命令

1. 语法

   ```docker
   docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
   ```

   OPTIONS 说明 :

   - -a stdin : 指定标准输入输出内容类型,可选 STDIN/STDOUT/STDERR 三项;

   - -d : 后台运行容器,并返回容器的 ID;

   - -i : 以交互模式运行容器，通常与 -t 同时使用；

   - -p : 端口映射，格式为：主机(宿主)端口:容器端口

   - -t : 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

   - --name="nginx-lb" : 为容器指定一个名称；

   - --dns 8.8.8.8 : 指定容器使用的 DNS 服务器，默认和宿主一致；

   - --dns-search example.com : 指定容器 DNS 搜索域名，默认和宿主一致；

   - -h "mars" : 指定容器的 hostname；

   - -e username="ritchie" : 设置环境变量；

   - --env-file=[] : 从指定文件读入环境变量；

   - --cpuset="0-2" or --cpuset="0,1,2" : 绑定容器到指定 CPU 运行；

   - -m : 设置容器使用内存最大值；

   - --net="bridge" : 指定容器的网络连接类型，支持 bridge/host/none/container: 四种类型；

   - --link=[] : 添加链接到另一个容器；

   - --expose=[] : 开放一个端口或一组端口

2. 实例

   使用 docker 镜像 nginx:latest 以后台模式启动一个容器,并将容器命名为 nginx。

   ```docker
   docker run --name nginx -d nginx:latest
   ```

   使用镜像 nginx:latest 以后台模式启动一个容器,并将容器的 80 端口映射到主机随机端口。

   ```docker
   docker run -P -d nginx:latest
   ```

   使用镜像 nginx:latest，以后台模式启动一个容器,将容器的 80 端口映射到主机的 80 端口,主机的目录 /data 映射到容器的 /data。

   ```docker
   docker run -p 80:80 -v /data:/data -d nginx:latest
   ```

   绑定容器的 8080 端口，并将其映射到本地主机 127.0.0.1 的 80 端口上。

   ```docker
   docker run -p 127.0.0.1:80:8080/tcp ubuntu bash
   ```

   使用镜像 nginx:latest 以交互模式启动一个容器,在容器内执行/bin/bash 命令。

   ```docker
   centos@centos:~$ docker run -it nginx:latest /bin/bash
   ```

---

### start/stop/restart

- docker start : 启动一个或多少已经被停止的容器

- docker stop : 停止一个运行中的容器

- docker restart : 重启容器

1. 语法

   ```docker
   docker start [OPTIONS] CONTAINER [CONTAINER...]

   docker stop [OPTIONS] CONTAINER [CONTAINER...]

   docker restart [OPTIONS] CONTAINER [CONTAINER...]
   ```

2. 实例

   启动已被停止的容器 centos

   ```docker
   docker start centos
   ```

   停止运行中的容器 centos

   ```docker
   docker stop centos
   ```

   重启容器 centos

   ```docker
   docker restart centos
   ```

---

### kill

docker kill : 杀掉一个运行中的容器。

1. 语法

   docker kill [OPTIONS] CONTAINER [CONTAINER...]
   OPTIONS 说明：

   - -s : 向容器发送一个信号

2. 实例

   杀掉运行中的容器 nginx

   ```docker
   centos@centos:~$ docker kill -s KILL nginx
   ```

---

### rm

docker rm ：删除一个或多少容器

1. 语法

   docker rm [OPTIONS] CONTAINER [CONTAINER...]
   OPTIONS 说明：

   - -f : 通过 SIGKILL 信号强制删除一个运行中的容器

   - -l : 移除容器间的网络连接，而非容器本身

   - -v : 删除与容器关联的卷

2. 实例

   强制删除容器 db01、db02

   ```docker
   docker rm -f db01 db02
   ```

   移除容器 nginx01 对容器 db01 的连接，连接名 db

   ```docker
   docker rm -l db
   ```

   删除容器 nginx01,并删除容器挂载的数据卷

   ```docker
   docker rm -v nginx01
   ```

---

### pause/unpause

docker pause : 暂停容器中所有的进程。

docker unpause : 恢复容器中所有的进程。

1. 语法

   ```docker
   docker pause [OPTIONS] CONTAINER [CONTAINER...]
   docker unpause [OPTIONS] CONTAINER [CONTAINER...]
   ```

2. 实例

   暂停数据库容器 db01 提供服务。

   ```docker
   docker pause db01
   ```

   恢复数据库容器 db01 提供服务。

   ```docker
   docker unpause db01
   ```

---

### create

docker create ：创建一个新的容器但不启动它

用法同 : [docker run](#run)

1. 语法

   ```docker
   docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
   ```

   语法同 : [docker run](#run)

2. 实例

   使用 docker 镜像 nginx:latest 创建一个容器,并将容器命名为 centos

   ```docker
   centos@centos:~$ docker create  --name centos  nginx:latest
   09b93464c2f75b7b69f83d56a9cfc23ceb50a48a9db7652ee4c27e3e2cb1961f
   ```

---

### exec

docker exec ：在运行的容器中执行命令

1. 语法

   ```docker
   docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
   ```

   OPTIONS 说明：

   - -d : 分离模式: 在后台运行

   - -i : 即使没有附加也保持 STDIN 打开

   - -t : 分配一个伪终端

2. 实例

   在容器 nginx 中以交互模式执行容器内/root/runoob.sh 脚本

   ```docker
   centos@centos:~$ docker exec -it nginx /bin/sh /root/runoob.sh
   http://www.runoob.com/
   ```

   在容器 nginx 中开启一个交互模式的终端

   ```docker
   centos@centos:~$ docker exec -i -t  nginx /bin/bash
   root@b1a0703e41e7:/#
   ```

---

## 2.容器操作

### ps

---

### inspect

---

### top

---

### attach

---

### events

---

### logs

---

### wait

---

### export

---

### port

---

## 3.容器 rootfs 命令

### commit

---

### cp

---

### diff

---

## 4.镜像仓库

### login

---

### pull

---

### push

---

### search

---

## 5.本地镜像管理

### images

---

### rmi

---

### tag

---

### build

---

### history

---

### save

---

### import

---

## 6.info|version

### info

---

### version
