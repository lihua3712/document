 **下载安装** 
这里是CentOS7_64操作系统，所以我采用rpm包进行安装

下载：https://dev.mysql.com/downloads/mysql/
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/110215_14878379_5296156.png "屏幕截图.png")
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/110225_3aaf61c3_5296156.png "屏幕截图.png")
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/110232_680abdfc_5296156.png "屏幕截图.png")
 **移除mariadb** 
安装之前先检测有没有mariadb，如果有则移除，执行如下命令

```
# 查看，我这里显示有
[root@iz2zehvxttbua2f45dp7ihz java]# rpm -qa | grep mariadb
mariadb-libs-5.5.56-2.el7.x86_64

# 接着使用下面命令进行移除
[root@iz2zehvxttbua2f45dp7ihz java]# rpm -e mariadb-libs-5.5.56-2.el7.x86_64 --nodeps

# 再次查看有没有，没有则表示移除干净了
[root@iz2zehvxttbua2f45dp7ihz java]# rpm -qa | grep mariadb
```
上传安装文件
我们使用cd名跳转到/usr/local目录下，并执行mkdir mysql命令，创建mysql目录并进入

```
[root@iz2zehvxttbua2f45dp7ihz java]# cd ..
[root@iz2zehvxttbua2f45dp7ihz local]# ls
[root@iz2zehvxttbua2f45dp7ihz local]# mkdir mysql
[root@iz2zehvxttbua2f45dp7ihz local]# cd mysql
```
这里建议使用ftp上传，安装xftp或者其他ftp工具，使用ftp连接后进行上传，上传到/usr/local/mysql目录
查看文件目录位置及文件信息
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/110428_0e325f31_5296156.png "屏幕截图.png")
 **解压安装** 
1.执行解压命令进行解压

```
[root@iz2z7ihz mysql]# tar -xvf mysql-8.0.16-2.el7.x86_64.rpm-bundle.tar
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/110532_c91e228b_5296156.png "屏幕截图.png")
2.执行安装命令进行安装
安装common

```
# 命令:rpm -ivh mysql-community-common-8.0.16-2.el7.x86_64.rpm --nodeps --force

[root@iz2zehvxttbua2f45dp7ihz mysql]# rpm -ivh mysql-community-common-8.0.16-2.el7.x86_64.rpm --nodeps --force
```
安装libs

```
# 命令:rpm -ivh mysql-community-libs-8.0.16-2.el7.x86_64.rpm --nodeps --force

[root@iz2zehvxttbua2f45dp7ihz mysql]# rpm -ivh mysql-community-libs-8.0.16-2.el7.x86_64.rpm --nodeps --force
```
安装client

```
# 命令:rpm -ivh mysql-community-client-8.0.16-2.el7.x86_64.rpm --nodeps --force

[root@iz2zehvxttbua2f45dp7ihz mysql]# rpm -ivh mysql-community-client-8.0.16-2.el7.x86_64.rpm --nodeps --force
```
安装server

```
# 命令:rpm -ivh mysql-community-server-8.0.16-2.el7.x86_64.rpm --nodeps --force

[root@iz2zehvxttbua2f45dp7ihz mysql]# rpm -ivh mysql-community-server-8.0.16-2.el7.x86_64.rpm --nodeps --force
```
查看已安装的包

```
[root@iz2zehvxttbua2f45dp7ihz mysql]# rpm -qa | grep mysql
mysql-community-libs-8.0.16-2.el7.x86_64
mysql-community-server-8.0.16-2.el7.x86_64
mysql-community-common-8.0.16-2.el7.x86_64
mysql-community-client-8.0.16-2.el7.x86_64
```

3.初始化
通过以下命令完成mysql初始化操作

```
mysqld --initialize
```
mysqld --initialize





