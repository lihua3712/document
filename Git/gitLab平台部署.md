 **centos7安装部署gitlab服务器** 

### 第一步：查看并设置分区
 _（打开swap分区，要不然内存不到4G以上会崩溃访问不了）_ 
 **一** ：查看swap分区是否启动,有数据则说明设置成功了
cat /proc/swaps
如果只有标题栏，而查不出来数据，那就是没有swap分区。下面是解决方法：

    1.创建swap分区(有的是/data/swap，也有的是/mnt/swap)
     dd if=/dev/zero of=/mnt/swap bs=512 count=8388616
    2.通过mkswap命令将上面新建出的文件做成swap分区
     mkswap /mnt/swap
    3.查看内核参数vm.swappiness中的数值是否为0，如果为0则根据实际需要调整成60
      a.查看参数
      cat /proc/sys/vm/swappiness
      b.若是0设置参数
      sysctl -w vm.swappiness=60           
    4.启动分区
      swapon /mnt/swap
      echo "/mnt/swap swap swap defaults 0 0">>/etc/fstab
    5.查次查看swap分区是否启动,有数据则说明设置成功了
      cat /proc/swaps
### 第二步：安装GitLab需要的组件
     mv /etc/yum.repos.d/CentOS-* /opt/
     yum install curl policycoreutils openssh-server openssh-client postfix -y
 _备注：_                               
_yum clean all            
yum makecache            
yum update_ 

    #默认使用Postfix发送邮件
    systemctl start postfix
    #清空规则
    iptables -F  
     
 **安装gitLab:** 
将下载的软件包gitlab-ce-10.2.3-ce.0.el7.x86_64.rpm上传到linux系统中。

    安装插件：
    yum install lrzsz
    选择软件包：
    rz(手动选择)
    wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/yum/el7/gitlab-ce-10.8.4-ce.0.el7.x86_ 64.rpm（wget方式下载）
 
rpm -ivh gitlab-ce-10.2.3-ce.0.el7.x86_64.rpm
最终，gitLab是安装到：

    ls /opt/gitlab/

修改配置文件：（修改gitlab外部访问地址）

    vim /etc/gitlab/gitlab.rb
    external_url 'http://gitlab.example.com'
    ==>>         'http://192.168.18.128'

配置并启动GitLab:

    #重新配置，改完配置文件需重新配置再重启，否则不生效
    gitlab-ctl reconfigure    
    #重启服务
    gitlab-ctl restart        
    #重启状态 
    gitlab-ctl status  
    #查看网络端口情况       
    netstat -anup | grep :80
    #清空规则
    iptables -F
    free -m

-----gitLab私有平台--------	

包上传
tar zxvf .......
查看的版本
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
  
