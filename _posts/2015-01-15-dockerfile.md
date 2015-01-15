---
layout: post
title:Docker入门之如何用Dockerfile
category: docker
---

Docker可以利用dockerfile来创建一个镜像，dockerfile里包含了它所需要的一系列的指令，当然要有个基镜像。

当有了dockerfile之后，就可以用 `docker build` 来创建自己的镜像了

首先来学习下Dockerfile的基本命令

####build

在一个包含Dockerfile的文件夹内执行`sudo docker build .`就可以创建镜像了。

需要注意的是，这个命令会把当前文件夹内的所有文件包括子文件夹都传递给docker程序，于是，最好的办法是**新建一个空文件夹，然后写Dockerfile，然后再执行该命令**。

在创建镜像时可以通过 `-t xxx`来给该镜像打个标签，例如`sudo docker build -t omartech/ubuntu-clean`

每一条语句在Dockerfile中都是独立的，也就是说，如果第一条语句写`RUN cd /tmp`,对第二条语句是**没有作用**的。


####指令

接下来是Dockerfile的命令组成部分，该文件中的命令均是

	#Comment
	INSTRUCTION arguments
这种格式，其中INSTRUCTION部分不区分大小写，但是为了避免出错，建议都用大写。而且命令的第一条必须是`FROM xxxxx`用于标示该镜像来源自哪个基镜像。

1.可以用`ENV`来定义环境变量

	FROM busybox
	ENV foo /bar
	WORKDIR ${foo}   # WORKDIR /bar
	ADD . $foo       # ADD . /bar
	COPY \$foo /quux # COPY $foo /quux

2.可以操作环境变量的指令有

* ENV
* ADD
* COPY
* WORKDIR
* EXPOSE
* VOLUME
* USER


####.dockerignore文件

由于docker build 会把当前路径下所有文件和子文件夹都上传给docker，所以用.dockerignore来过滤掉一些目录，跟.gitignore一样的用法。

####FROM

	FROM <image>
OR

	FROM <image>:<tag>

都可以用，例如`FROM ubuntu` 和 `FROM docker.cn/docker/ubuntu:14.04.1`

如果构建的时候没有传递`tag`给它，默认用`latest`做为tag

如果需要创建多个镜像，FROM可以在Dockerfile中出现多次的

####MAINTAINER

这个命令用来写自己的名字，是谁创建的这个镜像

	MAINTAINER <name>


####RUN

RUN命令是用来做具体的操作，它有两种形式

	RUN <command> (the command is run in a shell - /bin/sh -c - shell form)
	RUN ["executable", "param1", "param2"] (exec form)

RUN命令执行完之后会被commit，就跟源代码管理一样，会有个commit的编号，方便后续从任何一个命令之后checkout出来。

RUN命令会带cache，例如执行	`RUN apt-get dist-upgrade -y`, 如果不想用cache，可以这样`docker build --no-cache`

####CMD

CMD命令有三种格式

	CMD ["executable","param1","param2"] (exec form, this is the preferred form)
	CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
	CMD command param1 param2 (shell form)
	
但是，CMD只能在Dockerfile中出现一次，如果你写了多次，也只有最后一个生效

CMD主要是是用来启动该容器下需要执行的那个程序，作为main入口用。

如果CMD执行的命令式shell，那么会默认用`/bin/sh -c`
例如：
	
	FROM ubuntu
	CMD echo "This is a test." | wc -
	
如果不想用shell执行，需要写成json格式

	FROM ubuntu
	CMD ["/usr/bin/wc","--help"]

RUN跟CMD的区别：RUN是确实在执行，而且会提交；CMD只是告诉docker哪儿是入口，需要执行什么

####EXPOSE
	EXPOSE <port> [<port>...]

EXPOSE是用来指定开放端口的，也就是说它来说明该docker会监听在port这个端口，方便多个docker之间进行通信和调用。

它并不是跟host机器交互的端口，这个端口需要在启动`docker run`的时候用`-p`来指定。

####ENV
	ENV <key> <value>
	ENV <key>=<value> ...
	
ENV就是用来set环境变量的，这些变量可以在RUN命令中继续使用，`${key}` 即可。

例如：
	
	ENV myName="John Doe" myDog=Rex\ The\ Dog \
    myCat=fluffy
    ENV myName John Doe
	ENV myDog Rex The Dog
	ENV myCat fluffy

可以用`docker inspect`来查看该镜像有哪些环境变量，也可以用`docker run --env <key>=<value>`来重写环境变量

####ADD

	ADD <src>... <dest>

方便把别的文件拷入该镜像，src部分要写跟当前路径的相对路径，而dest肯定是写绝对路径。

####COPY
	COPY <src>... <dest>
似乎跟ADD一样

####ENTRYPOINT
	ENTRYPOINT ["executable", "param1", "param2"] (the preferred exec form)
	ENTRYPOINT command param1 param2 (shell form)

ENTRYPOINT命令可以帮忙把一个镜像配置为可运行的程序。
例如：

	docker run -i -t --rm -p 80:80 nginx
####VOLUME
	VOLUME ["/data"]
用VOLUME命令可以将docker内的文件存放在host机器上的目录中。
也可以创建多个，便于多个docker共享同一个文件夹。	

	VOLUME ["/var/www", "/var/log/apache2", "/etc/apache2"]
####USER
	USER daemon
通过设定USER来指定执行RUN命令的用户

####WORKDIR
	WORKDIR /path/to/workdir

WORKDIR可以用来帮忙指定RUN，CMD，ENTRYPOINT的工作目录，不用纠结RUN中各命令独立了。
而且，WORKDIR是上下文依赖的。
	
	WORKDIR /a
	WORKDIR b
	WORKDIR c
	RUN pwd
最终的结果是 `/a/b/c`

而且WORKDIR可以使用环境变量

	ENV DIRPATH /path
	WORKDIR $DIRPATH/$DIRNAME
	
####ONBUILD

	ONBUILD [INSTRUCTION]
	
当别人用这个镜像当基镜像时，这个命令才有用




















