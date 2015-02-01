---
layout: post
title: "《Moving to Docker》系列之二：搭建一个私有registry服务"
category: docker

---

【译者的话】本文是《Moving to Docker》系列的第二篇，这个系列的文章讲述了创业公司如何把基础服务迁移到Docker上，以及迁移过程中的经验教训。本文主要介绍了如何安装、测试和使用私有registry服务，其中也包含了从DigitalOcean选VPS和注册Amazon S3服务。

这是迁移到Docker系列的第二篇，整个系列都是介绍我们公司是如何把基础设施从PaaS迁移到Docker的。

* [第一篇](http://dockerone.com/article/169)：我会介绍在使用Docker之前我们的处理过程。
* 第三篇：我会介绍如何自动化构建整个镜像的过程以及如何用Docker部署Rails应用。

为什么我们想要搭建一个私有的registry？对于初学者，Docker Hub只允许你有一个免费的私有库。虽然其他公司也开始提供类似的服务，但是价格也都不便宜。并且，如果你需要部署基于Docker的生产环境，你也不想将这些镜像发布到公开的Docker Hub。

这是一个非常务实的方法来处理搭建私有Docker registry的复杂过程。在这个教程中，我们会用一个512M的DigitalOcean的机器。你需要具备Docker的基础知识，因为我会引入一些复杂的东西。

###本地搭建

首先，你需要安装`boot2docker`和`docker CLI`.如果你已经安装了Docker的环境并运行着，可以跳过这一节了。

从终端运行下面的命令

	brew install boot2docker docker
	
如果这一步安装成功，就可以启动一个共Docker运行在里面的虚拟机，命令如下：

	boot2docker up

根据说明，使用`export`命令，boot2docker会输出在终端里。如果你现在运行`docker ps`，你就可以看到下面的结果:

	CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES  

现在，Docker已经安装好了，下一步我们开始安装registry。

###创建服务器

登陆你的DigitalOcean账号，并创建一个预安装了Docker镜像的Droplet，如下图：

--->image,s1

你会在email中收到root用户的登录凭证。登陆并运行`docker ps`来确认机器可以正常工作。

###创建一个亚马逊AWS S3

我们准备去使用亚马逊的简单存储服务（S3）作为我们registry的物理存储。下面我们需要创建一个bucket和用户凭证来让docker容器使用它。

登陆AWS账号（如果你没有，可以在[http://aws.amazon.com/](http://aws.amazon.com)注册一个），然后选择S3服务，如下图：

image,s2

点击创建Bucket，输入该bucket的唯一码（记下来，后面还要用到），然后点击生成，如下图：

image s3

我们已经完成了创建存储的部分。

###安装AWS登陆凭证
接下来我们将创建一个新用户。回到AWS的控制台界面，选择IAM（Identity & Access Management）。
image s4

你会看到下面这个界面：

image s5

                           
用户需要授权密钥来启用通过REST或者Query协议来连接AWS的服务API。如果需要连接AWS的管理终端，在完成这个向导之后在用户面板创建一个密码就行。

	Enter a name for your user (e.g. docker-registry) and click on Create. Write down (or download the csv file with) your Access Key and Secret Access Key that we'll need when running the Docker container. Go back to your users list and select the one you just created.

	Under the *Permission section*, click on Attach User Policy. In the next screen, you will be presented with multiple choices: select Custom Policy.

###设置权限
选择一个策略模板，创建一个策略或者自定义一个策略。策略是一个文档，它包含一个或者多个权限，可以通过下面这个界面来修改策略或者通过用户、组和角色页面来修改。

image s7

下面是一个自定义的策略：
	
	{
		"Version": "2012-10-17",
		"Statement": [
    	{
      	"Sid": "SomeStatement",
      	"Effect": "Allow",
      	"Action": [
        	"s3:*"
			],
      	"Resource": [
        	"arn:aws:s3:::docker-registry-bucket-name/*",       
        	"arn:aws:s3:::docker-registry-bucket-name"
      		]
    	}
		]
	}

这样可以允许用户（例如：registry）在bucket上操作（当创建AWS S3之前确认bucket已经创建完成）。简而言之：当你要准备把Docker镜像从本地机器发布到自己的库里时，服务器将会把它们保存在S3上。

###安装registry

接下来，用SSH连接到DigitalOcean的虚拟机上。我们准备使用[官方提供的registry镜像](https://registry.hub.docker.com/_/registry/)。

用下面的命令启动registry：

	docker run \  
         -e SETTINGS_FLAVOR=s3 \
         -e AWS_BUCKET=bucket-name \
         -e STORAGE_PATH=/registry \
         -e AWS_KEY=your_aws_key \
         -e AWS_SECRET=your_aws_secret \
         -e SEARCH_BACKEND=sqlalchemy \
         -p 5000:5000 \
         --name registry \
         -d \
         registry

Docker会从Docker Hub上下载相应的fs层并启动守护进程容器。

###测试registry

如果前面都顺利的话，接下来可以通过`ping`测试registry的服务并搜索它内部的内容（尽管现在它还是空的）。

我们的registry是个非常基本的安装，它并不提供任何权限上的控制。因为添加权限的方法都比较麻烦（至少我没有发现哪个方法可以容易的实现并确认权限控制），我决定用最简单的方法来查询、下载和发布来使用registry，那就是利用ssh在HTTP协议上做一个不加密的隧道连接。

通过下面的命令可以从你的机器创建一个隧道：

	ssh -N -L 5000:localhost:5000 root@your_registry.com  

这条命令使得服务器（就是刚才`docker run`启动的那个机器）的5000端口到本地的5000端口之间通过SSH协议做了一个隧道。

如果你通过[http://localhost:5000/v1/_ping](http://localhost:5000/v1/_ping)来请求，你会得到一个简单结果：

	{}
	
这说明你的registry已经正常工作了。通过[http://localhost:5000/v1/search](http://localhost:5000/v1/search)可以列出整个registry中的内容，如下：

	{
		"num_results": 2,
		"query": "",
		"results": [
			{
				"description": "",
				"name": "username/first-repo"
			},
			{
      "description": "",
      "name": "username/second-repo"
			}
		]
	}

###创建一个镜像

下面我们尝试创建一个非常简单的镜像来测试我们的registry服务。在本地机器上创建如下内容的Dockerfile：

	# Base image with ruby 2.2.0
	FROM ruby:2.2.0
	MAINTAINER Michelangelo Chasseur <michelangelo.chasseur@touchwa.re>  
	
然后创建它

	docker build -t localhost:5000/username/repo-name .  
	
其中`localhost:5000`部分非常重要：一个Docker镜像的第一部分是告诉`docker push`应该把镜像发布到哪儿。在我们的例子中，因为我们通过SSH连接了我们的私有registy到本地的5000端口，于是`localhost:5000`就是指代我们的registry。

如果运行的正常，当这个命令返回结果后，你可以通过`docker images`来查看新创建的镜像了，请自己尝试一下。

###发布到registry

接下来介绍一些技巧。我也花了一些时间来弄明白自己要介绍的东西，所以如果你第一次没看懂，请保持耐心并尝试。我知道这些东西看起来很复杂（如果你没有自动化这个过程，它确实很复杂），但是我保证它们都是有意义的。接下来的一篇，我会展示一堆shell脚本和Rake任务，它们将把整个过程自动化，方便你用一条简单的命令来部署一个Rails应用到你的registry。

你从本地终端运行的`docker`命令确实是用boot2docker虚拟机来运行容器并且做了所有的操作。于是我们运行一条像`docker push some_repo`的命令时，就是boot2docker 虚拟机在连接registry而不是我们本地的。

这是非常重要的一点：为了发布Docker镜像到远端私有registry，SSH隧道需要被boot2docker虚拟机创建而不是从给你的本机。

这里有几种方法来实现它，我会介绍最简洁的一种（它并不是最容易理解的，却是它可以帮助我们通过shell脚本来自动化这个过程）。

###建立SSH

先来添加boot2docker的SSH密钥到远端registry服务器的known hosts.我们可以通过`ssh-copy-id`功能，它可以用如下命令来安装：
	
	brew install ssh-copy-id
	
然后运行：

	ssh-copy-id -i /Users/username/.ssh/id_boot2docker root@your-registry.com  

请确保`/Users/username/.ssh/id_boot2docker`是你的ssh密钥的正确路径。

然后我们来测试一下：

	boot2docker ssh "ssh -o 'StrictHostKeyChecking no' -i /Users/michelangelo/.ssh/id_boot2docker -N -L 5000:localhost:5000 root@registry.touchwa.re &" &  
	
解释下：

* `boot2docker ssh`可以让你传递一条命令作为参数让boot2docker虚拟机来执行
* 最后的`&`来保证我们的命令在后台运行
* `ssh -o 'StrictHostKeyChecking no' -i /Users/michelangelo/.ssh/id_boot2docker -N -L 5000:localhost:5000 root@registry.touchwa.re &`才是虚拟机真正执行的。
	* `-o 'StrictHostKeyChecking no'`部分可以确保不用回答设置的安全问题
	* `-i /Users/michelangelo/.ssh/id_boot2docker`来说明我们希望虚拟机使用的授权SSH密钥(这个需要是在上一步添加给远端registry服务的那个密钥)。
	* 最后我们打开了一个隧道来连接远端5000端口和本地的5000端口
	
###从别的服务器下载

现在你可以通过下面的命令来实现从远端registry服务发布镜像：
	
	docker push localhost:5000/username/repo_name  
	
在接下来的本系列第三篇，我会介绍如何自动化这些步骤并且我们会把一个实际Rails应用容器化。
请保持关注！

P.S. 请留言关于我的教程中的任何不一致或者错误的地方。希望你会喜欢。

1. 前提是你也在用OS X系统
2. 请参考[http://boot2docker.io/](http://boot2docker.io/)中完整的说明来安装你的docker环境和相关的依赖。
3. 本文的环境选择是*Image>Applications>Docker 1.4.1 on 14.04*。
4. 参考[https://github.com/docker/docker-registry/](https://github.com/docker/docker-registry/).
5. 这只是一小部分，下一篇我会介绍你如何把一个Rails应用打包进一个Docker容器。

**原文链接**：[**Moving to Docker**](http://cocoahunter.com/2015/01/23/docker-2/)

>本文已在[Dockerone.com](http://dockerone.com/article/174)上发表。