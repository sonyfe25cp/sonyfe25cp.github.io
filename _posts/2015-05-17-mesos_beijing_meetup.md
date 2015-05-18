---
layout: post
title: "Mesos北京线下见面交流会in数人科技"
category: docker
---


5月17日下午，Mesos北京线下交流会在数人科技会议室举行，来自各行业对Mesos感兴趣的约40名技术人参加了此次活动，其中Hyper的创始人王旭对Hyper做了技术报告，LeiSu对IBM EGO做了详细的介绍，之后数人科技的Chen Fu对Mesos的最佳实践进行了技术分享。

<image src="/image/0517/Haolin.jpg" style="width:100%"/>

活动开始，郝林宣布本次线下活动正式开始，然后对下午的活动内容做了介绍，对数人科技的支持表示感谢，并欢迎参会人员。

###王旭：Hyper技术分享

<image src="/image/0517/Hyper.jpg" style="width:100%"/>

在简单的自我介绍之后，王旭对自己的项目Hyper开始做分享。首先他从Mesos早期的情况进行介绍，包括其在早期主要针对的job时单一case的job，（例如：hadoop，storm，spark）。而更加通用的任务就是对于Docker的支持了。

“Docker是应用的延伸。”这是王旭对Docker的见解。
而Hyper则是一个面向应用中心的虚拟机。Hyper = hypersivor + docker。这个项目基于Hypervisor，并采用定制过的kernel，实现了直接在vm中跑Docker image，而不是在os中跑。这也是与其他现有项目最大的区别之一。
它包含一个deamon（用于pull镜像相关）和Hyper init（它负责来启动docker）。

在对于项目的介绍上，目前该项目基于go语言开发，依赖docker1.5和hypersivor，目前采用的时KVM。对于项目的愿景，他表示有开源的计划，由于功能还在完善，所以将在版本相对稳定时进行开源。

此外，王旭还生动的介绍OPENSTACK的背景及相关事情, OSV和Hyper的kernel的区别，给参会人员普及了一些背景知识。


###LeiSu：IBM EGO技术分享

<image src="/image/0517/IBMEGO.jpg" style="width:100%"/>

来自IBM的LeiSu对EGO进行了分享，他先介绍市场背景，关于IBM的转型和CAMSS方面的知识。然后开始介绍DCOS的概念，引出EGO的解决方案。

EGO是一个企业级资源管理的解决方案。它包含一整套的企业资源管理配置组件和策略。其中EGO的调度策略是重点。提供多种策略，包括：Ownership、Borrow/lend, Dynamic share, Hybird, Multiple Dimension Scheduling、standby service、smart reclaim 、Resource Group Preference、Topoiogy Aware Scheduling。他随后针对几种具体策略进行了详细讲解并分别举了例子说明。


他然后介绍了DCOS在Waston QA系统中的应用、在Hadoop中的应用的情况、在EGO上schedule作业以及DCOS 在spark上的任务调度。

对于EGO和Mesos的集成进度，他表示集成工作正在进行，他们会积极的与Mesos社区进行互动，并思考在不同应用场景的情况下，如何跟Mesos社区共同发展。希望可以把EGO中的调度策略加入到Mesos中，以增强Mesos。目前正在采用插件机制来开发。对于Yarn，也重新了其对应的schedule模块；这跟改造Mesos的方法类似。

QA：
如何回到社区？
由于EGO本身不是开源，如何回到开源有很多问题。虽然EGO已经功能很多，但还是希望可以回归Mesos，增强生态，使得两方在社区的帮助下都获得足够的发展。


###Chen Fu：Dataman Mesos Best Practice

<image src="/image/0517/shuren.jpg" style="width:100%"/>

Chen Fu介绍了数人科技在Mesos使用方面的实战经验。

数人科技目前有做自己的Mesos发行版，主要是针对Ubuntu和Centos。ChenFu通过现场的操作演示了如何使用该版本并建立一个单机测试版本的mesos。

然后他介绍了如何用marathon来调度Mesos。由于其自带的界面提供的功能相对简单，更多的功能需要通过API来完成。在现场，他操作并演示了如何通过RESTful的API对已创建的应用进行管理。其中，对Constrain和Cluster部分进行了重点讲解和演示。


###结束

在三位嘉宾精彩的技术分享之后，郝林宣布本次的Mesos线下见面交流会圆满结束，感谢嘉宾的精彩报告和大家的参与。

会后，大家对于各自在Mesos实际使用中遇到的问题分组进行了激烈的讨论。

另外，数人科技表示将会在每个月组织Mesos的线下见面交流会。


