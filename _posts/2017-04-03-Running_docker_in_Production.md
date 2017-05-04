
Running Docker in Production: three use cases and the good, the bad and the ugly

生产环境中的Docker：三种应用场景，优点、缺点、痛点

【编者的话】xxx

在2017年1月17日的Helsinki的首届Docker线下见面会中，Solita、Zalando和Pipedrive公司分别介绍了Docker在生产环境中的实践，包括案例及相应的输入输出。同时，也介绍了Docker在生产环境中的优点、缺点和痛点。

###Solita的使用场景

首先，Solita公司的Heikki Simperi介绍了他们公司如何利用Docker来处理多种app和芬兰国家铁路管理系统（VR）的关系，以及他们在使用Docker过程中遇到的问题。

Solita在Docker上运行了多种app，包括火车司机导航系统、通知系统和交通控制app。Heikki特别提到在铁路管理系统中减少系统停机维护时间的重要性。他的原话是：“anything over a 3-5 min downtime causes delays for trains, but nobody dies.”

他们在使用Docker过程中遇到的大部分问题大致包含以下几个方面：构建镜像、创建私有仓库、移除或者启动容器以及app中存在的bug。总的来说，他们使用Docker过程中一直是稳定的，并且是零停机维护。

他们之前遇到的问题在升级Docker版本之后或减少或解决。他们正期待Docker1.12稳定版本在RedHat官方渠道上的发布。

###Zalando的使用场景

随后，Rami Rantala 介绍了Zalando公司的各个团队和项目中是如何使用Docker用于部署生产系统。

在Zalando Helsinki的办公地，这里有超过100多名工作人员和多个团队，他们负责不同的生产系统、相关探索研究、持续交付等工作，采用敏捷开发的方式开发他们的平台。

每个自主团队均有独立的AWS账号，而且无视了不可以在生产环境运行Docker的建议。据Rami介绍，在Zalando内，任何运行在AWS上的程序都采用了Docker运行，Docker被认为是唯一允许用于部署生产环境的工具。他们同样有独立的Docker资源库（Pierone），他们自主的Docker基础镜像和每个实例运行一个全栈容器。

有很多领域还存在改进的空间，包括如下内容：部署太慢，他们丢失了Docker中层通信的优势，每个实例运行一个容器的代价很高，导致存在数千个实例；另一个问题在于Docker经常拖垮Pierone。

他们目前正调研使用Kubernetes，它看起来可以降低成本，对AWS账号和基础设施要求更低，更高效的利用容器间通信和快速简单的部署。他们在技术周进行了技术演练，并计划在第二季度用于生产环境灰度发布。

###Pipedrive的使用场景

最后，Renno Reiurm讨论了Pipedrive公司在使用Docker中的收获与痛点。这是家致力于帮助小微企业控制复杂销售流程的公司，成立于2010年，目前拥有30000家付费客户，并有200多名雇员和Tallinn, Tartu和New York有三处办公地。

Pipedrive采用Docker并感受到了随着业务增长，Chef带来的各种问题。由于很少去编写配置单，使得容易遗忘Chef的工作原理，而学习一门新的语言和工具又存在一个准入门槛。

他们最初的Docker平台是在Vagrant虚机环境中，后来他们迁移到定制化docker宿主机，最近迁移到了Docker4Mac。
















































===
**原文链接:**[http://blog.kontena.io/docker-in-production-good-bad-ugly/](http://blog.kontena.io/docker-in-production-good-bad-ugly/)
