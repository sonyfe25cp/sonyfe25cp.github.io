
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









===
**原文链接:**[http://blog.kontena.io/docker-in-production-good-bad-ugly/](http://blog.kontena.io/docker-in-production-good-bad-ugly/)
