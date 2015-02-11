---
layout: post
title: "Docker生态系统系列之一：常用组件介绍"
category: docker
---

【编者的话】本篇文章是介绍Docker生态系统的第一篇，不仅从概念上介绍了容器化、服务发现和全局配置存储、网络工具、调度、集群管理和编制这几部分内容，而且配以清晰易懂的例子进行讲解说明，非常赞。

###简介

容器化是处理分发和部署应用的一种方式。实现打包组件并且将所需依赖实现标准化、独立化、轻量化环境的工具叫容器。现在许多组织对设计可以容易部署在分布式系统上的应用和服务很感兴趣，这要求系统可以容易的扩展并对机器和应用的容错性好。Docker是一个在多样环境下简单并且标准化的容器平台。它在很大程度上有助于这种风格服务的设计和管理。大量的围绕着分布式容器管理的软件已经开发出来了。

###Docker和容器化

Docker是当今最常用的容器化软件。尽管也有其他的容器化系统，但是Docker使得创建容器和管理容器非常简单，并且与很多开源项目进行了整合。

<img src="/image/0210/dc1.png" width="100%"/>

在上图中，你可以看到容器跟宿主机的关系。容器隔离独立的应用并使用已经被Docker抽象化的操作系统资源。在右侧的视图中，我们可以看到容器是用‘layer’来建立的，多个容器共享基础层来减少资源的使用。

Docker的主要优点：

* 轻量级资源使用：容器在进程级别隔离并使用宿主机的内核，而不需要虚拟化整个操作系统。
* 可移植性：一个容器应用所需要的依赖都在容器中，这就让它可以在任意一台Docker机上运行。
* 可预测性：宿主机不需要关心容器内运行的是什么，同样，容器也不需要关心是在哪个宿主机上运行。所需要的接口都是标准化的，并且交互也都是可预测的。

通常在用Docker来设计应用或者服务时，最好的方法是打破面向服务架构的设计，而采用独立容器的设计。这可以让以后容易的扩展或者升级独立组件。拥有如此的灵活性是人们对用Docker开发和部署感兴趣的原因之一。

想要找更多Docker容器化的应用，请点击[这里](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-an-overview-of-containerization)。

###服务发现和全局配置存储

服务发现是整个策略中的一个组成部分，它旨在使容器部署更具有伸缩性和灵活性。使用了服务发现后，可以让容器在没有管理员干预的情况下了解运行环境。它们可以自行发现必须要交互的组件的连接信息，可以自行注册自身以便其他工具知道该组件已准备就绪。这些工具同样经常作为全局分布式配置存储服务，可以存储你的基础设施中任意的服务配置信息。

<img src="/image/0210/dc2.png" width="100%"/>

从上图中，你可以看到一个流程，图中应用A注册自身的连接信息给发现服务系统。一旦注册成功，其他应用可以通过查询发现服务系统来找到如何连接到这个应用。

这类工具通常这么实现：在分布式环境中用基本的键值对来分布存储。通常来说，键值对存储提供一个HTTP API接口用来存储和获取值。有一些还提供了更加安全的机制，如加密条目或者访问控制机制。除了它们的提供新容器自配置的主要功能外，这些分布式存储对管理Docker宿主机也是非常重要的。

服务发现存储的一些职责：

* 允许应用去连接它们所依赖的服务区获取所需数据
* 允许服务区为了上述的需求去注册它们的连接信息
* 提供一个全局可访问的位置，用于存储任意的配置数据
* 存储任何一个集群管理软件所需要的集群节点信息

一些流行的服务发现工具和相关项目：

