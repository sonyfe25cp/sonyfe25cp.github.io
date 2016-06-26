
###容器和持久化存储


本篇文章是前一篇文章关于询问持久化存储对于容器是否是个好主意。我收到了很多上一篇文章的反馈，各方面的观点都有。于是，我认为值得回应一下对于持久化存储的三种"观点"。观点是加了引号的，因为我准备使用一句格言来论述：仅仅是因为你能做到，而并不是说明你应该去做。

在本文讨论持久化存储之前，先来从声明持久化存储的范畴。我已经同一些读者讨论过，在运行各类应用时如何能够不用持久化数据的。声明下，运行容器并不意味着完全摒弃数据持久化。但是，容器的传统持久层已经采用对象存储，如S3，数据库作为一项服务使用，如RDS，并且，数据库运行在虚拟机上或者裸机上。

在每个应用场景中，数据并不是存储在容器文件系统中，而是通过网络进行访问。当在这两篇博文中讨论持久化存储时，我们指的是挂载一个卷到容器宿主并且链接到容器。卷存在于容器之外，并且用于持久化事务数据。这些卷是容器宿主机上的本地存储，但它同样可以被网络文件系统和网络块存储使用。


关于事务数据持久化，我们来回顾下容器存储的三种方案：

原生云：按照纯粹的原生云的设计模式，例如那些Twelve-Factor应用提出的大纲。这个方案假定持久化数据并不是存储在容器中，而是作为后端服务，例如对象存储和数据库即服务。
这个方案可以确保容器和它们的数据持久化支持服务松耦合，同时也不需要那些会限制扩展的依赖。弹性不是通过共享基础设施提供，例如网络存储，而是通过应用运行在容器中，容器与容器管理平台的配合工作。

把容器作为虚拟机：为了利用容器带来的应用便携性的优点，一些用户将容器作为轻量虚拟机来使用。在这种方案中，一部分直接使用遗产应用，例如Oracle，同时将他们从虚拟机中迁移到容器里。这包括挂载共享网络存储卷到容器中作为数据存储使用。尽管这种架构可以起作用，它同样带来了实际的条件和限制。例如，重启一个容器，甚至一个共享存储，已经不同于在vSphere中重启虚拟机的核模式。当一个容器在一个新的容器宿主机上重启，它的期望是这个应用能够通过重启到数据库卷链接来恢复。相对于遗产应用假定为虚拟机基础设置将处理恢复，而不是需要设计应用。同样，如果便携性是迁移到容器中的一个原因，采用它们来替代虚拟机来安装遗产应用是这种便携性的反模式。在大卷中存储数据是紧耦合在容器上，这使得便携性非难实现。像这样的限制使得一个虚拟机直接容器化存在很多问题。
混合：由于没找到一个更好的词，第三种方案我将之称为混合，利用容器和卷来应对带事务持久要求的应用。然而，与上一中方法不同，这个方案缩小持久化存储方案的应用使用场景，它不需要共享基础设施来实现弹性和恢复。例如非关系型数据库，如Cassandra，它有可用性架构在应用程序层，并且它比遗产应用程序有不同的基础设施预期。在这个方案中，持久层产生价值，不是通过弹性，而是通过可编程和灵活，例如通过优秀设计的API来扩展存储。这个方案结合了持久层和或多或少的纯原生云设计模式。

我只能提供对该话题的一个回顾。我将会提供更多的细节关于上面提到的几个方案。同时，如果你计划参加在奥斯汀举行的OpenStack Summit，并且想听到关于这个话题的更多内容。我、来自SolidFire的John Griffith以及来自IBM的Shamail Tahir已经提交了一个关于容器持久化存储的话题。请认真考虑为这个话题投票，投票期为2月9日至2月17日。同样的，欢迎给这篇博文进行反馈、修正或者反驳。


原文：[Follow Up Post: Using Persistent Storage With Containers](http://cloudarchitectmusings.com/2016/02/10/follow-up-post-using-persistent-storage-with-containers/)
