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

后台启动命令
```
# docker run -d 镜像名
[root@node128 /]# docker run -d centos
93fac83670ff36d630c78df1bd3eaa4d0c29e3eea3028a3e5e022d8faf370028
[root@node128 /]#
[root@node128 /]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@node128 /]#

#问题 docker ps ,发现docker停止了
#常见的坑， docker容器使用后台运行就必须要有一个前台进程，docker发现没有应用就会自动停止
#nginx ,容器启动后，发现自己没有提供服务，就会立刻停止，就是没有程序了
```
查看日志

```
docker logs -f -t --tail 容器，没有日志
#自己编写一段shell脚本
[root@node128 /]# docker run -d centos /bin/sh  -c "while true;do echo lihua...;sleep 1;done"
ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4
[root@node128 /]#
[root@node128 /]#
[root@node128 /]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS     NAMES
ed1ec268b63c   centos    "/bin/sh -c 'while t…"   6 seconds ago   Up 5 seconds             heuristic_galileo
#显示日志
      -tf   显示日志
      --tail num   要显示日志的条数
[root@node128 /]# docker logs -tf --tail 10 ed1ec268b63c

```
查看容器中进程信息

```
#命令  docker top 容器id
[root@node128 /]# docker top ed1ec268b63c
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                125021              125000              0                   20:00               ?                   00:00:00            /bin/sh -c while true;do echo lihua...;sleep 1;done
root                128320              125021              0                   20:08               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1

```
查看镜像的元数据

```
#命令
docker inspect 容器id

#测试
[root@node128 /]# docker inspect ed1ec268b63c
[
    {
        "Id": "ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4",
        "Created": "2021-10-08T12:00:31.046039688Z",
        "Path": "/bin/sh",
        "Args": [
            "-c",
            "while true;do echo lihua...;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 125021,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2021-10-08T12:00:32.086525645Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
        "ResolvConfPath": "/var/lib/docker/containers/ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4/hostname",
        "HostsPath": "/var/lib/docker/containers/ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4/hosts",
        "LogPath": "/var/lib/docker/containers/ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4/ed1ec268b63cdcfb49d0de0542a6302c3868159bf649f03e2c8cda32e6896ff4-json.log",
        "Name": "/heuristic_galileo",
        "RestartCount": 0,
        "Driver": "devicemapper",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "DeviceId": "46",
                "DeviceName": "docker-253:0-3151424-e13853936b449f9bd9a2576c667f16985f3cc0ff6aef95785d85a57b8049b9a5",
                "DeviceSize": "10737418240"
            },
            "Name": "devicemapper"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "ed1ec268b63c",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "while true;do echo lihua...;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20201204",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "1f0791d4762c9db1268cf4b3188eda4b3d6134e82736b5493a4e2d50a061f8b5",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/1f0791d4762c",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "dfb39c98a20ba999973822f64a1337eb381c6080836ebfd5938271d741057222",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "8a95c4d15d7c5398f03ee0b1209234ec07130f0a651d42f20cbbaf6116826a36",
                    "EndpointID": "dfb39c98a20ba999973822f64a1337eb381c6080836ebfd5938271d741057222",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```
进入当前正在运行的容器

```
#我们通常容器都是使用后台方式运行的，需要进入容器，修改一些配置

#命令
docker exec -it 容器id /bin/bash

#测试
[root@node128 /]#
[root@node128 /]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
ed1ec268b63c   centos    "/bin/sh -c 'while t…"   25 minutes ago   Up 25 minutes             heuristic_galileo
[root@node128 /]#
[root@node128 /]# docker exec -it ed1ec268b63c /bin/bash
[root@ed1ec268b63c /]#
[root@ed1ec268b63c /]# ls
bin   core.1369  core.1371  core.1373  core.1375  core.1377  core.1379  core.1381  core.1383  dev  home  lib64       media  opt   root  sbin  sys  usr
boot  core.1370  core.1372  core.1374  core.1376  core.1378  core.1380  core.1382  core.1384  etc  lib   lost+found  mnt    proc  run   srv   tmp  var
[root@ed1ec268b63c /]# ps -ef

#方式二
#命令  docker attach 容器id


#docker exec 容器id            -- 进入容器后开启一个新的终端，可以在里面操作(常用)
#docker attach 容器id          -- 进入容器正在执行的终端，不会启动新的进程
```
从容器内拷贝文件到主机

```
docker cp 容器id：容器内路径  目的主机路径

#查看当前主机目录下
[root@node128 home]# ll
总用量 0
-rw-r--r--. 1 root root 0 10月  8 22:41 lihua.java
[root@node128 home]#
[root@node128 home]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
725775e56fe5   centos    "/bin/bash"   5 minutes ago   Up 5 minutes             determined_elbakyan

#进入docker容器内部
[root@node128 home]# docker attach 725775e56fe5
[root@725775e56fe5 /]#
[root@725775e56fe5 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@725775e56fe5 /]# cd home/
[root@725775e56fe5 home]# ls

#在容器内新建一个文件
[root@725775e56fe5 home]# touch test.java
[root@725775e56fe5 home]# ll
bash: ll: command not found
[root@725775e56fe5 home]# ls
test.java
[root@725775e56fe5 home]# exit
exit
[root@node128 home]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
[root@node128 home]# docker ps -a
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
725775e56fe5   centos    "/bin/bash"   6 minutes ago   Exited (0) 7 seconds ago             determined_elbakyan

#将文件拷贝到主机
[root@node128 home]# docker cp 725775e56fe5:/home/test.java  /home
[root@node128 home]#
[root@node128 home]# ls
lihua.java  test.java
[root@node128 home]#


#拷贝是一个手动过程 未来使用 -V 卷的技术 ，可以实现
```






