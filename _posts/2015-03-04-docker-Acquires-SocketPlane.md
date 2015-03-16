---
layout: post
title: "Docker收购SDN技术创业公司SocketPlane.io"
category: docker
---
###Docker收购SDN技术创业公司SocketPlane.io

【编者的话】本文在报道Docker收购SocketPlane.io之余，不仅介绍了第一代SDN背后的技术，还介绍了基于Layer2的技术，最后介绍了Docker对于收购SocketPlane的规划和愿景。

衡量容器化技术发展速度的一个指标是迁移。SocketPlane.io是一个去年十月成立的创业公司，它们致力于为Docker环境创建一个低控制的软件定义网络模型。该公司已经被Docker收购，两个组织于周三早晨正式公布了这个消息。

就在上个月The New Stack的[报道](http://thenewstack.io/sdn-docker-real-changes-ahead/)中，SocketPlane在socket层为Docker容器创建了一个网络抽象层。它可以不需要物理网络控制器和虚拟网络控制器的情况下连接容器。换句话说，它通过让容器成为网络的方式来促进了软件定义网络。

####第一代SDN之外的事情

使用SocketPlane的作用是可以在一个逻辑网络内创建多个容器。这样的网络可以存在于一个单处理器的地址空间内，不需要调用网络控制器。

在The New Stack的一次采访中，Docker的副产品总监 Scott Johnston说“我们认为网络新兴是基于Docker的分布式应用的一个重要组成部分”。他还说道“虽然我们实现了一些功能，但是当我们看到分布式应用的容器间网络应用的具体用例时，我们意识到我们还缺乏一些专业知识和经验在这方面”。

SocketPlane利用IPv4地址空间创建了一个逻辑网络。于是它可以在自己掌控范围内管理逻辑网络，类似于某种SDN。但它还缺少API，这将由开源社区来共同完成。但随着Docker社区的迅猛发展和Docker生态系统的迁移，会有这么一个API出现么？


Johnston说“我们希望公司内的专业人士指导社区围绕着开发一个这样的API努力，这样将会有一套既能让用户成功构建便携式应用的API，又能让社区实现出可以观察这些实现方法差异的API。”

####用Layer 2实现

SocketPlane的联合创始人Brent Salisbury在RedHat做了一年的高级软件工程师。去年十月，他在[DevOps4Networks上宣布SocketPlane成立](https://www.youtube.com/watch?v=4DqxTVloBX8)时，他解释了为什么随着非单片应用越来越依赖下层网络层时，它们的扩展会成为一个困难。

他对OSI模型的数据链路层的看法是：“L2是疯狂的，但我们却只有L2，它已经差的不能再差了。它真心是个很差的应用。”


Salisbury说他已经问过“那些开发组在用网络做什么以至于不得不用L2？”这个问题。“我们依然用L2的主要原因是为了解决工作负载迁移，对么？我并不是想说系统管理员们太懒，因为它确实很有吸引力，可以满足这样的需求：‘我想在网络上放一个虚拟机，并且让它在网络的任何地方都可见，并且我不想做额外的工作。我还需要相同的ip地址。’。更重要的是，我们已经放弃了L2，因为没有它没有可操作扩展。在哪儿扩展L2，网络就将会在哪挂掉。”

Salisbury提供了一个reactive OpenFlow的例子，这是一组SDN操作，它可以将一组包不带转发信息的情况下通过一个主频道接口发给OpenFlow的SDN控制器。这使得控制器来决定一个端到端的路由策略，它是一组特定的数据包流。SDN圈内一直争论这样的操作到底算不算是Layer2。但SocketPlane公司的Salisbury认为，不管这是Layer2还是Layer22，反正它都被证实是不可以扩展的。

我们在去年十月同Docker的Scott Johnston分享了Salisbury的观点。他的反馈让人很意外，他甚至用了过去时态来表述当今的SDN已经过时了。

Johnston说“OpenFlow做的事情是让人震撼的，它们致力于虚拟机之间的网络。如果我们反思它们所做的事情：处理数以十计甚至上百的虚拟机。但是，我们来讨论容器间的网络，我们在讨论一个数量级或更多的容量，也即是说我们讨论的是数百个甚至上千个容器之间的网络。于是，相比起OpenFlow原有的设计，这就将是一个非常不一样的规模。”

####Both Feet

Docker已经内置了第一代的网络系统，并且Docker组织已经从这里学习了很多。但是在2014下半年，Docker意识到它的客户们在为数以千计的容器组网，交叉在多个宿主机和数据中心。也就是这时，Docker派出它的团队到社区中，去调研那些大的、积极进取的创业公司。在于他们讨论Docker如何集成OpenStack来做私有云或混合云，如何集成OpenDaylight和Open vSwitch来做SDN时，一个名字总是在这些讨论中出现，那就是：SocketPlane。

Johnston承认，SocketPlane并没有进入它们的收购意愿清单中。经过[最近的GitHub搜索证实](https://github.com/search?q=SocketPlane&type=Code&utf8=%E2%9C%93)，确实是它在推动Docker网络方面的讨论。

“他们在第四季度加入的这个社区，快速前进着、积极的贡献着。他们的聪明才智、他们的尊重、非常接地气和周到全面的讨论、他们代码的质量、他们提出的协议的质量，这些事情让广大的社区成员对他们有了认识。他们也引起了我们的注意。”

Johnston说，那是在一月中旬，两个公司在一个与商业无关的讨论中，关于建立一个便携的、自组网的平台，可以应用在不同环境中任何不同的实现：应用可以自组网或者不需要互联网。

在周三上午的声明中，Docker坚信SocketPlane的备选策略可以将开发者从不得不适应供应商或云提供商的网络策略的困境中解放出来。该组织声明，随着应用在异构基础设施上的更加便携，SDN相关的替代方法需要更强的定义。该组织希望这次收购可以提供一个机会，让开发者、网络工程师和操作经理能够在同一套框架下工作。

**原文链接：[Docker Acquires SDN Technology Startup SocketPlane.io](http://thenewstack.io/docker-acquires-sdn-technology-startup-socketplane-io/) （译者：陈杰）**

