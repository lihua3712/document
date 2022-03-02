```
1.  rz  上传   yum insatll lrzsz
2.  sz  下载   yum insatll lrzsz
```
```
3.netstat -apn    查看网络状态
an，按一定顺序排列输出
p，表示显示那个进程在调用
```

```
4.uname 
-m 查看系统是几位的操作系统
-r 查看系统的内核版本
-a 查看详细的系统内核版本和系统的操作系统
```

```
hostname，查看当前的主机名为
修改etc/hostname文件修改主机名，输入命令：vim /etc/hostname
重启生效
#----------------------------------------------------------
root@user-PC:~# hostname -i
hostname: Name or service not known
 
root@user-PC:~# vi /etc/hosts
10.170.200.148  node148
 
## 设置hostname 
root@user-PC:~# hostnamectl --static set-hostname node148
## 再执行的hostname的时候就不会报错了.
root@user-PC:~# hostname -i
10.170.200.148
root@user-PC:~# hostname 
node148
```

```
关闭防火墙：
开启：service iptables start
关闭：service iptables stop

永久关闭防火墙：
开启：chkconfig iptables on 
关闭：chkconfig iptables off
```
云服务器，开放安全组，
虚拟机,开放防火墙，在防火墙中加入对应端口并重载配置

```
（1）设置开机启用防火墙：systemctl enable firewalld.service
（2）设置开机禁用防火墙：systemctl disable firewalld.service
（3）启动防火墙：systemctl start firewalld
（4）关闭防火墙：systemctl stop firewalld
（5）检查防火墙状态：systemctl status firewalld 

（1）查看防火墙状态：firewall-cmd --state
（2）重新加载配置：firewall-cmd --reload
（3）查看开放的端口：firewall-cmd --list-ports
（4）开启防火墙端口：firewall-cmd --zone=public --add-port=9200/tcp --permanent
　　命令含义：
　　–zone #作用域
　　–add-port=9200/tcp #添加端口，格式为：端口/通讯协议
　　–permanent #永久生效，没有此参数重启后失效
　　注意：添加端口后，必须用命令firewall-cmd --reload重新加载一遍才会生效
（5）关闭防火墙端口：firewall-cmd --zone=public --remove-port=9200/tcp --permanent

  （6）查询防火墙端口：firewall-cmd --query-port=8848/tcp
```

Start......
```
固定ip地址
参考虚拟机
刷新配置：
systemctl restart network
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/0903/101435_9680c834_5296156.png "屏幕截图.png")
END......

```
1.  如果是新安装的操作系统，要先以管理员身份，升级系统：
   #sudo yum update

2.  如果wget没有安装，运行：
  # yum install wget
```


 **远程拷贝命令：** 
```
linux 将一个服务器上的文件或者文件夹复制到另一台服务器上
使用scp将一个Linux系统中的文件或文件夹复制到另一台Linux服务器上

复制文件或文件夹（目录）命令：
  一、复制文件：
  （1）将本地文件拷贝到远程
  scp 文件名 用户名@计算机IP或者计算机名称:远程路径

  
  本地192.168.1.8客户端

scp /root/install.* root@192.168.1.12:/usr/local/src
  （2）从远程将文件拷回本地
  scp 用户名@计算机IP或者计算机名称:文件名  本地路径

 

  本地192.168.1.8客户端取远程服务器12、11上的文件

scp root@192.168.1.12:/usr/local/src/*.log /root/
  二、复制文件夹（目录）：

  （1）将本地文件夹拷贝到远程
  scp -r 目录名 用户名@计算机IP或者计算机名称:远程路径

scp -r /home/test1 zhidao@192.168.0.1:/home/test2 
#test1为源目录，test2为目标目录，zhidao@192.168.0.1为远程服务器的用户名和ip地址。
  （2）从远程将文件夹拷回本地
  scp -r 用户名@计算机IP或者计算机名称:目录名 本地路径

scp  -r zhidao@192.168.0.1:/home/test2 /home/test1
#zhidao@192.168.0.1为远程服务器的用户名和ip地址，test1为源目录，test2为目标目录。
```



