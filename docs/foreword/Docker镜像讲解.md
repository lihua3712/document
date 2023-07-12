

# Docker数据卷



## 什么是容器卷技术

docker理念：将应用和环境打包成一个镜像！

数据？如果数据都在容器中，那么我们容器删除，数据就会丢失！需求：数据可以持久化

MySQL,容器删了，需求：Mysql数据可以存储在本地

容器之间可以有一个数据共享的技术，Docker容器中产生的数据，同步到本地！

这就是数据卷！目录的挂载，将我们容器内的目录挂载到Linux上面！

## 使用数据卷

```
方式一：直接使用命令来挂载 -v
```

```shell
测试
[root@node128 test]# docker run -it -v /home/test:/home centos /bin/bash

#启动起来的时候可以通过 docker inspect 容器id
```

![1634017966803](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634017966803.png)

## 安装mysql

mysql数据持久化问题！

```shell
#获取镜像
# docker pull mysql:5.7

#运行容器，需要做数据挂载！
#安装启动mysql,需要配置密码的，注意点!

#官方测试
$ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

$ docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
  
#测试连接成功  
```

## 具名挂载和匿名挂载

```shell
#匿名挂载
-v  容器内路径！
docker run -d -P --name nginx01 -v /etc/nginx nginx

#查看所有的volume 的情况
$ docker volume ls
[root@node128 /]# docker volume ls
DRIVER    VOLUME NAME
local     3d62d5ad933633bef00825655eea43a353efc7cab0b3f0c8a7a97aae011ec140
local     7b859c4e385c17630595b8cbbb835d572b2c0509570eae30a52e28178319d61b

#这里发现，这种就是匿名挂载，在-v 后面只写了容器内的路径，没有写容器外的路径


#具名挂载
$ docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx

$ docker volume ls

# 通过-v 卷名：容器内的路径
# 查看一些这个卷
```

![1634022565521](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634022565521.png)

所有docker容器中的卷，在没有指名目录的情况下都是在/var/lib/docker/XXX/_data

通过具名挂载可以方便的找到一个卷的位置，大多数情况下使用：具名挂载

```shell
-v                         #匿名挂载
-v 卷名：容器内路径           #具名挂载
-v  /宿主机路径：/容器内路径   #指定路径挂载
```

拓展：

```shell
#通过-v 容器内路径：ro rw 改变读写权限
ro readonly    只读
rw readwrite   #可读可写

#设置了容器挂载权限，就对挂载的内容有了一定的限定
$ docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
$ docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx

#ro 只要看到ro 说明这个路径只能通过宿主机来操作，容器内部是无法操作的！
```

# DockerFile

## 初始Dockerfile

Dockerfile就是用来构建docker镜像的构建文件！命令脚本

通过这个脚本可以生成镜像，镜像是一层一层的，脚本一个个的命令，每个命令都是一层

```shell
#创建一个Dockerfile 文件 ，名字可以随机 建议Dockerfile
#文件中的内容 指令（大写）参数

FROM centos

VOLUME ["volume01","volume02"]

CMD echo "----------end----------"

CMD /bin/bash

#这里每个命令就是镜像的每一层

$ docker build -f /home/docker-test-volume/dockerfile -t lihua/centos:1.0 .
```

![1634024637820](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634024637820.png)

![1634024794918](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634024794918.png)

![1634025150972](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634025150972.png)

## 数据卷容器

```shell
#共享容器卷   --volumes-from
  例：
      docker02 --volumes-from docker01  #容器02共享容器01的数据，继承01
$ docker run -it --name docker02 --volumes-from d0b01d732f6a lihua/centos:1.0
```

## 进阶DockerFile

### DockerFile介绍

dockerfile是用来构建docker镜像文件的！命令参数脚本！

构建步骤： 

1.编写一个dockerfile文件

2.docker build 构建成为一个镜像

3.docker run 运行镜像

4.docker push 发布镜像（DockerHub 、阿里云镜像参考！）

