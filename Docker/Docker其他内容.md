### **Commit镜像** 

```
docker commit    提交容器成为一个信息副本


#命令和git原理类似
docker commit -a="作者" -m="描述" 容器id  目标镜像名：[tag]

```
实战测试

```
#1.启动默认tomcat

#2.默认tomcat没有webapps应用，镜像的原因，官方镜像默认webapps下没有文件

#3.拷贝进去基本文件

#4.将操作过的容器通过commit提交为一个新的镜像
```
![输入图片说明](https://images.gitee.com/uploads/images/2021/1011/163946_215380c4_5296156.png "屏幕截图.png")
