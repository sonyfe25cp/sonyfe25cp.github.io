---
layout: post
title: "回顾微服务的边界选择"
date: 2017-01-03 11:26:02
category: docker
---

目前，微服务是一个热门的话题。尽管它很复杂，包括分布式事务、最终一致性、操作开销等，它依然是发展趋势，特别是它带来的优点，如跨语言架构、选择的伸缩性、强大的模块化、容错能力、实验性、敏捷性等。

微服务可能不是新鲜事物。我仍然记得,2011年我工作在一个SaaS产品，我们试图建立包含不同的上下文的架构，其中包含敏捷项目管理、问题跟踪器，和协作工具。当时我们不熟悉这个微服务概念,但我们试图实现的功能与微服务架构类似。最初,决定分发应用程序到不同模块这个结构效果很好,但是我们在集成它的过程中搞砸了。我们采用直接同步调用的方式，而不是拥抱事件驱动架构的不同模块之间信息共享的机制。这使得我们距离最初的目标（独立部署、开发和运行）越来越远。

本文讨论的另一个教训来源于我最近的一个项目，它可以帮助你从不同的角度识别微服务的边界。开始讨论我们能做什么不同之前，对将要讨论的系统进行宏观了解是首要的。

###背景

下一代值机系统(NGB)是一个护照管理系统的升级版本。NGB的主要目标是加快的乘客在机场的移民柜台的处理速度。该系统通过将处理时间平均从56秒减少到10秒，提高了乘客的体验。下面是该系统的[处理流程图](http://martinfowler.com/bliki/BoundedContext.html)。

图1

在NGB系统中，有边界的组件用红线标示。其中一些边界组件已经被刻意去除，避免发散讨论。

NGB系统的核心是预清除过程。预清除过程通常在乘客登机前72小时开始。航空公司将预订信息发送给相关机构的乘客信息系统,将信息转发给NGB系统用于预清除。预清除期间,在移民系统的配合下，NGB检查乘客的概要文件，并将结果发送给后台校验，避免乘客因程序问题(例如黑名单)被误判有罪。乘客发现无罪是绿色标志，它需要在移民柜台花费几秒钟来处理这些乘客。

希望现在你已经对NGB系统有了一定了解，那么开始我们的回顾。


###哪些变好了呢?

从按照大部分的书本上对微服务边界的定义来说，NGB被分成自动化清除、旅客出境管理和通知模块。自动化预清除部分在后台处理预订数据流并将数据传递给旅客处境控制模块，用于持久化和人工干预。这个场景包含两大类用户：当面处理手续的一线用户与需要后台操作处理手续的用户。通知模块，顾名思义，用于发送通知给系统管理员。

###哪些变坏了呢？

尽管普遍用于乘客边境控制模块的语言是特定的和独特的,它仍然是不够的。随着时间的流逝,乘客边境控制模块不仅开始变得独立,也开始随着生产环境的变化而产生问题。我们在后台操作中做任何更改操作必须小心，避免任何影响前线操作。此外,任何只影响后台操作的变更，需要隔离在不影响前线部署操作的前提下进行,反之亦然。

###我们可以做出哪些改变？

在进行讨论可以做出哪些改变之前，我们先快速回顾下NGB系统的商业处理流程，如下：

图

虚线箭头描述在不同时间线上发生的商业流程。例如，预订信息在航班出发之前72小时被提供，工作人员在航班出发前几个小时检查旅客信息，当旅客到达柜台时进行旅客护照检查。

如果你关注了上文提到的模块构成图，自动化预清除流程被拿到一个单独的模块，其中工作处理流程和前台处理被封装到单独地旅客出境控制模块。把它们放到同一个模块中的原因是符合边界定义。为了克服上一段中高亮部分提到的问题，我们可以分布边界模块如下：

再次，为了维持讨论话题的重点，上文流程图中某些有界的模块不在讨论范围内。乘客出境控制模块被进一步分割为三个不同的模块，用红色加粗的圆圈标示。

后台和前线边境控制环境将使乘客的最小结构域模型作为共享内核上下文,而完整的域模型将在旅客event-sourced客运上下文。后台系统操作和前端出境控制模块将保持旅客处理模型的最小结构，共享模块的核心部分，其中完整的旅客领域模型将会在旅客出行模块中由事件构成。

这三个模块都会在不同时间线发送事件到旅客出行模块，例如在自动预清除阶段、后台审核阶段、前端审核旅客出行阶段等。
这种情况下，旅客旅游模块将会扮演一个无上线的微服务。并且，它将会隔离NGB系统内的读写操作。

在这种形式的分布式中，不仅降低了旅客出境控制系统中后端或者前端变更服务的影响，同时有助于独立变更。这个方法唯一的权衡之处在于我们必须维持多个旅客领域模型的镜像在不同的模块中。为了实现降低耦合，这将无法避免。

###结论

从上下文边界的角度对微服务进行划分会是一个好的开始，但是仅靠上下文这一个角度来处理是不够的。其他角度确定微服务边界也至关重要，例如根据选择性扩展、可变性、可部署性、测试性、商业流程、组织结构等，也会影响微服务的边界选择。

----------------------
原文链接：[A Retrospective on Microservice Boundaries](https://dzone.com/articles/retrospective-on-microservice-boundaries)