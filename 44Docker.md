# 为什么使用docker

docker可以完成系统平滑移植，即容器虚拟化技术，可完成一次构建、随处运行

**容器与虚拟机不同，不需要捆绑一整套操作系统**，只需要软件工作所需的库资源和设置，可更快的完成应用部署

# 为什么Docker会比VM虚拟机快

1. **docker有着比虚拟机更少的抽象层，无需进行硬件虚拟化**

由于docker不需要Hypervisor(虚拟机)实现硬件资源虚拟化,运行在docker容器上的程序直接使用的都是实际物理机的硬件资源。因此在CPU、内存利用率上docker将会在效率上有明显优势。

2. **docker利用的是宿主机的内核,而不需要加载操作系统OS内核**

当新建一个容器时,docker不需要和虚拟机一样重新加载一个操作系统内核。进而避免引寻、加载操作系统内核返回等比较费时费资源的过程,当新建一个虚拟机时,虚拟机软件需要加载OS,返回新建过程是分钟级别的。而docker由于直接利用宿主机的操作系统,则省略了返回过程,因此新建一个docker容器只需要几秒钟。

# 镜像

镜像是一种轻量级、可执行的**独立软件包**，用来打包软件运行环境和基于运行环境开发的软件，它**包含运行某个软件所需的所有内容**，包括代码、运行时、库、环境变量和配置文件。

## 镜像原理

docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统 UnionFS

Linux 文件系统由 **bootfs** 和 **rootfs** 两部分组成

**bootfs（boot file system）**主要包含 **bootloader** 和 **kernel**，**bootloader** 主要是引导加载 **kernel**，**Linux** 刚启动时会加载 **bootfs** 文件系统，**在 Docker 镜像的最底层是 bootfs**

**rootfs (root file system)** ，在 bootfs 之上。包含的就是典型 Linux 系统中的 /dev，/proc，/bin，/etc 等标准目录和文件。rootfs 就是各种不同的操作系统发行版，比如 Ubuntu，Centos 等等

Docker中的镜像分层，**支持通过扩展现有镜像，创建新的镜像**。类似Java继承于一个Base基础类，自己再按需扩展。

新镜像是从 base 镜像一层一层叠加生成的。每安装一个软件，就在现有镜像的基础上增加一层。

# 常用命令

## 镜像命令

### 镜像本机iamges

查看本机中所有镜像命令格式：`docker images [options] [镜像名]`

```sh
# 列出所有本地镜像格式
docker images [options] [镜像名]

# 列出所有镜像（包含中间映像层）
docker iamges -a

# 只显示出镜像 id
docker iamges -q

# 专门查询某个镜像
docker images <镜像名>
```

### **搜索镜像**search

去 Docker Hub 上查询镜像命令格式：`docker search [options] <镜像名>[:TAG]`

```sh
# 查询指定的镜像格式
docker search [options] <镜像名>[:TAG]

# 列出收藏数不少于指定值的镜像
docker search -s <收藏数/指定值> <镜像名>

# 显示完整的镜像信息
docker search --no-trunc <镜像名>
```

### 镜像下载pull

从远程仓库下载镜像命令格式：`docker pull <镜像名>[:TAG | @DIGEST]`

- TAG：版本号、标签
- DIGEST：摘要

推荐通过「版本号」下载镜像，如果不指定版本，默认最新版 `latest`

```sh
# 下载镜像格式
docker pull <镜像名>[:TAG | @DIGEST]

# 通过「版本号」下载镜像
docker pull <镜像名:TAG>

# 通过「摘要」下载镜像
docker pull <镜像名:@DIGEST>
```

### 查看镜像/容器/数据卷所占的空间 system df

```sh
[root@master ~]# docker system df
```

### 镜像删除image rm

在本地仓库删除镜像命令格式：

- 完整：`docker image rm [options] <镜像名>[:TAG | IMAGE ID]`
- 简写： `docker rmi [options] <镜像名>[:TAG | IMAGE ID]`

`i` 指的是 image

