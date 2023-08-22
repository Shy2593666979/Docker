# Docker详解

## 一、Docker简介

### 什么是容器 ？

- 一种虚拟化的方案
- 操作系统级别的虚拟化
- 只能运行相同或相似的内核操作系统
- 依赖于Linux内核特性：Namespace和Cgroups（Control Group）

### 容器技术有哪些优点 ？
![在这里插入图片描述](https://img-blog.csdnimg.cn/3af9793cc21d4618a45c06f53d6c143a.png)



从图中我们很容器看出，容器技术资源占用比较少，由于虚拟机需要模拟硬件的行为，对CUP和内存的损耗比较大。所以同样配置的服务器，容器技术就有以下优点：

- 资源占用比较少
- CPU/内存消耗低

那既然容器有这些优点，为什么直到Docker的出现，才真正的被关注呢？一个重要原因就是容器技术的复杂性。容器本身就很复杂，他依赖于Linux内核的很多特性，而且他不易安装，也不易于管理和实现自动化。而Docker就是为了改变这一切而产生的。

### 什么是Docker ？

- 将应用自动部署到容器的开源引擎
- Go语言实现的开源项目，诞生于2013年初，最初发起者是dotCloud公司

### Docker的特点

- **提供简单轻量的建模方式**：简单，Docker非常容器上手，用户只需要几分钟，就能把自己的项目Docker化。
- **职责的逻辑分离**：使用Docker，开发人员只需要关心容器中运行的程序，运维人员只需要关心如何管理容器；Docker设计的目的就是加强开发人员写代码的环境与应用程序要部署的生成环境的一致性。
- **快速高效的开发生命周期**：Docker的目标之一是缩短代码开发到测试到部署上线的运行周期，让应用程序具备可移植性，在容器中开发，以容器的形式交付和分发，这样开发、测试、生产，都使用相同的环境，这样也就避免了额外的调试和部署上的开销，这样就能有效的缩短产品的上线周期。
- **鼓励使用面向服务的架构**：Docker推荐单个容器只运行一个应用程序或者进程，这样就形成了一个分布式的应用程序模型，在这种模式下应用程序或服务都可以表述为一系列内部互联的容器，从而使分布式部署应用程序扩展或调试都变得非常简单。这就像我们开发中常用的思想；高内聚，低耦合，单一任务。这样就能避免在同一服务器上部署不同服务时，可能带来的服务之间相互影响。这样服务运行中出现问题时，也比较容易定位问题的所在。

### Docker的使用场景

- **使用Docker容器开发、测试、部署服务**：因为Docker本身非常轻量化，所以本地开发人员可以构建、运行并分享Docker容器。容器可以在开发环境中创建，然后再提交到测试，最终进入生产环境。
- **创建隔离的运行环境**：在很多企业应用中，同一服务的不同版本可能服务于不同的用户，那么使用Docker非常容易创建不同的生成环境来运行不同的服务。
- **搭建测试环境**：由于Docker的轻量化，所以开发者很容易利用Docker在本地搭建测试环境，用来测试程序在不用系统下的兼容性；甚至搭建集群的部署测试。
- **构建多用户的平台即服务（PaaS）基础设施**。
- **提供软件即服务（SaaS）应用程序**。
- **高性能、超大规模的宿主机部署**。

## 二、Docker的基本组成

Docker 包含了一下几个重要主要部分：

- Docker Client 客户端
- Docker Daemon 守护进程
- Docker Image 镜像
- Docker Container 容器
- Docker Registry 仓库

### Docker 客户端 / 守护进程

![在这里插入图片描述](https://img-blog.csdnimg.cn/aaee887c248341469ae46eaffd9c3375.png)


- Docker是C/S架构的程序：Docker客户端向Docker服务器端，也就是Docker的守护进程发出请求，守护进程处理完所有的请求工作并返回结果。
- Docker 客户端对服务器端的访问既可以是本地也可以通过远程来访问。

### Docker Image 镜像

- 镜像是Docker容器的基石，容器基于镜像启动和运行。镜像就好比容器的源代码，保存了用于启动容器的各种条件。
- Docker镜像是一个层叠的只读文件系统。
- Docker镜像使用联合加载技术

docker的镜像是一个层叠的只读文件系统，最低端是一个引导文件系统（即bootfs），第二层是root文件系统（即rootfs），它位于bootfs之上，可以是一种或多种操作系统，比如ubuntu或者centos。在docker中，root文件系统永远只能是只读状态，并且docker运用联合加载技术又会在root文件系统之上加载更多的只读文件系统，联合加载指的是一次加载多个文件系统，但是在外面看起来只能看到一个文件系统，联合加载会将各层文件系统叠加到一起，这样最终的文件系统会包含所有的底层文件和目录，docker将这样的文件系统称为镜像。

![在这里插入图片描述](https://img-blog.csdnimg.cn/0038b1107d034841bbefc9095613a819.png)


### Docker Container 容器

- 容器通过镜像来启动，Docker的容器是Docker的执行来源，容器中可以运行客户的一个或多个进程，如果说镜像是Docker声明周期中的构建和打包阶段，那么容器则是启动和执行阶段。

当一个容器启动时，docker会在该镜像的最顶层加载一个读写文件系统，也就是一个可写的文件层，我们在docker运行的程序，就是在这个层中进行执行的，当docker第一次启动一个容器时，初始的读写层是空的，当文件系统发生变化时，这些变化都会应用到这一层上，比如像修改一个文件，该文件首先会从读写层下面的只读层复制到该读写层，该文件的只读版本依然存在，但是已经被读写层中的该文件副本所隐藏，这就是docker的一个重要技术：写时复制（copy on write)。每个只读镜像层都是只读的，永远不会变化，当创建一个新容器时，docker会构建出一个镜像栈，如下图所示：

![在这里插入图片描述](https://img-blog.csdnimg.cn/5eea75c04a104a15817c9ca043f41cb5.png)


### Docker Registry 仓库

- docker用仓库来保存用户构建的镜像，仓库分为公有和私有两种,Docker公司提供了一个公有的仓库Docker Hub。

## 三、Docker 依赖的 Linux内核特性

Docker依赖于Linux内核的两个重要特性：

- Namespaces 命名空间
- Control groups (cgroups) 控制组

### Namespaces 命名空间

很多编程语言都包含了“命名空间”的概念，我们可以认为“命名空间”是一种“封装”的概念， 而“封装”本身实际上实现的是代码的隔离。而在操作系统中，命名空间提供的是系统资源的隔离，而系统资源包括了进程、网络、文件系统等。

我们从Docker公开的文档来看，它使用了5种命名空间：

- PID（Process ID） 进程隔离
- NET（Network）管理网络接口
- IPC（InterProcess Communication）管理跨进程通信的访问
- MNT（Mount）管理挂载点
- UTS（Unix Timesharing System） 隔离内核和版本标识

那么，这些隔离的资源，是如何被管理起来的呢？这就需要用到——Control groups(cgroup)控制组了。

### Control groups (cgroups) 控制组

Control groups是Linux内核提供的，一种可以限制、记录、隔离进程组所使用的物理资源的机制。 最初是由google工程师提出，并且在2007年时被Linux的内核的2.6.24版本引进。可以说，Control groups就是为容器而生的，没有Control groups就没有容器技术的今天。

Control groups提供了以下功能：

- **资源限制**：例如，memory(内存)子系统可以为进程组设定一个内存使用的上限，一旦进程组使用的内存达到了限额，该进程组再发出内存申请时，就会发出“out of memory”(内存溢出)的警告。
- **优先级设定**：它可以设定哪些进程组可以使用更大的CPU或者磁盘IO的资源。
- **资源计量**：它可以计算进程组使用了多少系统资源。尤其是在计费系统中，这一点十分重要。
- **资源控制**：它可以将进程组挂起或恢复。

### Namespace 和 cgroup带给Docker的能力

到这里我们了解了Namespace和CGroup的概念和职能，而这两个特性带给了Docker哪些能力呢？如下：

- **文件系统隔离**：首先是文件系统的隔离，每个Docker的容器，都可以拥有自己的root文件系统。
- **进程隔离**：每个容器都运行在自己的进程环境中。
- **网络隔离**：容器间的虚拟网络接口和IP地址都是分开的。
- **资源的隔离和分组**：使用cgroups将cpu和内存之类的资源独立分配给每个Docker容器。



## 四、Docker的安装

### Docker下载地址

Docker可以运行在MAC、Windows、CentOS、UBUNTU等操作系统上，本文档基于CentOS 7 安装 Docker。

官网：https://www.docker.com

### Linux系统shell命令下载Docker（推荐）

```shell
# 1、yum 包更新到最新 
yum update
# 2、安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的 
yum install -y yum-utils device-mapper-persistent-data lvm2
# 3、 设置yum源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 4、 安装docker，出现输入的界面都按 y 
yum install -y docker-ce
# 5、 查看docker版本，验证是否验证成功
docker -v
```



## 五、Docker命令



### 1、进程相关命令

- 启动docker服务: 

  ```shell
  systemctl start docker 
  ```

- 停止docker服务: 

  ```shell 
  systemctl stop docker 
  ```

- 重启docker服务: 

  ```shell
  systemctl restart docker 
  ```

- 查看docker服务状态: 

  ```shell
  systemctl status docker 
  ```

- 设置开机启动docker服务: 

  ```shell 
  systemctl enable docker
  ```

### 2、Docker 镜像相关命令

- 查看镜像: 查看本地所有的镜像

```shell
docker images
docker images –q # 查看所用镜像的id
```

- 搜索镜像:从网络中查找需要的镜像

```shell
docker search 镜像名称
```

- 拉取镜像:从Docker仓库下载镜像到本地，镜像名称格式为 名称:版本号，如果版本号不指定则是最新的版本。 如果不知道镜像版本，可以去docker hub 搜索对应镜像查看

```shell
docker pull 镜像名称
```

- 删除镜像: 删除本地镜像

```she
docker rmi 镜像id # 删除指定本地镜像
docker rmi `docker images -q` # 删除所有本地镜像
```

### 3、Docker 容器相关命令

- 查看容器

```shell
docker ps # 查看正在运行的容器
docker ps –a # 查看所有容器
```

- 创建并启动容器

```shell
docker run 参数
```

- 进入容器

```shell
docker exec 参数 # 退出容器，容器不会关闭
```

- 停止容器

```shell
docker stop 容器名称
```

- 启动容器

```shell
docker start 容器名称
```

- 删除容器：如果容器是运行状态则删除失败，需要停止容器才能删除

```shell
docker rm 容器名称
```

- 查看容器信息

```shell
docker inspect 容器名称
```



## 六、Docker容器的数据卷

### 数据卷概念

- 数据卷是宿主机中的一个目录或文件 

- 当容器目录和数据卷目录绑定后，对方的修改会立即同步 

- 一个数据卷可以被多个容器同时挂载 

- 一个容器也可以被挂载多个数据卷

### 配置数据卷

- 创建启动容器时，使用 –v 参数 设置数据卷

```shel
docker run ... –v 宿主机目录(文件):容器内目录(文件) ... 
```

- PS：

  1.目录必须是绝对路径 
  2. 如果目录不存在，会自动创建
  3. 可以挂载多个数据卷

### 配置数据卷容器

- 创建启动c3数据卷容器，使用 –v 参数 设置数据卷

```shell
docker run –it --name=c3 –v /volume centos:7 /bin/bash 
```

- 创建启动 c1 c2 容器，使用 –-volumes-from 参数 设置数据卷

```shell
docker run –it --name=c1 --volumes-from c3 centos:7 /bin/bash
docker run –it --name=c2 --volumes-from c3 centos:7 /bin/bash 
```

### 总结

1. 数据卷概念 
   - 宿主机的一个目录或文件 
2. 数据卷作用
   - 容器数据持久化 
   - 客户端和容器数据交换 
   - 容器间数据交换 
3. 数据卷容器 
   - 创建一个容器，挂载一个目录，让其他容器继承自该容器( --volume-from )。
   - 通过简单方式实现数据卷配置

## 七、Docker 应用部署

### 部署MySQL

**需求：**

​	在Docker容器中部署MySQL，并通过外部mysql客户端操作MySQL Server

**实现步骤：**

​	① 搜索mysql镜像 

​	② 拉取mysql镜像 

​	③ 创建容器 

​	④ 操作容器中的mysql

**MySQL部署：**

- 容器内的网络服务和外部机器不能直接通信
- 外部机器和宿主机可以直接通信
- 宿主机和容器可以直接通信
- 当容器中的网络服务需要被外部机器访问时，可以将容器中提供服务的端口映射到宿主机的端口上。外部机 器访问宿主机的该端口，从而间接访问容器的服务。
- 这种操作称为：端口映射




![在这里插入图片描述](https://img-blog.csdnimg.cn/25146194c3814945bff1583328b8abf5.png)

**Linux命令：(需要在root权限下)**

1. 搜索mysql镜像

```shell
docker search mysql
```

2. 拉取mysql镜像

```shell
docker pull mysql:5.6
```

3. 创建容器，设置端口映射、目录映射

```shell
# 在/root目录下创建mysql目录用于存储mysql数据信息
mkdir ~/mysql
cd ~/mysql
```

```shell
docker run -id \
-p 3307:3306 \
--name=c_mysql \
-v $PWD/conf:/etc/mysql/conf.d \
-v $PWD/logs:/logs \
-v $PWD/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.6
```

- 参数说明：
  - **-p 3307:3306**：将容器的 3306 端口映射到宿主机的 3307 端口。
  - **-v $PWD/conf:/etc/mysql/conf.d**：将主机当前目录下的 conf/my.cnf 挂载到容器的 /etc/mysql/my.cnf。配置目录
  - **-v $PWD/logs:/logs**：将主机当前目录下的 logs 目录挂载到容器的 /logs。日志目录
  - **-v $PWD/data:/var/lib/mysql** ：将主机当前目录下的data目录挂载到容器的 /var/lib/mysql 。数据目录
  - **-e MYSQL_ROOT_PASSWORD=123456：**初始化 root 用户的密码。



4. 进入容器，操作mysql

```shell
docker exec –it c_mysql /bin/bash
```

​	5.操作MySQL

```shell
mysql  -uroot -p123456

mysql> show database;
```




![在这里插入图片描述](https://img-blog.csdnimg.cn/08d07fd3ebb24252a6ba46264f8e80e8.png)

### 部署Tomcat

1. 搜索tomcat镜像

```shell
docker search tomcat
```

2. 拉取tomcat镜像

```shell
docker pull tomcat
```

3. 创建容器，设置端口映射、目录映射

```shell
# 在/root目录下创建tomcat目录用于存储tomcat数据信息
mkdir ~/tomcat
cd ~/tomcat
```

```shell
docker run -id --name=c_tomcat \
-p 8080:8080 \
-v $PWD:/usr/local/tomcat/webapps \
tomcat 
```

- 参数说明：

   **-p 8080:8080：** 将容器的8080端口映射到主机的8080端口

   **-v $PWD:/usr/local/tomcat/webapps：** 将主机中当前目录挂载到容器的webapps

### 部署Nginx

1. 搜索nginx镜像

```shell
docker search nginx
```

2. 拉取nginx镜像

```shell
docker pull nginx
```

3. 创建容器，设置端口映射、目录映射


```shell
# 在/root目录下创建nginx目录用于存储nginx数据信息
mkdir ~/nginx
cd ~/nginx
mkdir conf
cd conf
# 在~/nginx/conf/下创建nginx.conf文件,粘贴下面内容
vim nginx.conf
```

```shell
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;

    include /etc/nginx/conf.d/*.conf;
}


```




```shell
docker run -id --name=c_nginx \
-p 80:80 \
-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf \
-v $PWD/logs:/var/log/nginx \
-v $PWD/html:/usr/share/nginx/html \
nginx
```

- 参数说明：
  - **-p 80:80**：将容器的 80端口映射到宿主机的 80 端口。
  - **-v $PWD/conf/nginx.conf:/etc/nginx/nginx.conf**：将主机当前目录下的 /conf/nginx.conf 挂载到容器的 :/etc/nginx/nginx.conf。配置目录
  - **-v $PWD/logs:/var/log/nginx**：将主机当前目录下的 logs 目录挂载到容器的/var/log/nginx，日志目录。

### 部署Redis

1. 搜索redis镜像

```shell
docker search redis
```

2. 拉取redis镜像

```shell
docker pull redis:5.0
```

3. 创建容器，设置端口映射

```shell
docker run -id --name=c_redis -p 6379:6379 redis:5.0
```

4. 使用外部机器连接redis

```shell
./redis-cli.exe -h 192.168.149.135 -p 6379
```

## 八、Dockerfile

### Docker 镜像原理

- Docker镜像是由特殊的文件系统叠加而成
- 最底端是 bootfs，并使用宿主机的bootfs
- 第二层是 root文件系统rootfs,称为base image
- 然后再往上可以叠加其他的镜像文件
- 统一文件系统（Union File System）技术能够将不同的 层整合成一个文件系统，为这些层提供了一个统一的视角 ，这样就隐藏了多层的存在，在用户的角度看来，只存在 一个文件系统。 
- 一个镜像可以放在另一个镜像的上面。位于下面的镜像称 为父镜像，最底部的镜像成为基础镜像。
- 当从一个镜像启动容器时，Docker会在最顶层加载一个读 写文件系统作为容器

**问题：**

1. Docker 镜像本质是什么？ 

   • 是一个分层文件系统 

2. Docker 中一个centos镜像为什么只有200MB，而一个centos操作系统的iso文件要几个个G？ 

   • Centos的iso镜像文件包含bootfs和rootfs，而docker的centos镜像复用操作系统的bootfs，只有rootfs和其他镜像层 

3. Docker 中一个tomcat镜像为什么有500MB，而一个tomcat安装包只有70多MB？ 

   • 由于docker中镜像是分层的，tomcat虽然只有70多MB，但他需要依赖于父镜像和基础镜像，所有整个对外暴露的 tomcat镜像大小500多MB

### 镜像制作

容器转为镜像

```shell
docker commit 容器id 镜像名称:版本号
docker save -o 压缩文件名称 镜像名称:版本号
docker load –i 压缩文件名称
```

### Dockerfile 概念

- Dockerfile 是一个文本文件
- 包含了一条条的指令 
- 每一条指令构建一层，基于基础镜像，最终构建出一个新的镜像 
- 对于开发人员：可以为开发团队提供一个完全一致的开发环境
- 对于测试人员：可以直接拿开发时所构建的镜像或者通过Dockerfile文件 构建一个新的镜像开始工作了 
- 对于运维人员：在部署时，可以实现应用的无缝移植

Dochub网址：https://hub.docker.com



### Dockerfile关键字

| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于那个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来标明dockerfile的标签 可以使用Label代替Maintainer 最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令 默认是/bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和ENTRYPOINT配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中 不仅仅局限于当前build上下文 可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量 可以在启动的容器的时候 通过-e覆盖 格式ENV name=value |
| ARG         | 构建参数                 | 构建参数 只在构建的时候使用的参数 如果有ENV 那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定build的image那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用-p来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定/ 使用的是绝对地址 如果不是/开头那么是在上一条workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候 用户 在RUN CMD ENTRYPONT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令 基本上没用 因为很多时候 应用本身有健康监测机制 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候 当执行FROM完成之后 会执行 ONBUILD的命令 但是不影响当前镜像 用处也不怎么大 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN CMD ENTRYPOINT 执行命令的时候 使用的shell            |

## 九、Docker服务编排

### 服务编排

微服务架构的应用系统中一般包含若干个微服务，每个微服务一般都会部署多个实例，如果每个微服务都要手动启停 ，维护的工作量会很大。

- 要从Dockerfile build image 或者去dockerhub拉取image 
- 要创建多个container 
- 要管理这些container（启动停止删除）

### Docker Compose

Docker Compose是一个编排多容器分布式部署的工具，提供命令集管理容器化应用的完整开发周期，包括服务构建 ，启动和停止。使用步骤： 

1. 利用 Dockerfile 定义运行环境镜像 
2. 使用 docker-compose.yml 定义组成应用的各服务 
3. 运行 docker-compose up 启动应用

### Docker Compose安装使用

#### 1、安装Docker Compose

```shell
# Compose目前已经完全支持Linux、Mac OS和Windows，在我们安装Compose之前，需要先安装Docker。下面我 们以编译好的二进制包方式安装在Linux系统中。 
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
# 设置文件可执行权限 
chmod +x /usr/local/bin/docker-compose
# 查看版本信息 
docker-compose -version
```

#### 2、卸载Docker Compose

```shell
# 二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```

#### 3、 使用docker compose编排nginx+springboot项目

1. 创建docker-compose目录

```shell
mkdir ~/docker-compose
cd ~/docker-compose
```

2. 编写 docker-compose.yml 文件

```shell
version: '3'
services:
  nginx:
   image: nginx
   ports:
    - 80:80
   links:
    - app
   volumes:
    - ./nginx/conf.d:/etc/nginx/conf.d
  app:
    image: app
    expose:
      - "8080"
```

3. 创建./nginx/conf.d目录

```shell
mkdir -p ./nginx/conf.d
```

4. 在./nginx/conf.d目录下 编写itheima.conf文件

```shell
server {
    listen 80;
    access_log off;

    location / {
        proxy_pass http://app:8080;
    }
   
}
```

5. 在~/docker-compose 目录下 使用docker-compose 启动容器

```shell
docker-compose up
```

6. 测试访问

```shell
http://192.168.233.20/hello
```

## 十、Docker的私有仓库

### 1、私有仓库搭建

```shell
# 1、拉取私有仓库镜像 
docker pull registry
# 2、启动私有仓库容器 
docker run -id --name=registry -p 5000:5000 registry
# 3、打开浏览器 输入地址http://私有仓库服务器ip:5000/v2/_catalog，看到{"repositories":[]} 表示私有仓库 搭建成功
# 4、修改daemon.json   
vim /etc/docker/daemon.json    
# 在上述文件中添加一个key，保存退出。此步用于让 docker 信任私有仓库地址；注意将私有仓库服务器ip修改为自己私有仓库服务器真实ip 
{"insecure-registries":["私有仓库服务器ip:5000"]} 
# 5、重启docker 服务 
systemctl restart docker
docker start registry

```

### 2、将镜像上传至私有仓库

```shell
# 1、标记镜像为私有仓库的镜像     
docker tag centos:7 私有仓库服务器IP:5000/centos:7
 
# 2、上传标记的镜像     
docker push 私有仓库服务器IP:5000/centos:7
```

### 3、从私有仓库拉取镜像 

```shell
#拉取镜像 
docker pull 私有仓库服务器ip:5000/centos:7
```



**打开私人仓库只有{"repositories":[]} 表示私有仓库 搭建成功！**

![在这里插入图片描述](https://img-blog.csdnimg.cn/4520963eb9eb41b9b31cbbabf2b0a230.png)



**MySQL镜像推送私人仓库中~~~**

![在这里插入图片描述](https://img-blog.csdnimg.cn/1d271b61bc7946c7b034ea39e5dbe4be.png)

 	

**将MySQL的最新版本push到私人仓库后，私人仓库中就有mysql**

![(img-gN6ARTak-1692709651580)(C:\Users\枯木逢春i\Desktop\10.png)\]](https://img-blog.csdnimg.cn/0ee1a9f777b243cea5de9769ca5c38e1.png)


更多资料尽在 [GitHub](https://github.com/Shy2593666979)	 	欢迎各位读者Star