查看官方做法 :

 ![img](https://img2020.cnblogs.com/blog/1869289/202005/1869289-20200529090831434-1733190457.png) 

 很多官方镜像都是基础包，很多功能没有，我们通常会自己搭建自己的镜像！ 

### DockerFile构建过程

**基础知识**

1.每个保留关键字（指令）都必须是大写字母

2.执行从上到下顺序执行

3.#表示注释

4.每个指令都会创建提交一个新的镜像层，并提交！

![1634103003868](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634103003868.png)

 步骤：开发，部署，运维 

dockerfile是面向开发的，我们以后要发布项目，做镜像，就需要编写dockerfile文件，这个文件十分简单！

Docker 镜像逐渐成为企业交付标准，

DockerFile：构建文件，定义了一切的步骤，源代码

DockerImages：通过DockerFile构建生成镜像，最终发布和运行的产品！

Docker容器：容器就是镜像运行起来提供服务器

### DockerFile指令

 常用指令 

```shell
FROM			# 基础镜像，一切从这里开始构建
MANTAINER		# 镜像是谁写的，姓名+邮箱
RUN				# 镜像构建的时候需要运行的命令
ADD				# 添加内容，如添加一个tomcat的压缩包
WORKDIR			# 镜像的工作目录
VOLUME			# 挂载的目录
EXPOE			# 暴露端口的配置
CMD				# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代！
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令，可以追加命令！
ONBUILD			# 当构建一个被继承 DockerFile 这个时候就会运行 ONBUILD 的指令，触发指令！
COPY			# 类似ADD ，将文件拷贝到镜像中！
ENV				# 构建的时候设置环境变量！
```

![1634103428593](C:\Users\11749\AppData\Roaming\Typora\typora-user-images\1634103428593.png)

### 实战测试

Docker Hub中 99% 的镜像都是从 FROM scratch 这个基础镜像过来的！然后配置需要的软件和配置来进行构建！

> 创建一个自己的centos

**在home目录下创建一个文件夹**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz home]# mkdir dockerfile
[root@iz2zeaet7s13lfkc8r3e2kz home]# ls
ceshi  dockerfile  docker-test-volume  mysql  www  ysl
```

**在创建的文件夹中创建dockerfile文件,并编写**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# vim mydockerfile-centos            
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# cat mydockerfile-centos 
FROM centos
MAINTAINER lh<1174934657@qq.com>

WORKDIR /usr/local

RUN yum -y install vim
RUN yum -y install net-tools

EXPOSE 80

CMD echo "---end---"
CMD /bin/bash
```

**通过这个文件构建镜像**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .
Sending build context to Docker daemon  2.048kB
Step 1/8 : FROM centos
 ---> 470671670cac
Step 2/8 : MAINTAINER ysl<493487822@qq.com>
 ---> Running in 2ce4c938a188
Removing intermediate container 2ce4c938a188
 ---> fd5e9920907d
Step 3/8 : WORKDIR /usr/local
 ---> Running in 98327d3050d5
Removing intermediate container 98327d3050d5
 ---> 167672c4b746
Step 4/8 : RUN yum -y install vim
 ---> Running in 7db13d6a1a95
CentOS-8 - AppStream                            1.9 MB/s | 7.0 MB     00:03    
CentOS-8 - Base                                 1.1 MB/s | 2.2 MB     00:02    
CentOS-8 - Extras                               7.1 kB/s | 5.9 kB     00:00    
Dependencies resolved.
================================================================================
 Package             Arch        Version                   Repository      Size
================================================================================
Installing:
 vim-enhanced        x86_64      2:8.0.1763-13.el8         AppStream      1.4 M
Installing dependencies:
 gpm-libs            x86_64      1.20.7-15.el8             AppStream       39 k
 vim-common          x86_64      2:8.0.1763-13.el8         AppStream      6.3 M
 vim-filesystem      noarch      2:8.0.1763-13.el8         AppStream       48 k
 which               x86_64      2.21-10.el8               BaseOS          49 k

Transaction Summary
================================================================================
Install  5 Packages

