 **centos7安装部署gitlab服务器** 

-------------------------搭建gitLab私有服务-----------------------
查看swap分区是否启动,有数据则说明设置成功了
cat /proc/swaps
1.创建swap分区(/data/swap       /mnt/swap)
dd if=/dev/zero of=/mnt/swap bs=512 count=8388616
2.通过mkswap命令将上面新建出的文件做成swap分区
mkswap /mnt/swap
3.cat /proc/sys/vm/swappiness               <<<<-------->>>>  0
    sysctl -w vm.swappiness=60              <<<<-------->>>>  60
4.启动分区
  swapon /mnt/swap
  echo "/mnt/swap swap swap defaults 0 0">>/etc/fstab
5.查次查看swap分区是否启动,有数据则说明设置成功了
cat /proc/swaps

---------@@安装GitLab需要的组件@@-----------
mv /etc/yum.repos.d/CentOS-*/opt/
yum install curl policycoreutils openssh-server openssh-client postfix -y                              yum clean all              yum makecache             yum update
默认使用Postfix发送邮件
systemctl start postfix
iptables -F       #清空规则

yum install lrzsz

rz

wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.8.4-ce.0.el7.x86_64.rpm
gitlab-ce-10.2.3-ce.0.el7.x86_64.rpm 


rpm -ivh .......

vim /etc/gitlab/gitlab.rb

gitlab-ctl reconfigure    #重新配置


gitlab-ctl restart    #重启服务


gitlab-ctl status    #重启服务


netstat -anup | grep :80

iptables -F

free -m



-----汉化gitLab私有平台--------	

汉化包上传
tar zxvf .......
查看汉化的版本
cat /root/gitlab/VERSION


gitlab-ctl stop    #停止服务

/usr/bin/cp -rv   /root/gitlab/*   /opt/gitlab/embedded/service/gitlab-rails/

gitlab-ctl restart    #重启服务
------------------------------------------------------------------------------------------------------------

	
/opt/gitlab/embedded/service/gitlab-rails/app/controllers/projects/wikis_controller.rb

response.headers['Content-Security-Policy'] = "default-src 'none'"
response.headers['X-Content-Security-Policy'] = "default-src 'none'"
将两行中的none，改成self
 
	
	
yum install policycoreutils-python             #########	
yum install -y policycoreutils-python	
strings /lib64/libc.so.6 |grep GLIBC_
-------------------------------
gitlab-ctl stop    #停止服务
gitlab-ctl start    #启动服务
gitlab-ctl restart    #重启服务
	
	
-------------------------------------------------------------------
1、uname －a ?

Linux查看版本当前操作系统内核信息??

2、cat /proc/version

Linux查看当前操作系统版本信息? ? ??

3、 cat /etc/issue ?或cat /etc/redhat-release

Linux查看版本当前操作系统发行版信息

4、cat /proc/cpuinfo

查看cpu的信息，

processor ? ? 逻辑处理器的id。

physical id ? ?正式的物理处理器的id。

core id? ? ? ? ? ?cpu里面的每个核心的id。

cpu cores ? ? 每个cpu物理处理器中的内核数量。

siblings ? ? ? ? 每个cpu物理处理器中的逻辑处理器的数量。
————————————————

-------------------------------------------------------------------
Linux CenterOS7.6(1年)
公网ip:47.97.103.127
私网ip:172.16.228.58
阿里云远程连接密码： 144009
用户名:root
密码:LIHUA3712@
-------------------------------------------------------------------

阿里云ECS服务器：
ip:47.116.31.30
用户名：root
密码：Cst58548808

mysql数据库：
ip:47.116.31.30                               
用户名:root 
密码：-pEj7VxkEkF_,q

------------------------Elasticsearch 6.4.3------------------------

elasticsearch查看集群状态:http://localhost:9200/_cat/health?v
elasticsearch查看索引列表:http://localhost:9200/_cat/indices?v


GRANT ALL PRIVILEGES ON *.* TO 'root' @'%' IDENTIFIED BY 'root';
FLUSH PRIVILEGES;

--------------------------------------------------------------------

ping video-center.alivecdn.com
telnet  47.10.16.249 1935
ffprobe -show_frames rtmp://live.zhibotest.com/AppName/StreamName

var shouxin= $("input:radio[name='shouxin']:checked").val(); //链接分类
  
   
脚本启动Eclipse
 start 
.\eclipse\eclipse.exe  
-vm .\jdk1.8.0_111\bin\javaw.exe 
-clean -data .\workspace 
-vmargs -Xms512m -Xmx512m 
-XX:PermSize=128m 
-XX:MaxPermSize=128m -Xmn168m 
-XX:+DisableExplicitGC