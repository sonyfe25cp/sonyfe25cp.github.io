Running MongoDB as a Microservice with Docker and Kubernetes

在Docker和Kubernetes上运行MongoDB微服务

##介绍
想尝试在笔记本电脑上运行MongoDB么？执行一个简单的命令，然后你就有一个轻量级、自组织的沙盒；再通过一条命令就可以移除所有的痕迹。

需要在多个环境中运行相同的应用程序栈？创建你自己的容器镜像，让开发、测试、操作和支持团队启动一份完全相同的环境。

容器正在改变整个软件生命周期；从最初的技术试验到通过开发、测试、部署和支持的概念证明。

###阅读微服务：容器和编排白皮书

编排工具管理者多个容器如何创建、升级和高可用。编排同样管理着容器如何连接，并利用多个微服务容器创建稳定的应用服务。

丰富的功能、简单的工具、强大的API让容器和编排得到DevOps团队的青睐。DevOps工程师将它们整合到持续集成（CI）和持续交付（CD）工作流中。

本篇文章将探索你在尝试运行和编排MongoDB容器时遇到的问题，并描述如何克服这些问题。

##对于MongoDB的思考

采用容器和编排运行MongoDB带来了一些新的思考：

* MongoDB数据库节点是有状态的。若一个容器挂了，并且被重新编排，数据丢失是不能接受的（虽然它可以从其他节点中恢复数据，但是很费时）。为解决这个问题，Kubernetes中的卷抽象（Volume abstraction）特性将用于映射MongoDB数据文件夹到一个持久化地址，避免容器的失败或重编排。
* 同一组MongoDB数据库备份节点之间需要通信，即使是在重编排之后。同一冗余备份集合的节点必须知道全部其他节点的地址，但是当某个容器重编排之后，它的IP地址会变化。例如，所有Kubernetes内的容器共享一个IP地址，当pod被重编排之后这个地址就会改变。在Kubernetes中，这个问题可以通过联系Kubernetes服务与MongoDB节点来解决，采用Kubernetes的DNS服务提供主机名给重编排之后的服务。
* 一旦每个独立的MongoDB节点（每个节点在单独容器中）启动起来，备份集合必须初始化，并把每个节点加入进来。这需要编排工具提供额外的逻辑。特别是备份集合中只有一个MongoDB节点时，必须执行rs.initiate和rs.add命令。
* 如果编排框架提供自动化重编排容器功能(如Kubernetes的特性)，那么这可以提高MongoDB的容灾性，节点会在挂掉之后自动重新创建，恢复到完整冗余水平且不需要人工干预。
* 当编排框架掌控所有容器的状态时，它并不管理容器内的应用或者备份数据。这就意味着采用一个有效的管理和备份方案很重要，如MongoDB Cloud Manager，包括MongoDB Enterprise Advanced和MongoDB Professional两部分。考虑到需要创建镜像，可采用你倾向的MongoDB版本和MongoDB Automation Agent。

###利用Docker和Kubernetes实现MongoDB冗余备份

