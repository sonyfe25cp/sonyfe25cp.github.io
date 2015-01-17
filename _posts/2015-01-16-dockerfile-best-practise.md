---
layout: post
title: "Docker入门之Dockerfile最佳实践"
category: docker
---

本文为Dockerfile的最佳实践，如果连`Dockerfile`是什么都不知道，就先看前面那篇[Dockerfile入门](http://sonyfe25cp.github.io/xxxxx)吧。

####常规方法和建议

**容器必须ephemeral**

所谓的ephemeral，就是指该容器可以被停止、销毁，并且当新创建一个的时候，它也是最小规模的配置。
其实就是说跟容器无关的东西不要放进去，也不要关联些没用的东西，要保持原子性。

**用.dockignore**

为了避免没用的文件或者文件夹被添加进容器，用.dockerignore来减少容器大小，如.git文件夹。

**不要装没用的包**

跟第一条一样，没必要的不要放进来。例如一个装mysql的容器，就没必要装vim

**尽量少的layer在dockerfile中**

简洁明了的写完各种命令

**对各种参数要排个序**

当一个命令带一堆参数时，尽量换行来写，通过`\`就可以换行了。如：

	RUN apt-get update && apt-get install -y \
	bzr \
    cvs \
    git \
    mercurial \
    subversion

**Build cache**

在创建新镜像时，docker在执行每一行命令时会默认去搜寻本地镜像，来看看是否有可以用的cache。如果不允许使用cache，可以用`--no-cache=true`来停止使用cache，但是需要明确什么时候会用cache，什么时候不用cache。

* Starting with a base image that is already in the cache, the next instruction is compared against all child images derived from that base image to see if one of them was built using the exact same instruction. If not, the cache is invalidated.
* In most cases simply comparing the instruction in the Dockerfile with one of the child images is sufficient. However, certain instructions require a little more examination and explanation.

* In the case of the ADD and COPY instructions, the contents of the file(s) being put into the image are examined. Specifically, a checksum is done of the file(s) and then that checksum is used during the cache lookup. If anything has changed in the file(s), including its metadata, then the cache is invalidated.

* Aside from the ADD and COPY commands cache checking will not look at the files in the container to determine a cache match. For example, when processing a RUN apt-get -y update command the files updated in the container will not be examined to determine if a cache hit exists. In that case just the command string itself will be used to find a match.

####Dockerfile 指令

**FROM**

只要可能，就用官方的原始镜像来创建。并且推荐用`Debian`，因为这个镜像最小，全版本也不到100M.

**RUN**

尽可能的让RUN命令易读易懂可控，且让长命令分行。

一般来说RUN中最常用的就是安装各种包的`apt-get`命令了，下面是几条相关的建议：

* 不要`RUN apt-get update`写在一行里，因为这可能导致相关联的几个包更新后，而接下来的几条`apt-get install`命令安装失败且没有提示。
* 不要用`RUN apt-get upgrade`或者`dist-upgrade`，因为这会导致内部包更新失败，如果非要更新某个包，可以用`apt-get install -y foo`来强制更新。
* 一定这样写`RUN apt-get update && apt-get install -y package-bar package-foo package-baz`
因为这样写，不但清晰易读，也能保证安装的一定是最新的包。
* 如果非要指定某个软件的版本，可以添加`package-foo=1.3`这样。

下面是个很好的例子：

	RUN apt-get update && apt-get install -y \
    	aufs-tools \
	    automake \
    	btrfs-tools \
	    build-essential \
    	curl \
    	dpkg-sig \
    	git \
    	iptables \
    	libapparmor-dev \
	    libcap-dev \
	    libsqlite3-dev \
	    lxc=1.0* \
    	mercurial \
	    parallel \
	    reprepro \
    	ruby1.9.1 \
	    ruby1.9.1-dev \
    	s3cmd=1.1.0* 

**CMD**

CMD命令是用来执行容器内的软件的，尽量用`CMD [“executable”, “param1”, “param2”…]`这种形式。

如果容器运行的是`Apache`这样的服务型软件，推荐这样`CMD ["apache2","-DFOREGROUND"]`。

如果是其他情况，CMD应该跟交互性终端相连，如bash, python, perl这些。例如`CMD ["perl", "-de0"], CMD ["python"], or CMD [“php”, “-a”].`。这样当你运行`docker run -it python`的时候，就直接可以进入到一个python的交互终端里了。

尽量少的用`CMD [“param”, “param”]`这种形式来跟`ENTRYPOINT`搭配使用，除非你非常熟悉`ENTRYPOINT`。

**EXPOSE**

这个命令就是让容器内部在监听某个端口，建议就监听在这个软件正常的端口上。例如这是个Apache的容器，那就监听在80端口；如果这是个mongoDB容器，就监听在27017端口。

对于外部程序调用端口，执行`docker run`时可以指定端口映射；对于容器间的相互调用，可以通过各种环境变量来调用。

**ENV**

为了方便安装后的软件在容器启动之后可以执行，可以通过ENV命令来把安装路径添加到PATH中，使用方法：`ENV PATH /usr/local/nginx/bin:$PATH`，可以确保`CMD [“nginx”]`可以执行。

在处理特殊的环境变量时候，也是很有用的，如`JAVA_HOME`这种。

同样在处理不同软件的版本号的时候，也很有用处。例如：
	
	ENV PG_MAJOR 9.3
	ENV PG_VERSION 9.3.4
	RUN curl -SL http://example.com/postgres-$PG_VERSION.tar.xz | tar -xJC /		usr/src/postgress && …
	ENV PATH /usr/local/postgres-$PG_MAJOR/bin:$PATH

**ADD和COPY**

虽然这俩命令很像，但是推荐用`COPY`,因为COPY更加的透明，它只支持本地文件的拷贝，而ADD支持本地tar包的解压和远程文件的拷贝，而这些也不容易debug。

但是ADD在解压的时候很好用，如`ADD rootfs.tar.xz /`

如果Dockerfile中有很多步拷贝文件，建议分开拷贝，确保build cache也是分开的。例如：
	
	COPY requirements.txt /tmp/
	RUN pip install /tmp/requirements.txt
	COPY . /tmp/
为了让镜像足够小，应该尽量避免用ADD来下载安装文件，例如，*请不要这样*：
	
	ADD http://example.com/big.tar.xz /usr/src/things/
	RUN tar -xJf /usr/src/things/big.tar.xz -C /usr/src/things
	RUN make -C /usr/src/things all
而，应该这样：

	RUN mkdir -p /usr/src/things \
    	&& curl -SL http://example.com/big.tar.gz \
	    | tar -xJC /usr/src/things \
    	&& make -C /usr/src/things all

对于其他不需要解压的文件，应该全用COPY命令，而不是ADD.

**ENTRYPOINT**

ENTRYPOINT应该用来执行一些辅助性的脚本。其他用途只会让这个代码更加难懂。

例如`docker run -it official-image bash`绝对比`docker run -it --entrypoint bash official-image -i`容易懂的多。

>额，其他情况好复杂，反正能不用就不用吧。。以后再看这段，实在太难懂。

**VOLUME**

VOLUME这个命令可以用来存储数据库文件、配置文件或者是该容器产生的某些文件夹。

强烈推荐对某些可变的或者是服务型的部分用VOLUME命令。

**USER**

如果你的程序在任何用户权限下都可以用，可以用USER来切换用户。可以通过在Dockerfile中创建用户和组来解决,`RUN groupadd -r postgres && useradd -r -g postgres postgres`

避免使用`sudo`命令，这会引入各种奇怪问题。同样不要用USER来回切换用户。

**WORKDIR**

可以用该命令来切换工作目录，避免总要写路径

**ONBUILD**

这个命令只有从某个指定镜像创建新镜像时候才有用。 For example, you would use ONBUILD for a language stack image that builds arbitrary user software written in that language within the Dockerfile, as you can see in Ruby’s ONBUILD variants.

用ONBUILD创建的镜像应该带个额外的标签，如`ruby:1.9-onbuild or ruby:2.0-onbuild`.

在ONBUILD中要慎用ADD,COPY。因为如果找不到所需要的资源文件，就会报错。


其他内容继续看[原版](https://docs.docker.com/articles/dockerfile_best-practices/)吧。



