 **修改密码的几种方式：** 
一、使用 SET PASSWORD 命令
a. SET PASSWORD FOR root@localhost = PASSWORD('root');
![输入图片说明](https://images.gitee.com/uploads/images/2021/0524/095336_fce6b614_5296156.png "屏幕截图.png")

二、 使用mysqladmin修改密码
a. mysqladmin -uroot -p123 PASSWORD 123
注意：下图修改密码的命令中 -uroot 和 -proot 是整体，不要写成 -u root -p root，-u 和 root 间可以加空格，但是会有警告出现，所以就不要加空格了。
![输入图片说明](https://images.gitee.com/uploads/images/2021/0524/095328_dd6330b9_5296156.png "屏幕截图.png")

三、 UPDATE直接编辑user表
a. use mysql;  
   UPDATE USER SET PASSWORD=PASSWORD("123") WHERE USER="root" AND HOST='localhost';

##输入命令update mysql.user set authentication_string=password('新密码') where user='用户名' and Host ='localhost';
设置新密码。
b.输入 flush privileges; 命令刷新权限。
c.输入quit;命令退出 MySQL 重新登录，此时密码已经修改为刚才输入的新密码了。

四、 图形化工具修改  
在忘记root密码的时候，可以这样：
以windows为例：   
1. 关闭正在运行的MySQL服务。  
2. 打开DOS窗口，转到mysql\bin目录。  
3. 输入mysqld --skip-grant-tables 回车。--skip-grant-tables 的意思是启动MySQL服务的时候跳过权限表认证。  
4. 再开一个DOS窗口（因为刚才那个DOS窗口已经不能动了），转到mysql\bin目录。  
5. 输入mysql回车，如果成功，将出现MySQL提示符 >。  
6. 连接权限数据库： use mysql; 。  
6. 改密码：update user set password=password("123") where user="root";（别忘了最后加分号） 。  
7. 刷新权限（必须步骤）：flush privileges;　。  
8. 退出 quit。  
9. 注销系统，再进入，使用用户名root和刚才设置的新密码123登录。


 **授权语句：** 
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root';
 **刷新权限：** 
FLUSH PRIVILEGES;

