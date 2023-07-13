### **Docker安装Ngxin** 

```
# 1.搜索镜像nginx  search  建议去dockerhub中搜，可以看到帮助文档
# 2.下载镜像  pull
# 3.运行测试
docker search nginx
docker pull   nginx
docker images


# -d 后台运行
# --name 给容器命名
# -p 宿主机端口 ：容器内部端口

docker run -d --name nginx01 -p 3344:80 nginx

[root@node128 home]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED             STATUS          PORTS                                   NAMES
a90b5a285085   nginx     "/docker-entrypoint.…"   About an hour ago   Up 10 minutes   0.0.0.0:3344->80/tcp, :::3344->80/tcp   nginx01
725775e56fe5   centos    "/bin/bash"              2 hours ago         Up 49 minutes                                           determined_elbakyan

[root@node128 home]# curl localhost:3344

#进入容器
[root@node128 /]# docker exec -it nginx01 /bin/bash
root@a90b5a285085:/#


root@a90b5a285085:/# whereis nginx
nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
root@a90b5a285085:/#
root@a90b5a285085:/# cd /etc/nginx/
root@a90b5a285085:/etc/nginx#
root@a90b5a285085:/etc/nginx# ls
conf.d  fastcgi_params  mime.types  modules  nginx.conf  scgi_params  uwsgi_params
root@a90b5a285085:/etc/nginx#


```
### **Docker安装Tomcat** 

```
#官方的使用
docker run -it --rm tomcat:9.0

#之前的启动都是后台启动，停止之后容器是可以查到的，docker run -it --rm * ，一般用来测试，用完即删

#下载再启动 
docker pull tomcat

#启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat

#测试访问

#进入容器
docker exec -it tomcat01 /bin/bash

#发现问题 
1.linux命令少了
2.没有webapps.阿里云镜像的原因。默认是最小镜像，所有不必要的都要剔除掉
#保证最小可运行的环境！


```

### **Docker安装elasticsearch+Kibana** 

```
#es 暴露的端口很多！
#es 十分的耗内存
#es 的数据一般需要放置到安全目录！挂载
#   --net somenetwork ?   网络配置

#启动 elasticsearch
$ docker network create somenetwork
$ docker run -d --name elasticsearch01  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2

#查看cpu的状态
docker stats

#测试一下是否成功

```

![输入图片说明](https://images.gitee.com/uploads/images/2021/1011/140420_ecc58101_5296156.png "屏幕截图.png")

```
#赶紧关闭，增加内存的限制，修改配置文件 -e 环境配置修改
$ docker run -d --name elasticsearch02  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m"  elasticsearch:7.6.2

#查看 docker stats
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/1011/141917_77fd8922_5296156.png "屏幕截图.png")
### **Docker可视化** 


```
Docker 图形化界面管理工具！提供一个后台面板供我们操作！
[root@node128 ~]# docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```
访问测试：http://ip:8088



