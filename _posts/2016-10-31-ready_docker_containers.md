---
layout: post
title: "Riddler助力Docker容器为runC运行环境做准备"
date: 2016-10-31 11:26:02
category: docker
---

这是一个关于标准化带来的优势的故事，同时介绍如何利用[Riddler](https://github.com/jfrazelle/riddler)转换一个Docker容器为runC镜像。Riddler由容器开发者[Jess Frazelle](https://github.com/jfrazelle)研发。

[Phil Estes](https://github.com/estesp) 是IBM开放云技术的高级技术经理，他将在本周多伦多的[LinuxCon](http://www.thenewstack.io/tag/Linuxcon-North-America-2016)会议上介绍Riddler的性能。

###Run， Run， Run

回顾开放容器，runC是一个开源引擎和规范运行容器，它遵守[OCI的规范](https://www.opencontainers.org/),包括Docker在内。[runC](https://github.com/opencontainers/runc)的思想配合远景规划,是为任何供应商控制或云栈提供一个免费开源容器。它是基于Docker的LibContainer，作为与操作系统交互的接口。它是Docker公司自己的核心容器引擎。

runC可以运行Docker的镜像。它依赖两个组成部分。一个是一组配置指令。这些命令行标志通常追加在Docker运行命令后面；而runC从**config.json**配置文件中读取配置信息。可以手工创建一个,或者runC将自动创建一个。

RunC还需要根文件系统,使它可以操作容器。用户可以手动创建一个目录,作为容器的根文件系统或者文件系统可以利用**docker export**命令从容器中导出。

埃斯蒂斯说："通过上述两块内容,runC可以执行一个容器。”

###But Why？

很多场景下，操作runC可能是比操作全功能的容器（如Docker）更合适,埃斯蒂斯解释说。

它可以提供容器移植到新环境的基础——比如[Solaris Zones](Solaris Zones)，或者用于创建新OCI引擎或平台兼容的容器。例如，英特尔的[Clear Containers](http://thenewstack.io/tag/clear-containers/)和[Hyper.sh](https://hyper.sh/)均是基于runC实现。

RunC在尝试新功能,如检查点恢复。Docker的[seccomp](http://thenewstack.io/docker-engine-hardened-secure-computing-nodes-user-namespaces/)是在Docker1.10中引入的。

Estes说：“RunC就是那种可以在高层开发这种能力的工具。”

开发者同样可以发现runC的便利，它提供一个简洁明了的接口便于快速迭代文件系统和配置中的变更。于是，他还说“我从不担心存储或者后端问题。”

**进入Riddler**

图

虽然runC可以创建容器配置（“[runc spec > config.json](https://runc.io/)”），但是该文件只有基本配置，通常需要再加入各种环境变量。如何让Docker自动获取这些信息呢？

Riddler让用户可以仅通过运行一个容器就可以为容器创建一个全信息配置文件，同时让Riddler从Docker引擎中画出必要的细节内容。Ridder准确的创建出刚运行的容器的配置文件。Estes介绍说：“是否有挂载的卷、是否采用只读文件系统、是否采用了命名空间，这些信息均会被复制到runC的配置文件中”。

Riddler同样会增加一些默认配置。例如，seccomp配置是根据Docker的多种假设，例如不允许系统调用。它同样获取docker run命令附加的参数。在Estes的例子中，他运行了`date`命令来演示这些参数也会被复制。

其他特性必须手工设定。网络并不是OCI管理，于是这些设定必须手工输入。Riddler提供hook到netns，它使得用户可以调整网络配置。用户还需要为容器创建文件系统。对于这部分，Estes创建了一个脚本用于匹配Riddler获取的用户网络命名空间信息。

在这些步骤之后，“我们拥有了一个非常类似简单Docker运行的环境。”

视频地址点击[此处](https://youtu.be/u0j4qPJy1iM)。

###解释

作为如何使用该工具的例子，Estes描述了如何用[NGINX](http://www.thenewstack.io/tag/NGINX)复制一个新运行的容器。他采用`-d`标志来启动软件到后台模式。他还运行了一个容器启动Lynx文本浏览器。

在这个场景下，对开发者来说runC可以简化很多事情。开发者可以容易的修改运行中容器的配置。容器可以访问开发者目录中的其他文件夹。

Estes说：“在开发模式，简便在工作中非常有用”

runC同样提供了与[Linux Capabilities](http://man7.org/linux/man-pages/man7/capabilities.7.html)相互协作，它有一系列的系统调用或者资源操作与超级管理员权限。例如Docker，它有一系列的Linux Capabilities设置。通过这些配置，runC提供一系列的设定，便于开发者修改。

例如，`CAP_NET_RAW`设定控制用户执行低级别TCP函数的能力，包括使用`ping`命令执行Internet Control Message Protocol (ICMP)。快速演示表明,开始时ping确实可以工作,之后Estes做出了改变,它就不再工作了。同样，`CAP_SYS_ADMIN`可以修改为允许或者不允许用户读取和配置宿主机名等功能。










