                                                     **服务器FTP手册** 
1.ftp安装配置目录/etc/vsftpd/ 端口21
2.ftp上传文件路径/home/ftpuser/file/
3.配置文件说明
   user_list为白名单
   ftpusers为黑名单
   vsftpd.conf 为主配置文件
注册用户成功后，将用户名配置在白名单内，然后修改主配置文件里的如下属性
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/140457_ba59d89c_5296156.png "屏幕截图.png")

chroot_local_user #是否将所有用户限制在用户当前的主目录,YES为启用 NO禁用.(该项默认值是NO,即在安装vsftpd后不做配置的话，ftp用户是可以向上切换到要目录之外的)
chroot_list_enable #是否启动限制用户的名单 YES为启用  NO禁用(包括注释掉也为禁用)
chroot_list_file=/etc/vsftpd/chroot_list #   简单点来说，这个文件就是为了我们上面组合而存在的，如果我们禁用名单开启。就是开出一个额外的附属情况给一些账号，让他们做出例外的特权。

通俗来讲：第一个配置是将所有的名单都进行限制，只能访问我们配置里预设的文件目录。第二个配置是启用黑名单。第三个配置是结合前两个配置给出的特权名单。
不过chroot_list文件默认不存在，需要新建。将特权用户写入即可。如下：
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/140538_9c433b89_5296156.png "屏幕截图.png")
