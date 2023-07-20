###  JDK配置

1.卸载已有的open-jdk

    查看目前已有的JDK
    $ rpm -qa | grep jdk

    输出如下
    copy-jdk-configs-3.3-10.el7_5.noarch
    java-1.8.0-openjdk-1.8.0.181-7.b13.el7.x86_64
    java-1.8.0-openjdk-headless-1.8.0.181-7.b13.el7.x86_64

    执行删除命令
    # 删除你执行时出现的结果
    $ rpm -e --nodeps java-1.8.0-openjdk-1.8.0.181-7.b13.el7.x86_64
    $ rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.181-7.b13.el7.x86_64

    再次查看确认
    # 再次确认，没有之后表示卸载干净
    $ rpm -qa | grep jdk

2.安装oracle官方jdk
    
    链接: https://pan.baidu.com/s/1Xd-zDEQDMc6Q0kwZ6o3PAA 
    提取码: bq6s

   准备好jdk.tar.gz文件，并使用解压命令tar -zxvf jdk.gz.tar将其解压
   使用vim命令编辑/etc/profile文件，在最后一行追加如下内容
![输入图片说明](https://images.gitee.com/uploads/images/2021/0728/151323_110261a4_5296156.png "屏幕截图.png")

     ##jdk1.8
     export JAVA_HOME=/usr/local/java/jdk1.8.0_181
     export PATH=$JAVA_HOME/bin:$PATH
     export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

   使JDK配置生效，并查看版本号

     $ source /etc/profile
     $ java -version
     # 输出版本号则表示成功

![输入图片说明](https://images.gitee.com/uploads/images/2021/0728/151504_fcd4adf2_5296156.png "屏幕截图.png")





