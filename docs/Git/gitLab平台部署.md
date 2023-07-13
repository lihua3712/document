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
--注：查看端口是否被占用，防火墙是否放开--，
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
    #查看已经使用4G以上内存
    free -m

登录并启动GitLab:
登录成功，到此gitLab已经安装成功。。。。
 
 ****其他****  
 **邮件配置:** 

            gitlab_rails['smtp_enable'] = true
            gitlab_rails['smtp_address'] = "your.smtp.server"
            gitlab_rails['smtp_port'] = 465
            gitlab_rails['smtp_user_name'] = "your.smtp user"
            gitlab_rails['smtp_password'] = "your.smtp password"
            gitlab_rails['smtp_domain'] = "your.demain.com"
            gitlab_rails['smtp_authentication'] = "login"
            gitlab_rails['smtp_enable_starttls_auto'] = true
            gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
            gitlab_rails['gitlab_email_from'] = 'gitlab@mail.domain.com'
            gitlab_rails['gitlab_email_reply_to'] = 'noreply@mail.domain.com'


 **查看服务：** 
A.查看服务状态

            [root@localhost ~]# gitlab-ctl status
            run: alertmanager: (pid 224124) 81166s; run: log: (pid 223418) 81323s
            run: gitaly: (pid 224146) 81166s; run: log: (pid 223304) 81324s
            run: gitlab-monitor: (pid 224167) 81166s; run: log: (pid 223352) 81324s
            run: gitlab-workhorse: (pid 224192) 81165s; run: log: (pid 223250) 81325s
            run: logrotate: (pid 236092) 1964s; run: log: (pid 223251) 81325s
            run: nginx: (pid 227942) 80560s; run: log: (pid 223181) 81326s
            run: node-exporter: (pid 224294) 81164s; run: log: (pid 223308) 81324s
            run: postgres-exporter: (pid 224307) 81163s; run: log: (pid 223428) 81323s
            run: postgresql: (pid 224318) 81163s; run: log: (pid 223216) 81325s
            run: prometheus: (pid 224327) 81162s; run: log: (pid 223417) 81323s
            run: redis: (pid 224371) 81162s; run: log: (pid 223215) 81325s
            run: redis-exporter: (pid 224376) 81162s; run: log: (pid 223354) 81324s
            run: sidekiq: (pid 227863) 80573s; run: log: (pid 223158) 81326s
            run: unicorn: (pid 228149) 80549s; run: log: (pid 223157) 81326s
             
            格式：
            进程名称：（进程ID编号）进程运行时间（秒）；进程的日志服务进程Id  日志运行时间
             
            run    表示进程运行正常
            down   表示进程没有启动或者挂掉，我们可以查看服务的日志信息，来定位问题。


B.查看服务日志：

        # 检查redis的日志
        gitlab-ctl tail redis
        # 检查postgresql的日志
        gitlab-ctl tail postgresql
        # 检查gitlab-workhorse的日志
        gitlab-ctl tail gitlab-workhorse
        # 检查logrotate的日志
        gitlab-ctl tail logrotate
        # 检查nginx的日志
        gitlab-ctl tail nginx
        # 检查sidekiq的日志
        gitlab-ctl tail sidekiq
        # 检查unicorn的日志
        gitlab-ctl tail unicorn

注：每次修改完配置，记得启动配置更新。



### 第三步：GitLab插件安装

Sinicization包上传：gitlab-patch-zh.tar.gz
tar zxvf gitlab-patch-zh.tar.gz

#下载Sinicization补丁
git clone https://gitlab.com/xhang/gitlab.git

#查看Sinicization补丁的版本
cat /root/gitlab/VERSION

 #停止服务
gitlab-ctl stop   

#将解压的包覆盖在gitlab部署上
/usr/bin/cp -rv   /root/gitlab/*   /opt/gitlab/embedded/service/gitlab-rails/

gitlab-ctl restart    #重启服务

 **Sinicization成功！！！** 

        #停止服务
        gitlab-ctl stop    
        #启动服务
        gitlab-ctl start    
        #重启服务
        gitlab-ctl restart
------------------------------------------------------------------------------------------
	
/opt/gitlab/embedded/service/gitlab-rails/app/controllers/projects/wikis_controller.rb

response.headers['Content-Security-Policy'] = "default-src 'none'"
response.headers['X-Content-Security-Policy'] = "default-src 'none'"
将两行中的none，改成self
 	
yum install policycoreutils-python            
yum install -y policycoreutils-python	
strings /lib64/libc.so.6 |grep GLIBC_

    
  
