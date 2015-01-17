---
layout: post
title: "Docker入门之链接多个容器"
category: docker
---

这一篇来介绍如何把多个容器关联在一起工作。

####复习下容器的端口绑定

1. 随机绑定端口

	$ sudo docker run -d -P training/webapp python app.py

2. 指定端口绑定

	$ sudo docker run -d -p 5000:5000 training/webapp python app.py
	
如果要指定IP，可以
	
	$ sudo docker run -d -p 127.0.0.1::5000 training/webapp python app.py
	
同样也可以只绑定成udp模式

	$ sudo docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py

其中`-p`参数可以用多次来指定不同的端口映射。
	
如果忘了绑定到哪了，可以用`docker port`来查看

	$ sudo docker port nostalgic_morse 5000
	127.0.0.1:49155

####容器名字

链接容器要通过名字来指定关联关系，其中命名可以带来两个额外的好处。

1. 见名知意，容器的名字就按照它里面的软件来命名比较方便，也容易记住。
2. 方便链接，例如，一个web容器就可以跟一个db容器去链接。

通过`--name`参数来命名一个容器，

	$ sudo docker run -d -P --name web training/webapp python app.py
	
通过`docker ps -l`可以查看到它的名字

	$ sudo docker ps -l
	CONTAINER ID  IMAGE                  COMMAND        CREATED       STATUS       PORTS                    NAMES
	aed84ee21bde  training/webapp:latest python app.py  12 hours ago  Up 2 seconds 0.0.0.0:49154->5000/tcp  web
	
也可以用`docker inspect`来查看名字

	$ sudo docker inspect -f "{{ .Name }}" aed84ee21bde
	/web

注意：名字要唯一，也就是说同时运行的容器里不能重名，否则只能用`docker rm`来删掉暂时不用的那个。


####链接容器

首先，我们来启动一个db容器

	$ sudo docker run -d --name db training/postgres
	
然后，删掉之前叫做web的容器

	$ sudo docker rm -f web
	
然后，重新启动一个容器并且连接db容器

	$ sudo docker run -d -P --name web --link db:db training/webapp python app.py
	
其中`--link`的用法是`--link name:alias`

使用`docker inspect`可以查看与该容器相连接的信息

	$ sudo docker inspect -f "{{ .HostConfig.Links }}" web
	[/db:/web/db]
	
使用link来链接容器的好处就是可以不用EXPOSE来暴露端口给host，而这两个容器之间创建了个安全通道来进行网络交互。

而源容器跟附属容器的通信通过以下两种方式：

1. 环境变量
2. 修改/etc/hosts文件

#####环境变量 

当两个容器相连接的时候，docker会通过设定环境变量的方法来方便附属容器用程序方式来查找源容器。

首先，Docker会设定`<alias>_NAME`来指定每一个目标容器的别名，这个取决于`--link`时的参数。例如，如果一个web容器通过`--link db:webdb`来链接到db容器，那么这个db容器在web容器中会定义为`WEBDB_NAME=/web/webdb`

同样，docker也会定义一堆环境变量来标示各种端口。

	<name>_PORT_<port>_<protocol> will contain a URL reference to the port. Where <name> is the alias name specified in the --link parameter (e.g. webdb), <port> is the port number being exposed, and <protocol> is either TCP or UDP. The format of the URL will be: <protocol>://<container_ip_address>:<port> (e.g. tcp://172.17.0.82:8080). This URL will then be split into the following 3 environment variables for convenience:

	<name>_PORT_<port>_<protocol>_ADDR will contain just the IP address from the URL (e.g. WEBDB_PORT_8080_TCP_ADDR=172.17.0.82).

	<name>_PORT_<port>_<protocol>_PORT will contain just the port number from the URL (e.g. WEBDB_PORT_8080_TCP_PORT=8080).
	<name>_PORT_<port>_<protocol>_PROTO will contain just the protocol from the URL (e.g. WEBDB_PORT_8080_TCP_PROTO=tcp).

回到刚才的web连接db的例子，

	$ sudo docker run --rm --name web2 --link db:db training/webapp env
    . . .
    DB_NAME=/web2/db
    DB_PORT=tcp://172.17.0.5:5432
    DB_PORT_5432_TCP=tcp://172.17.0.5:5432
    DB_PORT_5432_TCP_PROTO=tcp
    DB_PORT_5432_TCP_PORT=5432
    DB_PORT_5432_TCP_ADDR=172.17.0.5

####/etc/hosts的修改

Docker不仅通过上面的环境变量来通信，还修改了hosts文件

	$ sudo docker run -t -i --rm --link db:db training/webapp /bin/bash
	root@aed84ee21bde:/opt/webapp# cat /etc/hosts
	172.17.0.7  aed84ee21bde
	. . .
	172.17.0.5  db
	
于是可以直接ping通db容器

	root@aed84ee21bde:/opt/webapp# apt-get install -yqq inetutils-ping
	root@aed84ee21bde:/opt/webapp# ping db
	PING db (172.17.0.5): 48 data bytes
	56 bytes from 172.17.0.5: icmp_seq=0 ttl=64 time=0.267 ms
	56 bytes from 172.17.0.5: icmp_seq=1 ttl=64 time=0.250 ms
	56 bytes from 172.17.0.5: icmp_seq=2 ttl=64 time=0.256 ms
	
可以用多个web容器来链接db容器，并且如果db容器重启，web容器中的ip地址也会跟着变的。

	$ sudo docker restart db
	root@aed84ee21bde:/opt/webapp# cat /etc/hosts
	172.17.0.7  aed84ee21bde
	. . .
	172.17.0.9  db
	

####问题
那么问题来了...在web容器中的程序，怎么写db的地址啊？全都统一用一个环境变量么？
思考下

