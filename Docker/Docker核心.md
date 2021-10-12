

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

![输入图片说明](https://images.gitee.com/uploads/images/2021/1012/163325_e64bd00b_5296156.png "屏幕截图.png")

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

![输入图片说明](https://images.gitee.com/uploads/images/2021/1012/163349_827874e1_5296156.png "屏幕截图.png")

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

![输入图片说明](https://images.gitee.com/uploads/images/2021/1012/163408_50e0cdd9_5296156.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2021/1012/163432_b4506098_5296156.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2021/1012/163441_b7141879_5296156.png "屏幕截图.png")

## 数据卷容器

```shell
#共享容器卷   --volumes-from
  例：
      docker02 --volumes-from docker01  #容器02共享容器01的数据，继承01
$ docker run -it --name docker02 --volumes-from d0b01d732f6a lihua/centos:1.0
```

## 进阶DockerFile



# Docker网络

