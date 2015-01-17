---
layout: post
title: "Docker入门之如何操作容器"
category: docker
---


这一篇主要是来讲述下如何操作容器。
有以下几个命令比较重要：
	
	docker ps 查看当前有哪些容器在工作
	docker logs 查看容器的日志
	docker stop 停止某个容器
	
####以运行一个web容器为例

	sudo docker run -d -P training/webapp python app.py
我们在`training/webapp`镜像下执行 `python app.py`这个项目。

其中`-d`参数表示让docker在后台运行。
`-P`参数表示把需要映射的端口都映射到host机上。

使用`docker ps`可以查看到下面结果：

	$ sudo docker ps -l
	CONTAINER ID  IMAGE                   COMMAND       CREATED        STATUS        	PORTS                    NAMES
	bc533791f3f5  training/webapp:latest  python app.py 5 seconds ago  Up 2 	seconds  0.0.0.0:49155->5000/tcp  nostalgic_morse
	
其中`-l`参数表示显示出详细信息。

其中`0.0.0.0:49155->5000/tcp`就是`-P`参数的效果，它把`app.py`的默认端口5000映射到了主机的49155端口上，这样访问49155端口就可以访问到该web项目了。

如果我们不想让它这样随机的映射端口，则可以执行`sudo docker run -d -p 5000:5000 training/webapp python app.py`，通过`-p`参数来指定映射端口。

####查看指定端口

	$ sudo docker port nostalgic_morse 5000
	0.0.0.0:49155
可以用`docker port`这样来查看指定端口的映射情况。

####查看日志

	$ sudo docker logs -f nostalgic_morse
	* Running on http://0.0.0.0:5000/
	10.0.2.2 - - [23/May/2014 20:16:31] "GET / HTTP/1.1" 200 -
	10.0.2.2 - - [23/May/2014 20:16:31] "GET /favicon.ico HTTP/1.1" 404 -
通过`docker logs` 来查看对应容器的日志。其中`-f`参数的效果跟`tail -1f`的作用是一样的。

####查看容器内进程

	$ sudo docker top nostalgic_morse
	PID                 USER                COMMAND
	854                 root                python app.py
	
####注入容器

	sudo docker inspect nostalgic_morse
	
通过该命令可以注入到容器中，返回一个json格式的信息。

	[{
    	"ID": 	"bc533791f3f500b280a9626688bc79e342e3ea0d528efe3a86a51ecb28ea20",
    	"Created": "2014-05-26T05:52:40.808952951Z",
	    "Path": "python",
    	"Args": [
	       "app.py"
	    ],
    	"Config": {
	       "Hostname": "bc533791f3f5",
	       "Domainname": "",
	       "User": "",
	. . .

当然也可以指定查询某个特定的信息
	
	$ sudo docker inspect -f '{{ .NetworkSettings.IPAddress }}' nostalgic_morse
	172.17.0.5
	
####停止容器

	$ sudo docker stop nostalgic_morse
	
####重启容器
	
	sudo docker start nostalgic_morse
也可以用

	docker restart
####删掉容器

	$ sudo docker rm nostalgic_morse
	Error: Impossible to remove a running container, please stop it first or use -f
	2014/05/24 08:12:56 Error: failed to remove one or more containers
如果一个容器没用了，可以删掉。但是必须先停止它，才能删掉它。

