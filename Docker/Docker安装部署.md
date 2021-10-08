官方文档地址：https://docs.docker.com/engine/install/centos/
 **删除老版本 Uninstall old versions** 

```
#1.卸载旧版本
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
 **设置镜像仓库 Set up the repository** 

```
#2.需要的安装包
 sudo yum install -y yum-utils

#3.设置镜像的仓库
默认
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

推荐使用
yum-config-manager  \
     --add-repo \ 
     http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
#更新yum软件包索引
`yum makecache fast`

 **安装引擎 Install Docker Engine** 

docker-ce 社区   -ee 企业
```
 sudo yum install docker-ce docker-ce-cli containerd.io
```
 **启动docker** 

```
sudo systemctl start docker
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0821/131709_b6a93381_5296156.png "屏幕截图.png")

 **通过运行hello-world 映像验证 Docker Engine 是否已正确安装。** 

```
sudo docker run hello-world
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0821/132101_0b999f49_5296156.png "屏幕截图.png")
查看docker镜像:
`docker images`
![输入图片说明](https://images.gitee.com/uploads/images/2021/0821/132438_934a788b_5296156.png "屏幕截图.png")
了解：卸载docker

```
1.卸载依赖
  sudo yum remove docker-ce docker-ce-cli containerd.io
2.删除资源
 sudo rm -rf /var/lib/docker         #docker的默认工作路径！
 sudo rm -rf /var/lib/containerd
```
 **阿里云镜像加速：** 

```
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://h6b3qkto.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload

sudo systemctl restart docker
```

![输入图片说明](https://images.gitee.com/uploads/images/2021/0821/133556_c1406ac2_5296156.png "屏幕截图.png")
配置使用：

 **帮助命令：** 

```
docker version    #显示docker的版本信息
docker info       #显示docker的系统信息，包括镜像和容器的数量
docker 命令  --help #帮助命令
```
帮助文档的地址：https://docs.docker.com/engine/reference/commandline/commit/
 **镜像命令** 

```
docker images 查看所有本地的主机上的镜像
[root@lh-1 ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    d1165f221234   5 months ago   13.3kB
#解释
REPOSITORY     镜像的仓库
TAG            镜像的标签
IMAGE ID       镜像的id
CREATED        镜像的创建时间
SIZE           镜像的大小
#可选项
-a   --all      #列出所有的镜像
-q   --quiet    #只显示镜像的id
```

```
docker search 搜索命令
[root@lh-1 /]# docker search mysql
NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   11308     [OK]


#可选项，通过搜索来过滤
--filter=STARS=3000   #搜索STARS大于3000的
[root@lh-1 /]# docker search mysql --filter=STARS=3000
NAME      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql     MySQL is a widely used, open-source relation…   11308     [OK]
mariadb   MariaDB Server is a high performing open sou…   4297      [OK]
```

```
docker pull  #下载命令
docker pull mysql
[root@lh-1 /]# docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
e1acddbe380c: Pull complete
bed879327370: Pull complete
03285f80bafd: Pull complete
ccc17412a00a: Pull complete
1f556ecc09d1: Pull complete
adc5528e468d: Pull complete
1afc286d5d53: Pull complete
6c724a59adff: Pull complete
0f2345f8b0a3: Pull complete
c8461a25b23b: Pull complete
3adb49279bed: Pull complete
77f22cd6c363: Pull complete
Digest: sha256:d45561a65aba6edac77be36e0a53f0c1fba67b951cb728348522b671ad63f926 #签名
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest          #真实地址


#等价于
docker pull mysql
docker pull docker.io/library/mysql:latest


#下载指定版本命令
[root@lh-1 /]# docker pull mysql:5.7
5.7: Pulling from library/mysql
e1acddbe380c: Already exists
bed879327370: Already exists
03285f80bafd: Already exists
ccc17412a00a: Already exists
1f556ecc09d1: Already exists
adc5528e468d: Already exists
1afc286d5d53: Already exists
4d2d9261e3ad: Pull complete
ac609d7b31f8: Pull complete
53ee1339bc3a: Pull complete
b0c0a831a707: Pull complete
Digest: sha256:7cf2e7d7ff876f93c8601406a5aa17484e6623875e64e7acc71432ad8e0a3d7e
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0823/105835_d491cab2_5296156.png "屏幕截图.png")

```
 **删除镜像命令** 
docker rmi -f  *image id号*                  #删除指定镜像
docker rmi -f  *image id号*  *image id号*    #删除多个指定镜像
docker rmi -f  $(docker images -aq)          #删除全部镜像
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0823/110924_71f18459_5296156.png "屏幕截图.png")


 **容器命令** 
说明：有了镜像才能创建容器
下载一个centerOS 
```
docker pull centos
```
新建容器并启动

```
docker run [可选参数] image

#参数说明
--name="NAME"  容器名称 用来区分容器
-d             后台方式运行
-it            使用交互方式运行，进入容器查看内容
-p             指定容器的端口  -p  8080:8080
        -p  ip:zhu
        -p  主机端口：容器端口（常用）
        -p 容器端口
-p             随机指定端口



#测试
[root@lh-1 /]# docker run -it centos /bin/bash
[root@372c6b9faa54 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@372c6b9faa54 /]# exit
[root@lh-1 /]# 
```

```
列出所有运行的容器
docker ps        #列出当前正在运行的容器
...       -a     #列出当前正在运行的容器+历史运行过的容器
...       -n=?   #显示最近创建的容器
...       -q     #只显示容器的编号
```

```
退出容器
exit       #直接容器停止并退出
Ctrl+P+Q   #容器不停止退出
```

```
删除容器
docker rm 容器id                    #删除指定容器，不能删除正在运行的容器，强制删除rm -f 
docker rm -f $(docker ps -aq)       #删除所有容器
docker ps -a -q | xargs docker rm   #删除所有容器
```

```
启动和停止容器的操作
docker start   容器id     #启动容器
docker restart 容器id     #重动容器
docker stop    容器id     #停止当前正在运行的容器
docker kill    容器id     #强制停止当前容器
```
 **其他常用命令** 


```
docker run -d 镜像名


```





