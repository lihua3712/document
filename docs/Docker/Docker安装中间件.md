```
0、Docker可视化
Docker 图形化界面管理工具！提供一个后台面板供我们操作！
docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

```
1、安装mysql
docker run -itd --name ry-mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root mysql:5.7
```
```
2、安装nacos
docker run -itd --name ry-nacos -p 8848:8848 -e MODE=standalone -e NACOS_AUTH_ENABLE=true \
-v /home/nacos/logs:/home/nacos/logs \
-v /home/nacos/conf/application.properties:/home/nacos/conf/application.properties \
nacos/nacos-server:v2.2.3

# db mysql  数据库配置
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://ry-mysql:3306/ry-config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=root
db.password=root
```

```
3、安装Sentinel-dashboard

docker run -itd --name sentinel -p 8858:8858 bladex/sentinel-dashboard:latest
```

### 安装私有镜像仓库
方式一：使用的是Docker公司开发的私有镜像仓库Registry

```
下载Registry的Docker镜像；
   docker pull registry:2

使用Docker容器运行Registry服务，需要添加环境变量REGISTRY_STORAGE_DELETE_ENABLED=true开启删除镜像的功能；
   docker run -p 5000:5000 --name registry2 \
    --restart=always \
    -e REGISTRY_STORAGE_DELETE_ENABLED="true" \
    -d registry:2

修改Docker Daemon的配置文件，文件位置为/etc/docker/daemon.json，由于Docker默认使用HTTPS推送镜像，而我们的镜像仓库没有支持，所以需要添加如下配置，改为使用HTTP推送；
{
  "insecure-registries": ["192.168.3.101:5000"]
}

最后使用如下命令重启Docker服务。
systemctl daemon-reload && systemctl restart docker

### ----------------------------------镜像仓库可视化 -----------------------------------
由于私有镜像仓库管理比较麻烦，而docker-registry-ui有专门的页面可以方便地管理镜像，所以我们安装它来管理私有镜像仓库。

下载docker-registry-ui的Docker镜像:
   docker pull joxit/docker-registry-ui:static

使用Docker容器运行docker-registry-ui服务:
    docker run -p 8280:80 --name registry-ui \
    --link registry2:registry2 \
    -e REGISTRY_URL="http://registry2:5000" \
    -e DELETE_IMAGES="true" \
    -e REGISTRY_TITLE="Registry2" \
    -d joxit/docker-registry-ui:static

```
我们先来试试私有镜像仓库是否可用，首先下载一个测试用的镜像busybox；

```
docker pull busybox
docker tag busybox 192.168.3.101:5000/busybox:v1.0
docker push 192.168.3.101:5000/busybox:v1.0
访问docker-registry-ui管理界面，即可查看到busybox镜像，地址：http://192.168.3.101:8280
```
![输入图片说明](https://foruda.gitee.com/images/1688972270010570281/b2899bdc_5296156.png "屏幕截图")

