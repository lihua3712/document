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

```


 **容器命令** 
