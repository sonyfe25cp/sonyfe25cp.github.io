---
layout: post
title: "第二次Mesos北京线下见面交流会@去哪儿网"
category: docker
---

6月28日下午，第二次Mesos北京线下交流会在西小口去哪儿会议室举行，来自各行业对Mesos感兴趣的约30名技术人参加了此次活动。本次活动中，开放云精选创始人李建盛、数人科技云周伟涛、英特尔中国上海分公司罗钟悦、LeanCloud的王滨和时速云的杨乐给大家做了精彩的技术分享。

<img src="/images/0620/xiaodeshi.jpg" style="width:100%">

本次线下交流会由数人科技的肖德时主持，对大家能够在如此炎热的中午参加这么一个技术交流会表示了感谢和欢迎。

###开放云精选创始人李建盛


<img src="/images/0620/os.jpg" style="width:100%">

李建盛介绍自己对Mesos已经有3个月的学习时间了，然后引用了黑客帝国中尼奥拿起红色药丸进入矩阵的故事，开始介绍接触Docker的感受。随着对Docker使用的深入，开始接触Mesos并进行研究。

”总管计算机的历史是不断提出通用性的过程，不断加入抽象层的历史”，李建盛开始从传统操作系统开发者的角度开始回顾虚拟机的历史、分布式计算的历史、DCOC kernel和 Linux Kernel的区别。

随着Mesos对各种操作系统的支持、各厂商对物联网标准的争夺，这些都使得数据中心和微服务技术变成大势所趋。




###数人科技-Mesos持久化存储初探
<img src="/images/0620/shuren.jpg" style="width:100%">
在数人科技周伟涛简短的自我介绍之后，他开始对Mesos进行分析，由于Mesos对无状态的服务（如Spark这种）支持的很好，但是对Mysql这种需要持久化的东西支持的很一般，于是在Mesos上持久化数据的需求就很有必要。

这个内容主要分以下几个部分来讲，分别是：在0.22Mesos中如何玩持久化、在0.23中对持久化卷的细节介绍（磁盘隔离等）、持久化卷与独立/共享文件系统以及向社区可以做些什么贡献（去中心化的数据服务等）。

当前Mesos还处于0.22版本，如果要在该版本内实现持久化存储，有以下几种方法：

	a. 存储放在集群外
	b. 存在某个固定的slave节点上
	c. 增加一个分布式文件系统（延迟）
	d. 采用已经是数据分片的系统，如Cassandra

###Intel Mesos Ceph Framework

<img src="/images/0620/intel.jpg" style="width:100%">

罗钟悦讲师专程从南京坐飞机过来北京参加此次的线下交流会，他来自Intel中国上海分公司。这次来给大家分享的内容是他们自行研发的Mesos Ceph Framework。他在接触Mesos之前做了多年的Openstack研究，由于Openstack的太多坑而放弃并选择了Mesos。

为了使得Big data在Mesos上运行顺利，结合分布式文件系统Ceph在云环境中的使用，他们实现了一个Mesos ceph framework，目前提供一个RADOS gateway，并且有一个scheduler和executor。目前实现的版本的Journal写在SSD上，同时还有一些待完成的功能，例如暂时没有实现宿主HDD的选择。

最后用视频演示了该framework在实际中使用的情况，同时公布了该项目的开源地址：http://github.com/intel-bigdata/ceph-mesos。


###LeanCloud 使用者的角度看Mesos

<img src="/images/0620/wangbin.jpg" style="width:100%">

来自LeanCloud的王滨从一个使用者的角度给大家分享了他们使用Mesos的经验。由于在分布式环境下contab没法维护，于是他们找到了Mesos这个解决方案。在生产环节中，很多场景类似这样“统计模块貌似有问题，你去实时通信02那台机器上看一下”，这些都是混合部署（多种服务混合在不同的机器上）造成的。在以前使用虚拟机的时候，由于很多虚拟机跑在一个母鸡上，而该母鸡的load由于其他因素提高了，导致某些虚拟机莫名其妙的load高了，这个场景也使得虚机不如容器化的方案。

在实际部署中，name node用的是虚拟机，配置单核cpu和2g内存，而slave都是物理机（配置较高），当需要新机器加入集群时，配合puppet来快速启动和完成配置。

对于在mesos迁移过程，采用docker容器，自包含&&无状态。监控报警用nagios，只负责机器状态，不负责应用状态。日志则在容器内部用logstach来解决，然后由其他程序来解决日志。网络部分采用Host模式，其他bridge、模式虽然用过，但是有坑。

这个方案的缺点是暂时没有node drain功能，如果维护一个节点，需要强制杀进程，然后停掉slave。而marathon更新配置会自动重启，但很多应用场景并不允许重启，例如大量的链接断开。如果有热更新机制就好了。

最后，针对Marathon调度的特性，他展开了一系列的脑洞，包括codis、spark、flocker之类的。


###时速云 Kubernetes和Mesos在集群管理的应用和实践

<img src="/images/0620/rongqiguanli.jpg" style="width:100%">

杨乐的分享主题是浅谈容器集群管理，从使用角度来讲解容器集群管理，主要是说mesos和Kubernetes的特点，其中更侧重Kubernetes的管理、集群，希望能够给大家带来一些集群管理上的启示。

由于Docker在跨机通信上有难度，针对多机集群、资源调度、可扩展性、负载均衡、虚拟挽留过、微服务化的要求，介绍mesos和Kubernetes在集群管理上的异同点，包括：资源调度、生命周期、健康检查、伸缩、服务发现等。
而他们共同目的是一致的，都是分布式集群、容易扩展、资源调度、实现生命周期等。可以将Kubernetes作为mesos的framework来结合在一起，使两者的优点得到发挥。

接着，他介绍了Kubernetes的架构，其中的几个组成部分都可以采用其他插件进行替换，但默认的已经够用了。基础元素有pod、replicationcontroller、service和labels。对这几个基本元素进行了详细的介绍和举例说明。
最后介绍了时速云利用Kubernetes在编排上的实际经验。