Total download size: 7.8 M
Installed size: 31 M
Downloading Packages:
(1/5): gpm-libs-1.20.7-15.el8.x86_64.rpm        285 kB/s |  39 kB     00:00    
(2/5): vim-filesystem-8.0.1763-13.el8.noarch.rp 813 kB/s |  48 kB     00:00    
(3/5): which-2.21-10.el8.x86_64.rpm             294 kB/s |  49 kB     00:00    
(4/5): vim-enhanced-8.0.1763-13.el8.x86_64.rpm  1.6 MB/s | 1.4 MB     00:00    
(5/5): vim-common-8.0.1763-13.el8.x86_64.rpm    2.1 MB/s | 6.3 MB     00:03    
--------------------------------------------------------------------------------
Total                                           1.3 MB/s | 7.8 MB     00:05     
warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS-8 - AppStream                            1.6 MB/s | 1.6 kB     00:00    
Importing GPG key 0x8483C65D:
 Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
 Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : which-2.21-10.el8.x86_64                               1/5 
  Installing       : vim-filesystem-2:8.0.1763-13.el8.noarch                2/5 
  Installing       : vim-common-2:8.0.1763-13.el8.x86_64                    3/5 
  Installing       : gpm-libs-1.20.7-15.el8.x86_64                          4/5 
  Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                          4/5 
  Installing       : vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5 
  Running scriptlet: vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5 
  Running scriptlet: vim-common-2:8.0.1763-13.el8.x86_64                    5/5 
  Verifying        : gpm-libs-1.20.7-15.el8.x86_64                          1/5 
  Verifying        : vim-common-2:8.0.1763-13.el8.x86_64                    2/5 
  Verifying        : vim-enhanced-2:8.0.1763-13.el8.x86_64                  3/5 
  Verifying        : vim-filesystem-2:8.0.1763-13.el8.noarch                4/5 
  Verifying        : which-2.21-10.el8.x86_64                               5/5 

Installed:
  vim-enhanced-2:8.0.1763-13.el8.x86_64 gpm-libs-1.20.7-15.el8.x86_64          
  vim-common-2:8.0.1763-13.el8.x86_64   vim-filesystem-2:8.0.1763-13.el8.noarch
  which-2.21-10.el8.x86_64             

Complete!
Removing intermediate container 7db13d6a1a95
 ---> 46d4742d96c6
Step 5/8 : RUN yum -y install net-tools
 ---> Running in 3a8b3b61455f
Last metadata expiration check: 0:00:14 ago on Tue May 26 02:28:01 2020.
Dependencies resolved.
================================================================================
 Package         Architecture Version                        Repository    Size
================================================================================
Installing:
 net-tools       x86_64       2.0-0.51.20160912git.el8       BaseOS       323 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 323 k
Installed size: 1.0 M
Downloading Packages:
net-tools-2.0-0.51.20160912git.el8.x86_64.rpm   2.0 MB/s | 323 kB     00:00    
--------------------------------------------------------------------------------
Total                                           211 kB/s | 323 kB     00:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1 
  Installing       : net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 
  Running scriptlet: net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 
  Verifying        : net-tools-2.0-0.51.20160912git.el8.x86_64              1/1 

Installed:
  net-tools-2.0-0.51.20160912git.el8.x86_64                                     

Complete!
Removing intermediate container 3a8b3b61455f
 ---> 0a0a8b5370f2
Step 6/8 : EXPOSE 80
 ---> Running in e136862cbcf1
Removing intermediate container e136862cbcf1
 ---> 28b29770c09a
Step 7/8 : CMD echo "---end---"
 ---> Running in db998e281478
Removing intermediate container db998e281478
 ---> e7090ba90a77
Step 8/8 : CMD /bin/bash
 ---> Running in 291bec834681
Removing intermediate container 291bec834681
 ---> a68a7a44878e