* [etcd](https://www.digitalocean.com/community/tutorials/how-to-use-etcdctl-and-etcd-coreos-s-distributed-key-value-store)：服务发现/全局分布式键值对存储
* [consul](https://www.digitalocean.com/community/tutorials/an-introduction-to-using-consul-a-service-discovery-system-on-ubuntu-14-04)：服务发现/全局分布式键值对存储
* [zookeeper](https://www.digitalocean.com/community/tutorials/an-introduction-to-mesosphere#a-basic-overview-of-apache-mesos)：服务发现/全局分布式键值对存储
* [crypt](http://xordataexchange.github.io/crypt/)：加密etcd条目的项目
* [confd](https://www.digitalocean.com/community/tutorials/how-to-use-confd-and-etcd-to-dynamically-reconfigure-services-in-coreos)：观测键值对存储变更和新值的触发器重新配置服务

###网络工具

容器化的应用有助于它们向面向服务的设计，提倡将功能设计成离散式组件。在这种特性使得管理和扩展变容易的同时，它对组件间的网络功能性和可靠性提出了更高要求。Docker自身提供基本的网络结构，包括容器间和容器与宿主直接的通信结构。

Docker本地的网络能力为容器间的连接提供两种方案。第一种是暴露一个容器的端口，并可选择性的映射到宿主机上为外部路由服务。可以自己决定使用宿主机的端口来映射，也可以让Docker随机的选择一个未使用的高位端口号。这是一种对大多数场景友好的方式来提供对容器的访问。

另外一种方法是采用Docker的'links'来允许容器间通信。一个关联的容器将会获得它的对应连接信息，在它处理了那些变量后允许它自动连接。这样就使得同一个宿主机上的容器不需要知道对应服务的端口和地址，就可以直接进行通信。

这个基本的网络环境适用于单宿主机或者严格受限的环境。但是，Docker生态环境已经产生了大量软件，它们关注在为运营人员和开发者扩展网络功能。一些额外的网络功能已经可通过额外工具实现：

* 覆盖网络来简化和统一多宿主机间的地址空间
* 虚拟私有网络适配来提供多个组件间的安全通信
* 分配子网给每个宿主机或者每个应用
* 简历macvlan接口进行通信
* 为容器配置自己指定的mac地址、网关等。

参与改进Docker网络功能的项目有：

* **flannel**：覆盖网络提供给每个宿主机一个独立子网
* **weave**：覆盖网络描述一个网络上的所有容器
* **pipework**：一个高级网络工具，它用于任意高级网络配置

###调度、集群管理和编制

建立一个集群容器环境时另外一个必备组件是调度器。调度器负责在可用的宿主机上启动容器。

<img src="/image/0210/dc3.png" width="100%"/>

上图描述了一个简单的调度决策。请求来自API或者管理工具。然后，调度器衡量请求的条件和可用的宿主机的状态。在这个例子中，它从一个分布式数据存储/发现服务中获取容器密度的信息，以便它可以在一个不是很忙的宿主机上运行新应用。

这个宿主机选择的过程是调度器的一个核心任务。通常来说，它能够按照管理员预设定的特殊条件限制来自动化完成这个过程。可能的限制条件是：

* 当给定另一个容器时，安排新容器在同一个宿主机
* 确认这个容器不放在同一台宿主机上作为另一个容器
* 在宿主机上安置容器时记得带相匹配的标签或者元信息
* 在繁忙度最低的宿主机上安置容器
* 在集群的每一个宿主机上运行这个容器

调度器责任是在相关的宿主机上加载容器，启动容器、停止容器和管理这个进程的生命周期。

由于调度器必须要跟组内的每一个宿主机交互，集群管理功能通常也是包括在内的。这就要求调度器获取它们的信息并执行管理任务。编制在这里通常指的是容器的组合调度和宿主机管理。

一些流行的负责调度和集群管理的工具：

* [fleet](https://www.digitalocean.com/community/tutorials/how-to-use-fleet-and-fleetctl-to-manage-your-coreos-cluster): 调度器和集群管理工具
* [marathon](https://www.digitalocean.com/community/tutorials/an-introduction-to-mesosphere#a-basic-overview-of-marathon):调度器和集群管理工具
* [Swarm](https://github.com/docker/swarm/):调度器和集群管理工具
* [mesos](https://www.digitalocean.com/community/tutorials/an-introduction-to-mesosphere#a-basic-overview-of-apache-mesos):宿主机抽象服务，用于为调度器联合宿主机资源
* [kubernetes](https://www.digitalocean.com/community/tutorials/an-introduction-to-kubernetes):一个管理容器组的工具，具有先进的调度能力
* [compose](https://github.com/docker/docker/issues/9694):一个用于创建容器组的容器编制工具

想找更多关于Docker的基本调度管理、容器组、集群管理软件，请点击[这里](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-scheduling-and-orchestration)。

###结束语

现在，你应该已经熟悉与Docker生态环境相关的软件的基本功能。在支持项目的帮助下，Docker提供一个能够大规模扩展的软件管理、设计、部署策略。通过理解和使用这些项目的功能，你能解决一个要求能够足够灵活的解释变量操作的复杂应用部署需求。


###本系列的其他文章

Docker已经为开发者和管理员提供一个简单的平台来创建和部署可扩展的应用。在这个系列中，我们将探索Docker如何与其他组件整合在一起，并用它们提供的工具集来便捷地提供高可用性的分布式系统。

1. 常用组件介绍（本文）
2. 容器化的综述（翻译中）
3. 服务发现和分布式配置存储（翻译中）
4. 网络与通信（翻译中）
5. 调度与编制（翻译中）


