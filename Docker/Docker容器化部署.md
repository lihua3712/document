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