Successfully built a68a7a44878e
Successfully tagged mycentos:0.1
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
mycentos            0.1                 a68a7a44878e        About a minute ago   321MB
ysl/centos          1.0                 bf97aef123f3        23 hours ago         237MB
mysql               5.7                 a4fdfd462add        4 days ago           448MB
nginx               latest              9beeba249f3e        10 days ago          127MB
centos              latest              470671670cac        4 months ago         237MB
```

**运行**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run -it mycentos:0.1
[root@49326d559289 local]# pwd
/usr/local
```

  **官方的工作目录是 / ，不能使用vim，和ifconfig等命令！我自己的镜像安装了vim，和ifconfig！测试能否使用** 

 ![img](https://img2020.cnblogs.com/blog/1869289/202005/1869289-20200529090721372-506000564.png) 

 **测试官方的centos** 

 ![img](https://img2020.cnblogs.com/blog/1869289/202005/1869289-20200529090657916-1629168601.png) 

 列出本地进行的变更历史 `docker history 镜像id` 

![img](https://img2020.cnblogs.com/blog/1869289/202005/1869289-20200529090627149-1180673563.png) 

 CMD 和 ENTRYPOINT 的区别 

```shell
CMD				# 指定这个容器启动的时候要运行的命令，只有最后一个会生效，可被替代！
ENTRYPOINT		# 指定这个容器启动的时候要运行的命令，可以追加命令！
```

**测试CMD**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# vim dockerfile-cmd-test
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# cat dockerfile-cmd-test 
FROM centos
CMD ["ls","-a"]

# 构建
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker build -f dockerfile-cmd-test -t cmdtest .
Sending build context to Docker daemon  3.072kB
Step 1/2 : FROM centos
 ---> 470671670cac
Step 2/2 : CMD ["ls","-a"]
 ---> Running in 08576b2fe689
Removing intermediate container 08576b2fe689
 ---> 380b0c95615c
Successfully built 380b0c95615c
Successfully tagged cmdtest:latest

# 执行
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run 380b0c95615c
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# 想追加一个命令 -l 期望的是 ls -al 
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run 380b0c95615c -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.
# CMD的情况下 -l 替换了  CMD ["ls","-a"] 命令， -l 不是一个命令所以报错

# CMD正确应该
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run 380b0c95615c ls -al
total 56
drwxr-xr-x   1 root root 4096 May 26 03:04 .
drwxr-xr-x   1 root root 4096 May 26 03:04 ..
-rwxr-xr-x   1 root root    0 May 26 03:04 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 May 26 03:04 dev
drwxr-xr-x   1 root root 4096 May 26 03:04 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jan 13 21:48 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 111 root root    0 May 26 03:04 proc
dr-xr-x---   2 root root 4096 Jan 13 21:49 root
drwxr-xr-x  11 root root 4096 Jan 13 21:49 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 May  9 09:17 sys
drwxrwxrwt   7 root root 4096 Jan 13 21:49 tmp
drwxr-xr-x  12 root root 4096 Jan 13 21:49 usr
drwxr-xr-x  20 root root 4096 Jan 13 21:49 var
```

**测试ENTRYPOINT**

```shell
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# vim dockerfile-entrypoint-test
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# cat dockerfile-entrypoint-test 
FROM centos
ENTRYPOINT ["ls","-a"]

#构建
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker build -f dockerfile-entrypoint-test -t entrypoint-test .
Sending build context to Docker daemon  4.096kB
Step 1/2 : FROM centos
 ---> 470671670cac
Step 2/2 : ENTRYPOINT ["ls","-a"]
 ---> Running in bf9f861823b8
Removing intermediate container bf9f861823b8
 ---> cd452c66c026
Successfully built cd452c66c026
Successfully tagged entrypoint-test:latest


# 执行
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run cd452c66c026
.
..
.dockerenv
bin
dev
etc
home
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

# 追加一个命令 -l
[root@iz2zeaet7s13lfkc8r3e2kz dockerfile]# docker run cd452c66c026 -l
total 56
drwxr-xr-x   1 root root 4096 May 26 03:12 .
drwxr-xr-x   1 root root 4096 May 26 03:12 ..
-rwxr-xr-x   1 root root    0 May 26 03:12 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  340 May 26 03:12 dev
drwxr-xr-x   1 root root 4096 May 26 03:12 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Jan 13 21:48 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 112 root root    0 May 26 03:12 proc
dr-xr-x---   2 root root 4096 Jan 13 21:49 root
drwxr-xr-x  11 root root 4096 Jan 13 21:49 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  13 root root    0 May  9 09:17 sys
drwxrwxrwt   7 root root 4096 Jan 13 21:49 tmp
drwxr-xr-x  12 root root 4096 Jan 13 21:49 usr
drwxr-xr-x  20 root root 4096 Jan 13 21:49 var
```

### tomcat镜像制作-测试

#### 准备

需要先下载 tomcat9 及jdk8

```shell
tomcat 下载 
wget -P ./ https://mirrors.cnnic.cn/apache/tomcat/tomcat-8/v8.5.64/bin/apache-tomcat-8.5.64.tar.gz 
jdk 下载地址
wget -P ./ https://mirrors.cnnic.cn/AdoptOpenJDK/8/jdk/x64/linux/OpenJDK8U-jdk_x64_linux_hotspot_8u282b08.tar.gz
===== 以上两个地址与我使用的版本不一样，勿抄 

root@aliyunleo tomcat8source]# ll
total 191952
-rw-r--r-- 1 root root  11027411 Oct 15 11:44 apache-tomcat-9.0.33.tar.gz
-rw-r--r-- 1 root root 185516505 Oct 15 11:44 jdk-8u141-linux-x64.tar.gz

```

#### step 1 编写Dockerfile 脚本

```shell


[root@aliyunleo tomcat8source]# cat Dockerfile 
FROM centos
MAINTAINER leo<wei1986@126.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u141-linux-x64.tar.gz /usr/local/
ADD apache-tomcat-9.0.33.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local
WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_141
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.33
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.33

ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.33/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.33/bin/logs/catalina.out


在这里插入代码片

```

#### step 2  执行 docker build 指令 生成镜像

```shell


[root@aliyunleo tomcat8source]# pwd
/home/dockertest/tomcat8source
[root@aliyunleo tomcat8source]# ll
 apache-tomcat-9.0.33.tar.gz
 Dockerfile
 jdk-8u141-linux-x64.tar.gz


[root@aliyunleo tomcat8source]# docker build -f Dockerfile -t diytomcat:1.1 .


--------------看到已经有镜像了------
[root@aliyunleo ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
diytomcat           1.1              ce5fea56a27e        19 minutes ago      659MB
centos_entrypoint   latest              885b7ce9f700        About an hour ago   209MB


在这里插入代码片

```

#### step3 启动镜像 docker run

```shell



docker run -d -p 9090:8080 --name leotomcat -v /wwwroot/tomcat9/test:/usr/local/apache-tomcat-9.0.33/webapps/test -v  /wwwroot/tomcat9/logs:/usr/local/apache-tomcat-9.0.33/logs diytomcat:1.1

----------------------------下面为解释------------------------------
docker run -d 
-p 9090:8080          ----外网9090映射内网8080
--name leotomcat      ---- name  
-v /wwwroot/tomcat9/test:/usr/local/apache-tomcat-9.0.33/webapps/test    ---- 本地路径  /wwwroot/tomcat9/test   挂载到容器的   /usr/local/apache-tomcat-9.0.33/webapps/test
-v  /wwwroot/tomcat9/logs:/usr/local/apache-tomcat-9.0.33/logs     ---- 本地路径  /wwwroot/tomcat9/logs   挂载到容器的   /usr/local/apache-tomcat-9.0.33/logs
diytomcat             ---- 镜像名称 

---- 考到已经有容器启动了
[root@aliyunleo ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
12d332ce5431        diytomcat           "/bin/sh -c '/usr/lo…"   2 minutes ago       Up 2 minutes        0.0.0.0:9090->8080/tcp   leotomcat

在这里插入代码片

```

 ![img](https://img-blog.csdnimg.cn/20210312153204994.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaTE5ODYyMQ==,size_16,color_FFFFFF,t_70) 

#### step4 进入镜像 docker exec

```shell
[root@aliyunleo tomcat9]# docker exec -it 12d332ce5431 /bin/bash
[root@12d332ce5431 local]# 
```

#### step5 验证

```shell
curl  localhost:9090
47.105.***.247:9090
```

 ![img](https://img-blog.csdnimg.cn/20210312013759850.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaTE5ODYyMQ==,size_16,color_FFFFFF,t_70) 

#### step6 发布项目

 由于做了卷挂载，在本地发布项目就可以了，不用到容器发布了 

 代码参考
https://www.cnblogs.com/scu0413biubiubiu/p/7353839.html 

##### 本地文件目录结构

```
由于之间的挂载，如下，所以 本地的test 目录就是webapps 下面的一个目录
-v /wwwroot/tomcat9/test:/usr/local/apache-tomcat-9.0.33/webapps/test    ---- 本地路径  /wwwroot/tomcat9/test   挂载到容器的   /usr/local/apache-tomcat-9.0.33/webapps/test
-v  /wwwroot/tomcat9/logs:/usr/local/apache-tomcat-9.0.33/logs     ---- 本地路径  /wwwroot/tomcat9/logs   挂载到容器的   /usr/local/apache-tomcat-9.0.33/logs
 


[root@aliyunleo wwwroot]# tree
.
└── tomcat9
    ├── logs
    │   ├── catalina.2021-03-11.log
    └── test
        ├── index.jsp
        └── WEB-INF
            └── web.xml
[root@aliyunleo wwwroot]# pwd
/wwwroot

```

##### web.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
  <display-name>FirstWebFontEnd</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>

```

##### index.jsp的代码如下

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>首页</title>
<style>
    *{
    padding:0;
    margin:0;
    font-family:"微软雅黑";
}
.header{
    height:72px;
    background:#458fce ;
}
.header .logo{
    color:#fff ;
    line-height:70px;
    font-size:30px;
    margin-left:20px;
    display:inline-block;
    text-align:center;

}
a {
    color: #fff ;
    text-decoration: none ;
}
.header .login{
    float:right;
    color:#fff ;
    line-height:72px;
    margin-right:2px;
    display:inline-block;
}
.banner{
    height:380px;
    overflow:hidden;
    background: #ddd;
}
</style>
</head>
<body>
    <div class="header">
        <div class="logo">web实践</div>
        <div class ="login">
            <a href ="javascript:void(0)">登录</a>
            <span>|</span> 
            <a href ="javascript:void(0)">故事</a>
        </div>
    </div>
</body>
</html>

```

##### 测试

 成功访问 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210312014511472.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaTE5ODYyMQ==,size_16,color_FFFFFF,t_70) 

#### 注意启动外网端口（aliyun环境）

 云服务器 ECS/实例/实例详情 安全组 配置规则 安全组规则 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20210312015639991.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaTE5ODYyMQ==,size_16,color_FFFFFF,t_70) 



### 发布自己的镜像

#### 提交到DockerHub上

1、地址 https://hub.docker.com/ 注册自己的账号！

2、确定这个账号可以登录！

3、在我们服务器上提交自己的镜像！

```shelll
# 查看 docker login  指令
[root@localhost home]# docker login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username

# 登录
[root@localhost home]# docker login -u 你的登录名
Password: #密码
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

# 登录成功

```

 4、登录完毕后就可以提交镜像了，就是一步 docker push 

```shell
# push 自己的镜像到服务器上
[root@localhost home]# docker push diytomcat
The push refers to repository [docker.io/library/diytomcat]
f7ce4b1a8a61: Preparing
5c6781303655: Preparing
996457de2184: Preparing
bc85d4f50c99: Preparing
291f6e44771a: Preparing
denied: requested access to the resource is denied # 被拒绝
# 需要带有自己的信息和镜像版本号

# 解决
# 1、docker tag firstimage YOUR_DOCKERHUB_NAME/镜像名称:版本号
# 2、docker push YOUR_DOCKERHUB_NAME/镜像名称:版本号
[root@localhost home]# docker tag d1f139154142 YOUR_DOCKERHUB_NAME/diytomcat:1.0
# 查看镜像 发现又出现一个1.0版本的带有自己信息的镜像
[root@localhost test]# docker images
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
这个里是你的DockerHubName/diytomcat   1.0                 d1f139154142        About an hour ago   612MB
diytomcat              latest              d1f139154142        About an hour ago   612MB
entrypoint-test        latest              9d252b7bc745        18 hours ago        215MB
cmdtest                latest              7eb07d3647de        19 hours ago        215MB

# docker push 上去即可！ 自己发布的镜像尽量带上版本号！
[root@localhost test]# docker push YOUR_DOCKERHUB_NAME/diytomcat:1.0

```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130257828.png#pic_center) 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130310609.png#pic_center)

 提交的时候也是按照镜像的层级来提交的！ 

#### 提交到阿里云上

1、登录阿里云

2、找到容器镜像服务

3、创建命名空间（一个账号只能创建三个命名空间） 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130324931.png#pic_center) 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111513033863.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 4、创建容器镜像 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020111513050155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 ![img](https://img-blog.csdnimg.cn/20201115130517473.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130534703.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 浏览阿里云 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130545829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

```shell
# 退出dockerhub
[root@localhost test]# docker logout
Removing login credentials for https://index.docker.io/v1/
# 登录阿里云
[root@localhost test]# docker login --username=愚者派大星 registry.cn-hangzhou.aliyuncs.com
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

# 根据阿里云提供命令 docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/命名空间名称/镜像仓库名称:[镜像版本号]

[root@localhost ~]# docker tag d1f139154142 registry.cn-hangzhou.aliyuncs.com/bohaosong/diytomcat:1.0

# 提交阿里云
[root@localhost test]# docker push  registry.cn-hangzhou.aliyuncs.com/bohaosong/diytomcat:1.0


```

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130603194.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130615689.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RldmlsX1Nvbmc=,size_16,color_FFFFFF,t_70#pic_center) 

 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20201115130627685.png#pic_center) 

 阿里云容器镜像具体参考官方文档！ 

# Docker网络









# Docker Compose





# Docker Swarm







# CI/DI 之jenkins

