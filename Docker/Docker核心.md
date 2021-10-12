

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



# DockerFile













# Docker网络