```sh
# 删除镜像 完整格式
docker image rm [options] <镜像名>[:TAG | IMAGE ID]

# 删除镜像 简写格式
docker rmi [options] <镜像名>[:TAG | IMAGE ID]

# 强制删除镜像
docker image rm -f <镜像名>
docker rmi -f <镜像名>

# 通过「版本号」删除镜像
docker rmi <镜像名>:TAG

# 通过「镜像 id」删除镜像
docker rmi <镜像名>:IMAGE ID
```

### 虚悬镜像是什么？

仓库名、标签都是`<none>`的镜像，被称为虚悬镜像。

### 镜像命名tag

如果你觉得下载的镜像名或者镜像 TAG 太长，可以进行重命名，重命名之后两个文件都将存在

我们可以手动新增镜像的版本，也就是设置 TAG，并改名，格式为：`docker tag <ID> | <镜像名:原来 TAG> <镜像名>:<新的 TAG>`

```sh
# 手动新增镜像的 TAG
docker tag <ID> | <镜像名:原来 TAG> <镜像名>:<新的 TAG>
```

### 镜像打包save

利用 `save` 可以打包镜像，格式有两个，分别为：

- `docker save > <名称.tar> <镜像 ID>`
- `docker save <镜像名>[:TAG | ID] -o <名称.tar>`

```sh
docker save > <名称.tar> <镜像 ID>
# 或者
docker save <镜像名>[:TAG | ID] -o <名称.tar>
```

### 镜像载入load

利用 `load` 可以导入镜像，格式为：`docker load -i <名称.tar>`

```sh
docker load -i <名称.tar>
```

## 容器命令

切记

容器的唯一标识是 id 和 names（名字）。（大部分情况下）能以 id 操作容器，则也能以 name 操作容器，反之亦然。

内容例子可能只写一个标识如 id，忽略另一个，但请记住，两者使用任意一个标识都可以。

### 容器启动run

Docker 启动一个容器，这个容器与操作系统是隔离的

```
docker run [options] <镜像名[:tag | 镜像 id]>
			-p       # 指定端口号，将容器的端口和操作系统端口进行绑定
			-d       # 启动守护式容器，在后台启动容器
			--name   # 为容器起一个别名
			-it      # 打开窗口以交互模式启动容器，进入容器进程内容会有讲解
			--restart=always     # 固定格式，容器一旦关闭就会自启动，一般用于经常发生意外而宕机的容器
			--rm     # 容器启动成功并进入容器，再退出来后，容器自动停止并删除，一般在测试情况下使用
			--privileged	#容器内是否使用真正的 root 权限（实体机的root权限）
            --volumes-from	#容器2继承容器1的卷规则
            --name u2 		#容器名字
```

### 容器查看ps

容器查看的格式：`docker ps [options]`

**options 常用参数说明：**

| 参数 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| -a   | 表示 all，所有的，列出当前所有正在运行的容器 + 历史运行过的容器 |
| -l   | 显示最近创建的容器                                           |
| -n=? | 显示最近n个创建的容器                                        |
| -q   | 静默模式，返回正在运行的容器 id。                            |

### 容器退出exit

| 指令     | 说明           |
| -------- | -------------- |
| exit     | 容器停止退出   |
| ctrl+P+Q | 容器不停止退出 |

 ### 容器再启动start

容器再启动命令格式：`docker start <容器 id | 容器名>`

```sh
docker start <容器 id | 容器名>
```

### 容器停止和重启stop/kill||restart

容器正常停止命令格式：`docker stop <容器 id |	容器名>`

容器立即停止命令格式：`docker kill <容器 id | 容器名>`

容器重启命令格式：`docker restart <容器 id | 容器名>`

### 容器删除和清除rm

如果有挂着本地目录的话必须要手动将本地目录删除

容器删除命令格式：`docker rm [options] <容器 id | 容器名>`

容器清除命令格式：`docker container prune`

```sh
# 删除命令格式
docker rm [options] <容器 id | 容器名>

# 强制删除
docker rm -f <容器 id | 容器名>
```

