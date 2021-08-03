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

```



