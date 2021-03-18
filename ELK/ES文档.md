**安装成windows服务**
1.进入ElasticSearch 的bin 目录 执行elasticsearch-service.bat install这时已经是windows服务了，如下图所示：
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/111722_6b40fe1b_5296156.png "屏幕截图.png")
2.然后执行elasticsearch-service.bat manager然后把 use default 勾上 或者把 JAVA Virtual Machine 设置成绝对路径
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/111740_b43ae22a_5296156.png "屏幕截图.png")
3.按住windows+R 键 输入services.msc 进去服务面板设置自动启动项
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/111757_20deaa01_5296156.png "屏幕截图.png")
4.测试ElasticSearch是否启动成功，访问http://localhost:9200
![输入图片说明](https://images.gitee.com/uploads/images/2021/0318/111813_688714af_5296156.png "屏幕截图.png")
到此，安装完成！！！