### 查看容器进程top

查看容器进程命令格式：`docker top <容器 id | 容器名>`

```sh
docker top <容器 id | 容器名>
```

 ### 查看容器细节inspect

这个命令还是很常用的，要求容器必须运行起来。

查看容器内部细节命令格式：`docker inspect <容器 id | 容器名>`

```sh
docker inspect <容器 id | 容器名>

```

### 进入容器内部

```
docker exec -it <容器 id | 容器名> /bin/bash
```

### 容器开机自启

```
docker update mysql --restart=always
```

## 上传和下载命令

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting1@master/20220914/image.nzg3lx1r7y8.webp)

### 容器===》镜像

docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名]

例如：

```sh
docker commit -m="add vim cmd" -a="wuhx" e4a6abf80ca5 frx01/myubuntu:1.3
```

### 阿里云docker《===》镜像

- 从Registry中拉取镜像

```java
docker pull registry.cn-hangzhou.aliyuncs.com/outmanpro/test:[镜像版本号]
```

-  将镜像推送到Registry

```java
docker login --username=outmanpro registry.cn-hangzhou.aliyuncs.com
docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/outmanpro/test:[镜像版本号]
docker push registry.cn-hangzhou.aliyuncs.com/outmanpro/test:[镜像版本号]
```

### 私有docker《===》镜像

- 运行私有库Registry，相当于本地有个私有库Docker Hub

```sh
docker run -d -p 5000:5000  -v /zzyyuse/myregistry/:/tmp/registry --privileged=true registry
```

- 将新镜像zzyyubuntu:1.2修改符合私服规范的Tag

按照公式： docker tag 镜像:Tag Host:Port/Repository:Tag

例如：使用命令 docker tag 将zzyyubuntu:1.2 这个镜像修改为192.168.91.166:5000/zzyyubuntu:1.2

docker tag zzyyubuntu:1.2 192.168.91.166:5000/zzyyubuntu:1.2

- 从Registry中拉取镜像

```sh
docker pull 192.168.91.166:5000/zzyyubuntu:1.2
```

-  将镜像推送到Registry

```sh
docker push 192.168.91.166:5000/zzyyubuntu:1.2
```

## 数据卷命令

**数据卷** 是一个可供一个或多个容器使用的特殊目录，数据卷可用于将容器运行的数据持久化

### 数据卷挂载操作

三种挂载数据卷格式

- 具体目录挂载：`docker run [options] -v <宿主机绝对路径:容器内的路径[:ro | rw]> <镜像名>`
- 默认目录挂载：`docker run [options] -v <任意别名:容器内的路径[:ro | rw]> <镜像名>`
- 匿名目录挂载：`docker run [options] -v <容器内的路径[:ro | rw]> <镜像名>`

默认目录挂载和匿名目录挂载的目录默认在 `/var/lib/docker/volumes/` 目录下

匿名目录挂载生成的目录名就是 **随机生成的数据卷名字**。

`ro`：代表 read-only，容器的路径只允许读，不允许写。不影响宿主机的路径可读可写

`rw`：默认值，代表可读可写

### 数据卷查看

查看数据卷的命令已经在上面透露过了。

格式：`docker volume ls

### 数据卷信息

格式：`docker volume inspect <数据卷名>`

### 数据卷创建

如果不想在启动容器的时候利用 `-v`「被迫」创建数据卷，这个命令让你收益实用。

格式：`docker volumn create <数据卷名>`

### 数据卷删除

数据卷太多怎么办？可以删除指定名字的数据卷，也可以删除全部未被使用的数据卷。

格式：`docker rm <数据卷名>`

### 数据卷继承

格式：`docker run --volumes-from <数据卷容器名> <镜像名[:TAG | ID]>`

数据卷继承可用于不同容器之间的数据共享

使用 --volumes-from 参数所挂载数据卷的容器自己并不需要保持在运行状态。普通容器绑定数据卷容器，其实就是绑定数据卷容器的数据卷。