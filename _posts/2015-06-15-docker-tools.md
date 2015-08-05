---
layout: post
title: "在Eclipse中使用Docker Tools入门"
category: docker
---



Docker tooling旨在提供一个至少相同基本功能的命令行界面，同时通过提供一个完整成熟的UI来提供更多优于命令行的功能。

###安装Docker Tools插件

* 下载并安装[JBoss Developer Studio 9.0 Nightly](https://devstudio.redhat.com/9.0/snapshots/builds/devstudio.product_master/latest/installer/)，默认设置安装即可。也可以下载[Eclipse Mars latest build](http://www.eclipse.org/downloads/index-developer-default.php)并通过[http://download.jboss.org/jbosstools/updates/nightly/mars/](http://download.jboss.org/jbosstools/updates/nightly/mars/)配置JBoss Tools插件。
* 打开JBoss Developer Studio 9.0 Nightly或者Eclipse Mars
* 通过菜单项添加新的站点，步骤是：Help > Install New Software… > Add…。注明名字为：Docker Nightly，地址为：http://download.eclipse.org/linuxtools/updates-docker-nightly/。
<img src="/images/jbds-docker-tools1.jpg" style="width:100%" />
* 展开Linux Tools，选择Docker Client和Docker Tooling
<img src="/images/jbds-docker-tools2.jpg" style="width:100%" />
* 一路点击Next，然后同意它的协议，然后点击Finish。这就完成了插件的安装，重启IDE即可生效。

###Docker Explorer

Docker Explorer提供了一个向导来简历一个连接到Docker daemon。这个向导可以检测Docker的默认设置，无论是用户机器上原生安装的Docker还是利用Boot2Docker启动的虚拟机上安装的Docker。Linux机器上的Unix sockets和其他操作系统上的REST API都可以被检测。这个向导同样允许自定义设置来建立远程连接。

* 使用Window菜单，打开视图，然后输入Docker，就可以看到下图：
<img src="/images/jbds-docker-tools3.jpg" style="width:100%" />
* 选择Docker Explorer来打开资源区
<img src="/images/jbds-docker-tools4.jpg" style="width:100%" />
* 按照下图所示创建一个连接到Docker Host
<img src="/images/jbds-docker-tools5.jpg" style="width:100%" />
通过`docker-machine ip`来确保获取到Docker Host的IP地址，同样设置正确的`.docker`目录。
* 点击Test Connection来测试连接，如下图：
<img src="/images/jbds-docker-tools6.jpg" style="width:100%" />
点击OK和Finish来结束向导。
* Docker Explorer它自身有一个树状展示来显示多个连接，同时提供给用户现存的镜像和容器。
<img src="/images/jbds-docker-tools7.jpg" style="width:100%" />
* 通过点击工具栏上的箭头可以自定义视图：
<img src="/images/jbds-docker-tools8.jpg" style="width:100%" />
* 内置的筛选器可以展示或隐藏各类镜像和容器：
<img src="/images/jbds-docker-tools9.jpg" style="width:100%" />

###Docker镜像

在Docker Explorer视图中Docker Images会列出Docker Host上的所有镜像。这个视图允许用户来管理镜像，包括下列操作：

* 从Docker Hub Registry上拉取镜像或者上传镜像（其他仓库也已经支持）
* 通过Dockerfile来创建镜像
* 从一个镜像中创建容器

具体如下：

* 依次点击Window菜单、Show View、Other...，选择Docker Images。它会列出Docker Host上的所有镜像：
<img src="/images/jbds-docker-tools10.jpg" style="width:100%" />
* 右键点击wildy:latest，并点击工具栏上的绿色箭头，它会如下显示：
<img src="/images/jbds-docker-tools11.jpg" style="width:100%" />
* 默认情况下，所有从镜像中暴露的端口都会被随机映射到宿主机接口。这个设置可以通过反选第一个复选框并指定端口映射来更改。点击Finish来启动容器。
* 当容器启动起来时，Eclipse的终端如下显示：
<img src="/images/jbds-docker-tools12.jpg" style="width:100%" />

###Docker容器

Docker Container视图使得用户可以管理容器。这个试图工具栏提供了启动、停止、暂停、恢复、显示日志和杀死容器的操作。

* 依次点击Window、Show View、Other...，选择Docker Container。它如下显示：
<img src="/images/jbds-docker-tools13.jpg" style="width:100%" />
* 通过点击工具栏上的暂停按钮可以暂停容器。点击View Menu可以显示全部容器列表。
<img src="/images/jbds-docker-tools14.jpg" style="width:100%" />
* 选择已暂停的容器，可以点击绿色箭头来恢复容器
* 右键点击正在运行的容器可以查看运行日志。
<img src="/images/jbds-docker-tools15.jpg" style="width:100%" />

###查看检查镜像与容器

Eclipse Properties视图用来提供容器和镜像的一些信息。

* 打开Properties View并在Docker Explorer、Docker Container或者Docker Images视图中点击任何一个连接、容器或者镜像。Properties视图就会有相应的信息。
* 视图如下显示：
<img src="/images/jbds-docker-tools16.jpg" style="width:100%" />
* 检查视图如下：
<img src="/images/jbds-docker-tools17.jpg" style="width:100%" />

这些代码在[Linux Tools](http://www.eclipse.org/linuxtools/)项目中。

在[bugs.eclipse.org/bugs/enter_bug.cgi?product=Linux%20Tools](https://bugs.eclipse.org/bugs/enter_bug.cgi?product=Linux%20Tools)中记录你发现的bug。
可以在[IRC](irc://irc.freenode.org/jbosstools)上与我们沟通。

**原文：[Getting Started with Docker Tools in Eclipse](https://www.voxxed.com/blog/2015/06/docker-tools-in-eclipse/) 译者：陈杰**

===============================================
**译者介绍**
**陈杰**，北京理工大学计算机学院在读博士，研究方向是自然语言处理在企业网络信誉评价方面的应用，平时也乐于去实现一些突发的想法。在疲于配置系统环境时发现了Docker，跟大家一起学习、使用和研究Docker。



