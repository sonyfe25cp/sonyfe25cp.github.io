---
layout: post
title: "Docker生态系统之三：服务发现和分布式配置存储"
category: docker
---

【编者的话】
本文介绍了服务发现与全局可读配置存储两部分内容，不仅介绍了工作原理和工作方式，也介绍了与之相关的故障检测、重配置和安全问题，最后还介绍了常用的服务发现项目。整篇文章将这个知识点介绍的很全面细致，让读者能够对服务发现和全局可读配置存储有一个全面的认识，值得学习。


###介绍
容器给寻找大规模设计和部署应用的需求提供了一个优雅的解决方案。Docker提供实际的容器技术的同时，许多其他的项目在协助开发在部署环境中所需要的引导和沟通工具。

许多Docker环境依赖的核心技术之一是服务发现。服务发现可以让一个应用或者组件发现运行环境和其它应用或组件的信息。它通常是采用分布式key-value存储方式实现，通常用来当做一个查询配置细节信息的地方。配置一个服务发现工具可以帮助让实际容器跟运行配置分离开，可以在多个环境中复用同一个镜像。

在这篇向导中，我们将讨论在一个Docker集群环境中服务发现工具带来的好处。主要关注在常规概念，在需要的地方会用例子来描述。

###服务发现和全局可读配置存储

服务发现的基本思想是任何一个应用的实例能够以编程的方式获取当前环境的细节。这是为了让新的实例可以嵌入到现有的应用环境而不需要人工干预。服务发现工具通常是用全局可访问的存储实现，它存储了当前正在运行的实例或者服务的信息。大多数情况下，为了让这个配置更加的容错和易扩展，这个工具是在基础设施中的多个宿主机上分布式存储的。

虽然服务发现平台的初衷是提供连接信息来连接不同组件的，但是它们也可以用来存储任何类型的配置信息。许多部署工具通过写入它们的配置信息给发现工具来实现这个特性。如果容器配置了这些，它们就可以去查询这些预配置信息，并根据这些信息来调整自身行为。

###服务发现是怎么工作呢？

每一个服务发现工具都提供一套API来供其他组件设置或搜索数据。正是如此，每一个组件要么在程序内部硬编码服务发现工具的地址，要么在运行时以参数形式传递进入。通常来说，服务发现服务用键值对形式实现，采用标准http协议交互。

服务发现工具的工作方式是：当它启动之后，其他服务来这里注册自身信息。这里记录了一个组件若想使用某服务时的全部必要信息。例如，一个Mysql服务会在这注册它运行的ip和端口，如果有必要的话，用户名和密码也会留下。

当一个服务的消费者开始工作时，它能够从注册处查询该服务的相关信息。然后它就可以用查到的信息与目标服务进行交互。负载均衡是一个很好的例子，它可以通过查询注册处得到各个后端节点承受的流量数，然后根据这个信息来调整配置。

这可将配置信息提出到容器外部。一个好处是可以让容器更加灵活，不受限于特定的配置信息。另一个好处是使得组件与一个新的相关服务交互时变得简单，可以动态进行调整配置。

###配置存储如何工作？

全局分布式服务发现的一个主要优势是它可以存储任何类型的组件运行时所需的配置信息。这就意味着可以讲配置信息抽象出容器，并放入更大的运行环境。

通常来说，为了让工作更有效率，应用在设计时应该赋上合理的默认值，并且可以在运行时可以通过查询配置存储来覆盖这些值。这使得运用配置存储跟在命令行执行时的工作方式类似。区别在于，通过一个全局配置存储，可以不做额外工作就能够对所有组件的实例进行同样的配置操作。

###配置存储如何对集群管理带来帮助？

在Docker部署中，分布式键值对存储的功能之一是明显不是对集群成员的存储和管理。配置存储是为了追踪成员宿主机变更和管理工具的最好环境。

一些可能会存在分布式键值对存储中的个人宿主机信息：

* 宿主机IP
* 宿主机的链接信息
* 跟调度信息有关的标签或元数据信息
* 集群中的角色（如果是采用了主从模式的集群）

在正常情况下，这些信息在你去实现一个服务发现平台时，可能不是你需要考虑的，但是他们为管理工具提供了一个可以查询或修改集群本身信息的地方。

###故障检测怎么实现？

故障检测的实现方式也有很多种。需要考虑的是如果一个组件出现故障，服务发现能否更新状态指出该组件不再提供服务。这种信息是至关重要的，关系到将应用或服务故障可能性降低到最小。

许多服务发现平台允许赋值时带一个可配置的超时时间。组件可以设置一个超时时间，定期去请求服务发现来重置超时时间。如果该组件出现故障，超时时间达到设定值，那么这个组件的连接信息就会从服务发现的存储中被去掉。超时时间长度在很大程度上是一个函数，它与应用需要多快去应对一个组件的故障。

这也可以通过将一个基本的“助手”容器与每一个组件相连来实现,而它们唯一的责任是定期的健康检查组件和更新注册表如果组件。这种类型的架构的担忧是,如果辅助容器出现故障,将导致不正确的信息在存储中。一些系统解决这个问题的方法是在服务发现的工具中定义健康检查。这样,发现平台本身可以定期检查已注册组件是否仍然可用。

