# docker-面试题

# 一、基础问题

### 1、Docker 和虚拟机有啥不同？

Docker 是轻量级的沙盒，在其中运行的只是应用，虚拟机里面还有额外的系统。



### 2、Docker 安全么？

Docker 利用了 Linux 内核中很多安全特性来保证不同容器之间的隔离，并且通过签名机制来对镜像进行验证。大量生产环境的部署证明，Docker 虽然隔离性无法与虚拟机相比，但仍然具有极高的安全性。



### 3、如何清理后台停止的容器？

可以使用 sudo docker rm $sudo( docker ps -a -q) 命令。



### 4、如何查看镜像支持的环境变量？

可以使用 docker run IMAGE env 命令。



### 5、当启动容器的时候提示：exec format error？如何解决问题

检查启动命令是否有可执行权限，进入容器手工运行脚本进行排查。



### 6、本地的镜像文件都存放在哪里？

与 Docker 相关的本地资源都存放在/var/lib/docker/目录下，其中container目录存放容器信息，graph目录存放镜像信息，aufs目录下存放具体的内容文件。



### 7、如何退出一个镜像的bash，而不终止它？

按 Ctrl-p Ctrl-q。



### 8、退出容器时候自动删除?

使用 –rm 选项，例如 sudo docker run –rm -it ubuntu



### 9、怎么快速查看本地的镜像和容器？

可以通过docker images来快速查看本地镜像；通过docker ps -a快速查看本地容器。



### 10、Docker容器有几种状态？

四种状态：运行、已停止、重新启动、已退出。



### 11、DockerFile中最常见的指定是什么?

| 指令  | 备注                   |
| ----- | ---------------------- |
| FROM  | 指定基础镜像           |
| LABEL | 功能为镜像指定标签     |
| RUN   | 运行指定命令           |
| CMD   | 容器启动时要运行的命令 |

### 12、Docker的常用命令？

| 命令          | 备注                 |
| ------------- | -------------------- |
| docker pull   | 拉去或更新指定的镜像 |
| docker push   | 将镜像推送到远程仓库 |
| docker rm     | 删除容器             |
| docker rmi    | 删除镜像             |
| docker images | 列出所有镜像         |
| docker ps     | 列出所有容器         |

### 13、**什么是 DockerFile？**

Dockerfile 是一个文本文件，其中包含我们需要运行以构建 Docker 镜像的所有命令，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。Docker 使用 Dockerfile 中的指令自动构建镜像。我们可以 `docker build` 用来创建按顺序执行多个命令行指令的自动构建。

# 二、镜像相关问题

### 1、如何批量清理临时镜像文件？

可以使用sudo docker rmi $(sudo docker images -q -f danging=true)命令



### 2、如何查看镜像支持的环境变量？

使用sudo docker run IMAGE env



### 3、本地的镜像文件都存放在哪里

于Docker相关的本地资源存放在/var/lib/docker/目录下，其中container目录存放容器信息，graph目录存放镜像信息，aufs目录下存放具体的镜像底层文件。



### 4、构建Docker镜像应该遵循哪些原则？

- 整体远侧上，尽量保持镜像功能的明确和内容的精简，要点包括：
  尽量选取满足需求但较小的基础系统镜像
- 清理编译生成文件、安装包的缓存等临时文件
- 安装各个软件时候要指定准确的版本号，并避免引入不需要的依赖
- 从安全的角度考虑，应用尽量使用系统的库和依赖
- 使用Dockerfile创建镜像时候要添加.dockerignore文件或使用干净的工作目录



# 三、容器相关问题

### 1、容器退出后，通过docker ps 命令查看不到，数据会丢失么？

容器退出后会处于终止（exited）状态，此时可以通过 docker ps -a 查看，其中数据不会丢失，还可以通过docker start 来启动，只有删除容器才会清除数据。



### 2、如何停止所有正在运行的容器？

使用docker kill $(sudo docker ps -q)



### 3、如何清理批量后台停止的容器？

使用docker rm $（sudo docker ps -a -q）



### 4、如何临时退出一个正在交互的容器的终端，而不终止它？

按Ctrl+p，后按Ctrl+q，如果按Ctrl+c会使容器内的应用进程终止，进而会使容器终止。

### 5、很多应用容器都是默认后台运行的，怎么查看它们的输出和日志信息？

使用docker logs，后面跟容器的名称或者ID信息



### 6、使用docker port 命令映射容器的端口时，系统报错Error: No public port ‘80’ published for …，是什么意思？



创建镜像时Dockerfile要指定正确的EXPOSE的端口，容器启动时指定PublishAllport=true



### 7、可以在一个容器中同时运行多个应用进程吗？

一般不推荐在同一个容器内运行多个应用进程，如果有类似需求，可以通过额外的进程管理机制，比如supervisord来管理所运行的进程



### 8、如何控制容器占用系统资源（CPU，内存）的份额？

在使用docker create 命令创建容器或使用docker run 创建并运行容器的时候，可以使用-c|–cpu-shares[=0]参数来调整同期使用CPU的权重，使用-m|–memory参数来调整容器使用内存的大小。



# 四、仓库相关

### 1、仓库（Repository）、注册服务器（Registry）、注册索引（Index）有何关系？

首先，仓库是存放一组关联镜像的集合，比如同一个应用的不同版本的镜像，注册服务器是存放实际的镜像的地方，注册索引则负责维护用户的账号，权限，搜索，标签等管理。注册服务器利用注册索引来实现认证等管理。



# 五、配置相关

### 1、Docker的配置文件放在那里。如何修改配置？

Ubuntu系统下Docker的配置文件是/etc/default/docker，CentOS系统配置文件存放在/etc/sysconfig/docker



### 2、如何更改Docker的默认存储设置？

Docker的默认存放位置是/var/lib/docker,如果希望将Docker的本地文件存储到其他分区，可以使用Linux软连接的方式来做。



# 六、Docker 与虚拟化

### 1、Docker与LXC（Linux Container）有何不同？

LXC 利用Linux 上相关技术实现容器，Docker 则在如下的几个方面进行了改进：

- 移植性：通过抽象容器配置，容器可以实现一个平台移植到另一个平台；
- 镜像系统：基于AUFS的镜像系统为容器的分发带来了很多的便利，同时共同的镜像层只需要存储一份，实现高效率的存储；
-  版本管理：类似于GIT 的版本管理理念，用户可以更方面的创建、管理镜像文件；
- 仓库系统：仓库系统大大降低了镜像的分发和管理的成本；
- 周边工具：各种现有的工具（配置管理、云平台）对Docker的支持，以及基于Docker的Pass、CI等系统，让Docker的应用更加方便和多样化。



# 七、Other FAQ

### 1、Docker能在非Linux平台（Windows+MacOS）上运行吗？

可以



### 2、如何将一台宿主机的docker环境迁移到另外一台宿主机？

停止Docker服务，将整个docker存储文件复制到另外一台宿主机上，然后调整另外一台宿主机的配置即可。