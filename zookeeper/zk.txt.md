#!/bin/bash
case $1 in
"start"){
for i in node128 node129 node130
do
       echo ---------- zookeeper $i 启动 ------------
       ssh $i "/usr/local/zookeeper/apache-zookeeper-3.6.3-bin/bin/zkServer.sh start"
done
};;
"stop"){
for i in node128 node129 node130
do
       echo ---------- zookeeper $i 停止 ------------ 
       ssh $i "/usr/local/zookeeper/apache-zookeeper-3.6.3-bin/bin/zkServer.sh stop"
done
};;
"status"){
for i in node128 node129 node130
do
        echo ---------- zookeeper $i 状态 ------------ 
        ssh $i "/usr/local/zookeeper/apache-zookeeper-3.6.3-bin/bin/zkServer.sh status"
done
};;
esac
