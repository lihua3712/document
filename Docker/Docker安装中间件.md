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
