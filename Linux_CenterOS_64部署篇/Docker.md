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


