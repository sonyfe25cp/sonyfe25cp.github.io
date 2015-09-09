---
layout: post
title: "Docker容器和下一代虚拟化"
category: docker
---

今年，LinuxCon和ContainerCon参与者将有机会听到Jerome Petazzoni关于[Docker, Containers & Security: State Of Union](http://lccocc2015.sched.org/event/2084a1f2df36e7667df3a579846a3c72?iframe=no#.Vci8B_MVhBc)的报告。Jerome在Docker公司工作，他帮助其他人将所有的事情进行容器化。Jerome曾在各个技术领域工作过，包括VOIP，嵌入式系统，网站托管，虚拟化和云计算。

在这次的访谈中，他深入分析了Docker并解释了如何为Docker容器管理存储，以及如何将数据随着Docker容器迁移。

**什么是容器？有哪些现有的容器技术？Docker与其他容器技术的不同是什么？**

从宏观角度来看，容器更像是轻量化的虚拟机。你可以在容器中安装任何你想要的软件，独立且不影响其他容器或者宿主环境。每一个容器有它自己的网络堆栈、进程空间、文件系统等。同时，他们的影响要远小于虚拟机：容器启动更快、占用更少的内存和硬盘空间。从底层角度来看，这都是因为容器只是宿主机上的一个进程，利用内核特征如命名空间和组管理来提供这种隔离。启动一个容器只是启动了一个普通的UNIX进程；创建一个容器只是复制了一个copy-on-wirte文件系统的镜像。

Docker与其他容器技术不同，因为它不只是一个容器引擎。Docker是一个平台，整合了Docker引擎、Docker Hub以及一系列的生态工具，如Docker Compose、Docker Machine、Docker Swarm等。这些都采用开发API。

**Docker与其他hypervisor虚拟技术的不同之处？**

有人说Docker是是一个“容器的hypervisor”，但是许多人并不这么认为，这是因为hypervisor通常是管理虚拟机，而Docker是管理容器。在深层技术细节也是不同的。当一个hypervisor启动虚拟机，它创建虚拟硬件，并利用特定CPU基础结构和特性，如VT-x，AMD-x或者权限层级。当Docker创建一个容器，它利用的是内核的特性，如命名空间、群组管理，而不依赖硬件特性。

这意味着容器在某种意义上说更具有可移植性，因为它们可以同时在物理机或者虚拟机上运行；但是他们从另外某种意义上来说，也更不容易移植，因为容器必须使用宿主的内核。这意味着，你不能运行一个Windows容器在Linux内核上（除非linux内核可以执行Windows二进制程序）。

**对管理Docker容器的存储有什么建议？如何将docker容器和数据关联？**

Docker有个“卷”的概念，使得宿主机和容器间可以共享目录。卷从概念上与虚拟机的“共享文件夹”有点类似，只是他们不需要在容器上做任何配置，并且它们是零开销的，因为他们采用的是绑定挂载实现。

当你的数据在硬盘（可以是一个具体的硬盘、RAID、其他挂载网络上的设备或者其他的任何设备）上，挂载到容器主机上的最简便的方法就是通过“卷”机制来暴露给容器。

Docker同样有插件机制来使得一个容器可以为其他容器提供存储。这意味着一个容器可以负责的重任是同Ceph的成员、Gluster或者其他存储集群关联，并使得块设备和挂载点暴露给其他容器。

**当Docker容器在别的机器上启动时，如何使得数据随着容器移动？**

就像在容器技术之前我们做的一样：网络存储、分布式文件系统，数据传输或者同步（利用rsync、unison等）。当采用容器时，有两个优势。首先，使用数据的方式从容器中抽象出来。例如，如果从DRBD迁移到Ceph，容器并不需要知道这个细节；事实上，同一个容器在本地存储或者分布式存储上运行是一样的。另一个优势来自于那些新的存储插件。他们将使得数据获取更容易，采用将应用容器和存储容器分开的方式。


**如何确定运行的Docker容器与基础容器镜像的变更？**

Docker提供API调用来对比一个容器和它的基础镜像，并且支持从现有的容器中创建一个新的镜像。对应的命令是“docker diff”和“docker commit”

**Docker容器如何帮助构建高度可用的解决方案？**

当创建一个高度可用的系统，通常需要考虑一个相当长的检查列表。Docker将使得这个步骤简单。例如，确保部署时相关机器上软件版本的一致。Docker不会自动的解决问题，但是它可以使得事情简单、快捷和更加可信，就像一个包管理器通常比从源代码编译更可靠。

**如何看待在企业客户生产环境中Docker适配性的变化？**

通常来看：Docker作为一个开放工具来保证一致性、可复制性的开发环境，类似于Hashicorp的Cagrant。然后，它确保了持续集成和持续开发，它降低了一半甚至更高的测试次数。从这些看，它适用于生产环境的前一个阶段，一旦使用团队已经积累了足够的经验和自信来运行Docker，它就可以服务生产环境了。

这篇文章是LinuxCon、CloudOpen和ContainerCon North America 2015的[Speaker Interview Series](http://opensource.com/resources/linuxcon-2015)的一部分。LinuxCon North America是一个平台，使得“developers, sysadmins, architects and all levels of technical talent gather together under one roof for education, collaboration and problem-solving to further the Linux platform”。

**原文链接：[Docker containers and the next generation of virtualization](http://opensource.com/business/15/8/interview-jerome-petazzoni-docker-linuxcon) （翻译：陈杰）**

===============================================

文章已经发布在DockOne.io，链接：[http://dockone.io/article/590](http://dockone.io/article/590)















