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
  
