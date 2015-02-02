---
layout: post
title: "Docker中latest标签带来的困惑"
category: docker
---



在Docker中，最容易产生误解的部分应该是`latest`这个标签。困惑主要是由于这个名字造成的，因为字面意思并不能表达它的真正含义。在本文种，我们来学习下`latest`标签的真正作用和如何正确使用它。

通常有两种方式来对镜像打标签：使用`docker tag`命令或者是在执行`docker build`的时候用`-t`来传递参数。在这两种情况下，参数的形式通常是`repository_name:tag_name`，例如:`docker tag myrepo:mytag`.如果这个资源库被上传到了Docker Hub，资源库的名字会被加上一个由Docker Hub用户名和斜线组成的前缀，例如：`amouat/myrepo:mytag`。如果没有添加`tag`部分的参数，例如：`docker tag myrepo:1.0 myrepo`，Docker会自动的给它`latest	`标签。前面这些内容或许你已经熟知，但重要的是要认识到这就是它所作的工作，并没有其他神奇的地方。

不能因为镜像的标签是`latest`就认为这是资源库中最新的镜像。只有这个资源库的拥有者约定这样，拥有`latest`标签的镜像才一定是最新的镜像。例如，我可以轻易地把一个过时的镜像变成带有`latest`标签的镜像，例如：

	$ docker images myrepo
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	myrepo              1.0                 2e9f372f03a0        44 seconds ago      2.433 MB
	myrepo              latest              2e9f372f03a0        44 seconds ago      2.433 MB
	myrepo              0.9                 4986bf8c1536        2 weeks ago         2.433 MB
	$ docker tag -f myrepo:0.9 myrepo:latest
	$ docker images myrepo
	REPOSITORY          TAG                 IMAGE ID            CREATED              VIRTUAL SIZE
	myrepo              1.0                 2e9f372f03a0        About a minute ago   2.433 MB
	myrepo              0.9                 4986bf8c1536        2 weeks ago          2.433 MB
	myrepo              latest              4986bf8c1536        2 weeks ago          2.433 MB
	
这里带`latest`标签的镜像与0.9版本的镜像时一样的，都是两周前的版本，然而1.0的镜像是一分钟以前的。

为什么这个标签让很多人迷惑，其实比较容易理解。‘just pull the latest image’ 这句话的意思是获取带有latest标签的镜像还是获取最新的镜像？这两者是否是一样的呢？它们是不是资源库中最新的镜像呢？是不是最新的稳定版镜像或者是最新的开发版镜像呢？

更糟糕的是，很多人似乎认为`latest`标签会自动更新，也就是说如果我获取一个带有`latest`标签的镜像，docker会在每次运行之前去检查它是不是最新的版本。这是绝对不会出现的情况，就像其他的标签一样，你需要去手工决定docker获取最新版本的镜像。

困惑并不仅仅是这些。如果我从资源库docker pull一个镜像却没指定标签，会发生什么呢？如果你认为会获取下所有的镜像，那么就错了，它只会获取下来带有`latest`标签的那个。如果你需要获取全部镜像，需要加上`-a`标志。 如果你在资源库执行了pull操作，却没带`latest`标签，会发生什么呢？如下所示：

	$ docker pull amouat/myrepo
	Pulling repository amouat/myrepo
	2015/01/21 12:04:06 Tag latest not found in repository amouat/myrepo

意料之中的是docker给出了错误信息。但是我认为你不知道这其中发生了什么。

一个更令人讨厌的是`latest`标签隐藏了其他的标签，假设你要下载带`latest`标签的debian镜像。哪个是它的版本呢？

	$ docker images debian
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	debian              latest              4d6ce913b130        4 days ago          84.98 MB
	
额，不知道。事实上是7.8 wheezy版本。

	$ docker pull debian:7.8
	debian:7.8: The image you are pulling has been verified
	511136ea3c5a: Already exists
	d0a18d3b84de: Already exists
	4d6ce913b130: Already exists
	Status: Image is up to date for debian:7.8
	$ docker pull debian:wheezy
	debian:wheezy: The image you are pulling has been verified
	511136ea3c5a: Already exists
	d0a18d3b84de: Already exists
	4d6ce913b130: Already exists
	Status: Image is up to date for debian:wheezy
	$ docker images debian
	REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
	debian              7.8                 4d6ce913b130        4 days ago          84.98 MB
	debian              latest              4d6ce913b130        4 days ago          84.98 MB
	debian              wheezy              4d6ce913b130        4 days ago          84.98 MB

我认为Docker在下载镜像时应该把所有的标签都带上，但是我不知道为什么它没有这么做。现在的情况是用户可以拥有同一个镜像的不同版本因为服务器上用标签来标示。例如：如果`wheezy`和`latest`都在Hub上更新了，而我只获取了更新后的`wheezy`版本debian，那么尽管在Hub上他们可以被区分开，但是我的`wheezy`标签将会比本地的`latest`标签的版本新。

上述只是覆盖了`latest`的大部分语义以及它造成的常见误解。这种情况怎么能够改善呢？个人认为，可以取消`latest`标签并用一个更接近其字面意思的词来代替，例如`default`。我也希望可以看到一些改进标签原作方式的工作，例如同时更新一个镜像的全部标签。与此同时，我也强烈建议资源库管理员去警惕这个`latest`标签并彻底废弃它。

原文链接：[Docker: The latest Confusion](http://container-solutions.com/2015/01/docker-latest-confusion/)（翻译：陈杰)


	



