###当细节发生变化时，如何重新配置服务？

对于服务发现模型来说，一个关键的改进就是动态重新配置。普通服务发现工具影响组件的初始配置，并允许通过检查在启动时的信息，而动态重新配置涉及配置组件去反映配置存储中的新信息。例如，当你在运行一个负载均衡，后端服务器上的健康检查可能会提示集群中的某一个成员出现故障了。运行中的成员机器需要知道这个信息，并调整配置信息和重新加载它的负载。

这个有多种方式实现。由于负载均衡的例子是这个功能的主要应用场景之一，一些现有的项目专注在当配置变动时重新配置负载均衡。HAProxy配置调整是常见的，这归结于在负载均衡领域内它的普遍性。

某些项目更加灵活，它们可在任何类型的软件中被用来触发变更。这些工具周期性的去请求服务发现工具，当变更被发现，利用模板系统和服务发现工具中的值来生成新配置文件。当配置文件生成结束，相应的服务将被重新加载。

这种类型的动态配置在构建过程中需要更多的规划和配置，因为这些所有的策略都需要存在于组件容器之中。这使得组件容器负责调整自身的配置。找出需要存在服务发现工具中的必要参数值并设计一个适当的数据结构以便使用，这是该系统的另一个技术挑战，但是它可以带来可观的效益和灵活性。

###安全方面如何？

许多人初次接触全局配置存储时担心的一个问题是访问的安全性。将连接信息存储在全局可访问的存储中真的合适么？

这个问题的答案很大程度上依赖于你准备在存储中存放的内容以及保护你的数据需要多少层的安全等级。几乎所有的服务发现工具可以采用SSL/TLS加密链接。对于一些服务，隐私性可能不是最重要的，将发现服务放在内网中能让人满意。但是，大多数的应用会从它额外的安全性上获益。

有许多不同的方法来解决这个问题，同时各种项目也都提供他们自己的解决方案。一个项目的解决方案是继续允许开放服务发现服务本身，但是对于写入数据进行加密，使用者必须用相应的密钥来解码从服务发现中获取的信息才能使用。其他组件不可以获取到未加密的数据。

还有不同的方法，一些服务发现工具实现了访问控制列表，将不同的键值切分到不同的分组中。他们可以根据访问需要来制定不同的秘钥来访问相应的分组。这种简单的方式既保证了能够给特定组件提供信息又保证了对其他组件的不可访问性。每个组件都可以被配置为只允许访问它所需要的连接信息。

###有哪些常见的服务发现工具？

既然我们已经讨论了一些服务发现工具和一般特征的全局分布式键值存储，下面我们来介绍几个与这些概念有关的项目。

一些常见的服务发现工具：

* etcd：这是CoreOS的创建者提供的工具，面向容器和宿主机提供服务发现和全局配置存储功能。它在每个宿主机上有基于http协议的API和命令行的客户端。
* consul：这个服务发现平台有很多高级的特性，使得它脱颖而出，例如：配置健康检查、ACL功能、HAProxy配置等。
* zookeeper：这个工具较上面两个都比较老，提供一个更加成熟的平台和一些新特性。

一些扩展了基本服务发现工具的项目：

* crypt：Crypt允许组件通过采用公钥加密的方式来保护它们的数据。需要读取数据的组件会被分配密钥，而其他组件则不能读取数据。
* confd：Confd项目旨在基于服务发现的变化，而动态重新配置任意应用程序。该系统包含了一个工具来监测节点中的变化、一个模板系统来根据获取到的值来生成配置文件，并能够重新加载受影响的应用。
* vulcand：Vulcand为成组的组件作为负载均衡使用。它使用etcd作为后端，并基于监测变更来调整它的配置。
* marathon：虽然marathon主要是调度器（后续介绍），它也实现了一个基本的重加载HAProxy的功能，当发现变更时它来协调可用的服务。
* frontrunner：这个项目嵌入在marathon中来提供对HAProxy提供一个更稳定的解决方案。
* synapse：这个项目引入了嵌入式的HAProxy组件，它能够路由流量给各个组件。
* nerve：它被用来与探针的通信来为各个组件提供健康检查，如果组件不可用，nerve将更新探针将该组件移除出去。

###总结

服务发现工具和全局配置存储使得Docker可以适应它们所处环境并嵌入现有的组件。对于提供简单、易扩展和部署，并允许组件跟踪和应对他们所在运行环境变化来说，这是一个重要的先决条件。

在下一个指南中，我们将探讨Docker容器和宿主机之间用自定义的网络配置进行通信。

###本系列的其他文章

Docker已经为开发者和管理员提供一个简单的平台来创建和部署可扩展的应用。在这个系列中，我们将探索Docker如何与其他组件整合在一起，并用它们提供的工具集来便捷地提供高可用性的分布式系统。

1. [常用组件介绍](http://dockerone.com/article/205)（已翻译）
2. [容器化的综述](http://dockerone.com/article/208)（已翻译）
3. 服务发现和分布式配置存储（本文）
4. 网络与通信（翻译中）
5. 调度与编制（翻译中）




===============================================
**原文：[The Docker Ecosystem: Service Discovery and Distributed Configuration Stores](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-service-discovery-and-distributed-configuration-stores) （译者：陈杰）**
