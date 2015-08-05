

记录安装mesos、spark

1. mesos

按照文档装就可以，最后的make install要执行

mesos中slave成功
http://mesos.apache.org/gettingstarted/


2. spark

按照文档装

先启动mesos
然后启动hdfs, 按照文档启动即可

可以上传文件

根据Cluster mode然后在spark-1.4.0-bin-hadoop2.6中执行
MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so sbin/start-mesos-dispatcher.sh -m mesos://10.0.1.23:5050 -h 10.0.1.23

mesos中注册framework成功

3. marathon

https://mesosphere.github.io/marathon/docs/


MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so nohup ./bin/start --master zk://127.0.0.1:2181,127.0.0.1:2181/mesos --zk zk://127.0.0.1:2181,127.0.0.1:2181/marathon &

