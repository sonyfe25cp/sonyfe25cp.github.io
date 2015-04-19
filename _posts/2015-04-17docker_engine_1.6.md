
---
layout: post
title: "Docker 1.6发布：Engine & ORCHESTRATION 更新 & REGINSTRY 2.0 & WINDOWS客户端"
category: docker
---


【编者的话】Docker 1.6版本发布的同时，还有Registry 2.0、Compose、Swarm、Machine，这次的变更很赞，值得一试！

我们很高兴来宣布对Docker Engine、Registry、Compose、Swarm和Machine的更新。我们已经将它们同时发布，可以测试、使用它们了，同时可以承载多个跨工具的特性。这些新的特性通过更快的下拉镜像来提高开发体验、一个Windows的预览版Docker客户端，同时在Compose环境下支持更多的应用。

###Docker Engine 1.6

Docker Engine从1.5版本以来已经有了很大提升，同时带来了新的特性和Windows客户端支持。我们创造这么多令人惊讶的版本离不开很棒的社区贡献者们。你可以从[下载Docker Engine 1.6](http://docs.docker.com/installation/)开始体验，并阅读[变更日志](https://github.com/docker/docker/blob/master/CHANGELOG.md)。

下面让我们来一起看看Docker Engine1.6中有哪些新内容。

####容器和镜像标签

标签使得用户可以附加自定义的元数据到容器和镜像上，这可以通过你的工具来使用。这个特性已经被反复提出一段时间了。社区在设计上达成共识并满足大多数的使用场景，于是它成为1.6的一部分了。

谢谢[Darren Shepherd](https://github.com/ibuildthecloud)，是他贡献的这个补丁。如果你对这个特性非常感兴趣，你可以下拉这个任务并阅读[Darren的博客](http://rancher.com/docker-labels/)来了解标签是如何应用在Rancher中。

####Windows客户端预览版

我们非常幸运的有一些很棒的微软工程师来帮助我们，他们的集体努力使得Docker的Windows客户端能够有一个官方的版本。了解你的软件在一个特定的操作系统上是如何编写，这是非常有意思的事情，我们非常感谢这些Windows专家们在这个过程中指引我们。

Windows客户端同一个远端宿主机一起工作，就像Mac客户端一样。我们甚至扩展了测试框架来适应Windows客户端，每一个PR到Engine。

你可以在这篇[Ahmet Alp Balkan的博客](http://azure.microsoft.com/blog/2015/04/16/docker-client-for-windows-is-now-available)中阅读到更多关于Docker Windows客户度的内容，它是我们一个主要的贡献者，已经提交[70个pull请求](https://github.com/docker/docker/pulls?q=is%3Apr+author%3Aahmetalpbalkan+is%3Aclosed)。

####日志驱动器

已经有持续增长的的需求关于一个日志驱动器API，它使得你可以发送容器日志到其他系统中，如Syslog或者其他第三方工具。这个新的日志驱动器延续了现在Engine中的exec驱动器、存储驱动器的概念。

在`docker run`中增加了一个新的可选项参数`--log-dirver`，它有三个选项：`json-file`(这是个默认选项)、`syslog`和`none`。其中`syslog`驱动补丁只有70行代码，希望这是一个先例，让人们明白未来添加其他补丁到Docker中是多么容易的事情。一定不要忽视`none`，在特定的重度详细的应用中，你可能不关心日志（如irssi），`none`是一个很好的选择。

感谢[Alexander Morozov](https://github.com/lk4d4)的补丁。阅读更多信息，请去查看关于日志驱动器的[下拉请求](https://github.com/docker/docker/pull/10568)，syslog的补丁在[这里](https://github.com/docker/docker/pull/11458)。

####内容定位的镜像标示

以前当你下拉，编译或者运行镜像时，用`namespace/repository:tag`形式来指定他们，或者只有`repository`。在有了[Andy Goldstein]()的补丁之后，你现在可以在下拉、运行、编译时使用一个新的内容定位标示符，它叫做digest，语法是：`namespace/repo@digest`。Digest是一个不可变的引用到镜像中的内容。

使用digest的案例是围绕应用补丁和更新。如果你想要推出一个安全更新，你现在可以指定镜像中特定的digest做安全更新，确保服务器正在运行安全更新。

这个特征旨在v2版本的registry中支持，越多更多信息关于这个补丁，参考[下拉请求](https://github.com/docker/docker/pull/11109)。你也可以阅读[文档](http://docs.docker.com/reference/commandline/cli/#pull)。

####--cgroup-parent

容器由命名空间、功能和cgroups组成。Docker已经支持了自定义的命名空间和功能。另外，在这个版本中我们已经增加了对自定义cgroups的支持。通过`--cgroup-parent`标签，你可以传递一个特定的cgroup来运行一个容器在里面。这使得你创建和管理cgroups。你可以定义自定义的资源为这些cgroups并且把容器放在一个通用的父组中。

谢谢[Vish Kannan](https://github.com/vishh)的补丁，到[这里](https://github.com/docker/docker/pull/11428)越多更多信息。

####Ulimits

直到现在，容器从docker守护进程中继承ulimit设置。这通常是极高的占用了生产环境负载，而不是在李茜的容器内。Ulimts使得你可以限制一个给定进程的资源（你可能已经熟悉`ulimit`命令）。在这个特征的帮助下，你可以在设置守护线程时指定默认的`ulimit`设置给所有的容器。例如：
	
	docker -d --default-ulimit nproc=1024:2048
	
这将设置一个软限制1024和一个硬限制2048子线程为所有的容器。你可以设置这个可选项多次，例如：

	--default-ulimit nproc=1024:2408 --default-ulimit nofile=100:200

当这样创建一个容器时，这些设置会被覆盖：

	docker run -d --ulimit nproc=2048:4096 httpd
	
这会覆盖默认的`nproc`值。

感谢[Brian Goff](https://github.com/cpuguy83)的补丁。感兴趣的话，请阅读最初的[下拉请求](https://github.com/docker/docker/pull/9437)。

####Dockerfile说明在提交和导入时可以被使用

在Docker Engine1.6的惊艳特征中，最后但并非最不重要的更新是使得对镜像的变更可以on the fly，而不需要重新编译整个镜像。新特征`commit --change`和`import --change`使得你可以指定标准变更来应用到新的镜像。这些添加在Dockerfile语法并用于修改镜像。相应的Dockerfile说明在`docker commit`和`docker import`中罗列，文档在[这里](http://docs.docker.com/reference/commandline/cli/#commit)。

感谢[Dan Walsh](https://github.com/rhatdan)的补丁，如果你想了解更多内容，请阅读[下拉请求](https://github.com/docker/docker/pull/9123)。

###Registry 2.0 + Engine 1.6 = 更快的镜像下拉

由于全面的重构Registry和Engine 1.6中新的Registry API支持，下拉镜像的性能和可靠性已经大幅提升。今天已经在DockerHub支持，同时我们已经发布Registry2.0可用于自行搭建。更多内容在[这里](http://blog.docker.com/2015/04/faster-and-better-image-distribution-with-registry-2-0-and-engine-1-6)。

###Compose 1.2

Compose是一个在Docker上定义和运行复杂应用的工具。今天我们同时发布Compose1.2，它包括一个新的特征使得你可以扩展服务到其他Compose文件，这样就可以定义不同环境而不需要重复劳动。当然，也还有其他新特性，请查阅[文档](http://blog.docker.com/2015/04/easily-configure-apps-for-multiple-environments-with-compose-1-2-and-much-more)。

###Swarm 0.2

Searm是一个原生Docker集群。它将一群Docker宿主机变成一个单一，虚拟的主机。Swarm 0.2在二月发布的0.1版本的基础上建立。它包括下面几个新内容：

* 传播策略：一个新的策略来调度集群中的容器，在可用的节点中传播它们。
* 更多Docker命令支持：更多进展关于支持完整Docker API的工作已经完成，例如支持下拉和注入镜像。
* 集群驱动：这已经不是第三方的驱动了，但第一步已经向构建一个嵌入式驱动接口迈进。未来可能会在集群系统上使用Swarm，如Mesos。

更多内容在[这里](http://docs.docker.com/swarm/)。

###Machine 0.2

Machine使得在你的机器、云提供商和你的私有数据中心上创建Docker宿主很容易。Machine 0.2向一个稳定版本的Machine迈出了一步。主要集中在提高稳定性和扩展性上：

* 明确驱动接口：现在更容易来为提供商写驱动。
* 更加可靠和持久化供应：供应服务器现在由Machine处理，而不是让每个驱动各自做事。
* 重生成TLS认证：一个新命令已经被添加，为了更好的安全事件以及其中某个宿主机的IP变更，它用于重新生成一个宿主机的TLS认证。

从[这里](https://github.com/docker/machine/blob/master/CHANGES.md#020-2015-03-22)查阅变更文档，[下载](http://docs.docker.com/machine/)Machine 0.2。

###总结

我们非常兴奋关于新版本的发布已经对未来的希望。感谢这个项目的所有贡献者，这些都离不开你们的帮助。同时，我们感谢所有人，感谢你们对发布版的测试、问题发现和提出。我们希望你们可以享受这次版本。贡献者和管理者：我们在IRC上关注着你们。再次感谢大家！

**原文：[DOCKER 1.6: ENGINE & ORCHESTRATION UPDATES, REGISTRY 2.0, & WINDOWS CLIENT PREVIEW](http://blog.docker.com/2015/04/docker-release-1-6/)（译者：陈杰）
**

本文已发布在DockOne，链接：[http://dockerone.com/article/319](http://dockerone.com/article/319)




