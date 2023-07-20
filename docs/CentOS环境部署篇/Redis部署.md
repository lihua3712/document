下载地址：http://redis.io/download，下载最新稳定版本。

本教程使用的最新文档版本为 2.8.17，下载并安装：

```
# wget http://download.redis.io/releases/redis-6.0.8.tar.gz
# tar xzf redis-6.0.8.tar.gz
# cd redis-6.0.8
# make
```
执行完 make 命令后，redis-6.0.8 的 src 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli：
下面启动 redis 服务：
```
# cd src
# ./redis-server
```
注意这种方式启动 redis 使用的是默认配置。也可以通过启动参数告诉 redis 使用指定配置文件使用下面命令启动。

```
# cd src
# ./redis-server ../redis.conf
```
redis.conf 是一个默认的配置文件。我们可以根据需要使用自己的配置文件。
启动 redis 服务进程后，就可以使用测试客户端程序 redis-cli 和 redis 服务交互了。 比如：

```
# cd src
# ./redis-cli
redis> set foo bar
OK
redis> get foo
"bar"
```
redis连接命令
`redis-cli.exe -h 127.0.0.1 -p 6379`

 **配置说明：** 
首先，修改redis的配置文件，目录为：/usr/local/redis/redis.conf


```
1. bind 127.0.0.1改为 bind 0.0.0.0
127.0.0.1： 代表本地地址，访问redis服务只能通过本机的客户端连接，而无法通过远程连接
0.0.0.0： 接受所有来自于可用网络接口的连接

2. protected-mode yes 改为 protected-mode no
yes： 保护模式，只允许本地链接
no： 保护模式关闭

3. 加入 daemonize no(这个是是否在后台启动不占用一个主程窗口)
daemonize yes： 代表开启守护进程模式。此时是单进程多线程的模式，redis将在后台运行，并将pid写入redis.conf–pidfile文件中，此时redis将一直运行，除非手动kill
daemonize no： 当前界面将进入redis的命令行界面，exit强制退出或者关闭连接工具(xshell等)都会导致redis进程退出
```
修改配置文件后需要重启redis
注意： 启动redis 一定要指定配置文件，否则配置文件不生效的
./src/redis-server redis.conf

requirepass 连接密码（注释）