1.安装依赖包

        //一键安装上面四个依赖
        yum -y install gcc zlib zlib-devel pcre-devel openssl openssl-devel

2.下载并解压安装包

       //创建一个文件夹
        cd /usr/local
        mkdir nginx
        cd nginx
        //下载tar包
        wget http://nginx.org/download/nginx-1.13.7.tar.gz
        tar -xvf nginx-1.13.7.tar.gz

3.安装nginx

       //进入nginx目录
       cd /usr/local/nginx
       //进入目录
       cd nginx-1.13.7
       //执行命令
       ./configure
       //执行make命令
       make
       //执行make install命令
       make install

4.配置nginx.conf
 
     # 打开配置文件
     vi /usr/local/nginx/conf/nginx.conf

5.启动nginx

    /usr/local/nginx/sbin/nginx -s reload 

    如果出现报错：nginx: [error] open() ＂/usr/local/nginx/logs/nginx.pid＂ failed
    则运行： /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
    再次启动即可！


查看nginx进程是否启动：

    ps -ef | grep nginx
![输入图片说明](https://images.gitee.com/uploads/images/2021/0728/155121_82fafd59_5296156.png "屏幕截图.png")

6.若想使用外部主机连接上虚拟机访问端口192.168.131.2，需要关闭虚拟机的防火墙：

centOS6及以前版本使用命令： systemctl stop iptables.service
centOS7关闭防火墙命令： systemctl stop firewalld.service

随后访问该ip即可看到nginx界面。

7.访问服务器ip查看（备注，由于我监听的仍是80端口，所以ip后面的端口号被省略）

![输入图片说明](https://images.gitee.com/uploads/images/2021/0728/155238_8538a82c_5296156.png "屏幕截图.png")

安装完成一般常用命令

    进入安装目录中，
    命令： cd /usr/local/nginx/sbin
    启动，关闭，重启，命令：
    ./nginx 启动
    ./nginx -s stop 关闭
    ./nginx -s reload 重启

