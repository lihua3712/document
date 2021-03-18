Windows环境下安装redis服务：
卸载服务：redis-server --service-uninstall

开启服务：redis-server --service-start

停止服务：redis-server --service-stop

设置密码（永久）：
进入Redis的安装目录打开：redis.windows-conf，按下Ctrl+F查找：requirepass，找到编辑保存然后重新启动Redis服务即可。（注意：这里是redis.windows-conf，windows自启动会加载这个配置文件）
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/134721_b688ba2a_5296156.png "屏幕截图.png")
设置临时密码（服务重启后失效）
获取密码：config get requeirepass 
设置密码：config set requirepass‘123’