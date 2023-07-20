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

为了避免出现权限问题，给mysql解压文件所在目录赋予最大权限
```
chmod -R 777 mysql
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
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/111042_2045a685_5296156.png "屏幕截图.png")
则安装下libaio.so.1包
```
# 方案1
yum install -y libaio

# 方案2，如果方案1安装后，继续初始化mysql没成功，则执行该方案
yum install -y libaio.so.1
```
授权服务防火墙配置

```
#给mysql目录授权给mysql组合mysql用户，该步骤一般不用操作，我们使用的root用户拥有所有权限，
$ chown mysql:mysql /var/lib/mysql -R;

# 启动mysql服务
systemctl start mysqld.service;

# 配置开机启动
$ systemctl enable mysqld;
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/111134_4f63ff29_5296156.png "屏幕截图.png")
查看数据库默认密码

```
# 该密码为随机生成kQ*q9a1eQpZq
[root@sdfsdf mysql]# cat /var/log/mysqld.log | grep password
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/111205_884c03b0_5296156.png "屏幕截图.png")
修改密码

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```
然后退出以修改后的密码重新登录
![输入图片说明](https://images.gitee.com/uploads/images/2021/0805/111259_c45259fa_5296156.png "屏幕截图.png")
授权远程访问

```
create user 'root'@'%' identified with mysql_native_password by '123456';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```
修改加密规则，目前可视化工具如Navicat普遍只支持MySQL5.X，所以我们需要调整8.0的加密规则

```
-- 修改命令
ALTER USER 'root'@'localhost' IDENTIFIED BY 'root' PASSWORD EXPIRE NEVER;

-- 刷新权限
flush privileges;
```
执行exit退出mysql控制台

此时你采用Navicat等可视化客户端是无法连接该服务的，因为端口未对外开放
4.防火墙配置
本地虚拟机需要开启防火墙，CentOS6采用的iptables，而CentOS7采用的是systemctl，所以下面我们采用CentOS7的配置

```
#查看防火墙状态
$ systemctl status firewalld

#启动防火墙，防火墙启动后，除了22端口对外能够访问，其他端口均不能使用，所以需要添加
$ systemctl start firewalld

#添加端口
 firewall-cmd --zone=public --add-port=80/tcp --permanent
 firewall-cmd --zone=public --add-port=3306/tcp --permanent
 firewall-cmd --zone=public --add-port=6379/tcp --permanent
 
#重新加载
$ firewall-cmd --reload
```
查看mysql配置文件

```
# 默认的配置文件为：/etc/my.cnf
$ cat /etc/my.cnf
```
注意
Linux下的MySQL数据库大小写敏感，所以SQL语句中的表名区分大小写
忽略大小写配置

```
$ vim /etc/my.conf
[mysqld]
lower_case_table_names=1
```
查看进程语句

```
$ ps -ef | grep mysql
```

