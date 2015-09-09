Innovative Docker Log Management

【编者的话】本文介绍了一个新的工具SPM，它用于解决在多容器环境下日志管理所遇到的问题，同时它整合了多种功能，避免了以往需要安装多种工具的麻烦，配合Kibana的展示功能，使得该工具还是值得一试的。

在微服务流行的今天，日志路由和解析的传统静态配置方法已经有点吃力；事实上，它还带来了额外的复杂度和资源消耗。相对的，这使得不能运行在单机上的微服务的数量降低了。



在[SPM for Docker](http://blog.sematext.com/2015/06/09/docker-monitoring-support/)整合的日志管理功能中，对微服务进行了支持，方式为降低安装复杂度、启动时间和最小化占用资源。[SPM Agent for Docker](https://github.com/sematext/spm-agent-docker)汇集了标准、事件和日志，并且它运行在每一个Docker宿主机中的一个容器。除了标准的日志收集功能，我们最近还整合了自动日志格式探测功能和容器日志信息的值域抽取。这个过程被嵌入在Docker API数据流中，日志被收集起来送给统一日志索引工具--[Logsene](http://sematext.com/logsene/)。这就意味着并没有为Docker日志驱动配置syslog，也没有路由到Logstash中去解析，并且也没有Elasticsearch来维持这些日志，甚至是连Kibana也不需要。SPM for Docker和Logsene将为你处理好这一切。

有很多方法从Docker中收集日志(你可以阅读我们的另一篇文章：[Docker Logging Webinar](http://blog.sematext.com/2015/08/11/docker-logging-webinar/))；下面来介绍使用Logsene处理Docker日志的优点。

对于新手来说，我们从这几方面来了解日志管理功能：

* 配置 - SPM for Docker中日志的配置
* 自动格式探测和容器日志解析
* 规则的相关性
* 日志的报警检测和异常检测
* Logsene和[Kibana](http://blog.sematext.com/2015/06/11/1-click-elk-stack-hosted-kibana-4/)的可视化
* 从UI中、命令行中搜索日志并用UNIX工具处理日志

1. 配置 - SPM for Docker中日志的配置

首先，我们使SPM for Docker获取Docker事件和Docker 标注的日志非常的渐变。在安装方面没什么好说的，只需要增加`-e LOGSENE_TOKEN=your-logsene-token`到SPM Docker Agent的Docker run的命令中，然后你就可以获取Docker容器的全部日志并放入Logsene。就这么简单！

并不是所有的日志都有对我们有用，这就是为什么我们提供了一个通过镜像名或者容器名的白名单或者黑名单来控制日志输出。相关的参数如下：

* -e MATCH_BY_NAME – 容器名白名单的正则表达式
* -e MATCH_BY_IMAGE - 镜像名白名单的正则表达式 
* -e SKIP_BY_NAME – 容器名黑名单的正则表达式
* -e SKIP_BY_IMAGE - 镜像名黑名单的正则表达式 
* -v PATH_TO_YOUR_FILE:/etc/logagent/patterns.yml – 可选，自定义日志解析

一个筹集Metrics、Events和Logs的例子：

	docker run --name spm-agent \
	-e HOSTNAME=$HOSTNAME \
	-e SPM_TOKEN=YOUR_SPM_TOKEN \
	-e LOGSENE_TOKEN=YOUR_LOGSENE_TOKEN \
	-e MATCH_BY_IMAGE=”nginx|mysql|mongodb|myapp” \
	-v /var/run/docker.sock:/var/run/docker.sock \
	sematext/spm-agent-docker
	
2. 容器日志自动解析

Docker日志是容器们的终端输出流。这些数据中混合着来自启动脚本的文本信息和来自应用的结构化日志。问题很明显，并不能直接对日志事件流进行直接处理。需要区分出哪些日志来自哪个容器、应用，并正确的解析。

SPM for Docker分析了行格式，并将其转换为JSON格式，使得所有的值域可见，并且从文本日志中抽取属性域。过去，通常需要使用日志处理工具，如Logstash、Fluentd或者rsyslog来处理日志信息，但是这些的配置是根据输入源来静态配置。*这就不能够适应动态微服务的环境。*有些人已经对syslog驱动、解析器配置、日志路由甚至其他部分做了更改。这就是为什么我们要整合自动格式检测到SPM中的原因，利用logagent-js来处理文本。这个整合的footprint很少，并不需要将日志导到外部服务，并且它为流行的应用进行日志格式探测，并生成JSON和以行为单位的日志输出。

例如，SPM Docker Agent可以从下面这些官方镜像中解析日志：

* MongoDB, Mysql, Redis
* NGINX, Apache
* Logstash或bunyan支持的任何JSON输出
* 带或者不带时间戳的文本信息
* 任何采用mount属性挂载的自定义模式，如“-v /mypatters/patterns.yml:/etc/logagent/patterns.yml”

探测和解析日志信息的logagent-js是一个开源的项目，欢迎贡献更多的日志格式解析。

3. 指标相关性

由于日志存储在Logsene中，它提供了分析相关性能的能力，如CPU使用率、内存使用率、网络以及硬盘I/O。你可以在SPM中很方便的查看与任何性能指标有关的日志信息。如果你在公司中负责解决性能问题或者其他相关问题，你就会明白它会节省你多少时间去获取度量指标、日志和事件。

4. Docker日志上的报警

Logsene提供了定义报警的功能，同时还提供了日志的异常检测，例如错误信息、警告信息、安全漏洞等。

更多这方面的信息请查阅[这个主题](http://blog.sematext.com/2015/06/23/log-alerting-anomaly-detection-scheduled-reports/)。

5. 采用Kibana展示

我们对全部的Logsene应用提供了[一键到Kibana](http://blog.sematext.com/2015/06/11/1-click-elk-stack-hosted-kibana-4/)功能。如果你的日志包含数值型数据，又或者你想生成日志频率、消息类型、高频错误的统计信息，在Logsene中可以很轻松的创建Kibana控制台。

6. 在命令行中搜索日志并处理并用UNIX工具处理

如果你活在终端的世界里并热爱命令行工具，可以使用[Logsene命令行解释器](http://blog.sematext.com/2015/07/07/logsene-cli/)。没有必要为了搜索而使用UI界面，也没有必要用JSON查询。使用Logsene+awk+grep+sort+uniq+...任何你需要的即可。

###五分钟快速上手

恭喜！你已经掌握了很多信息。如果对刚刚阅读的内容有兴趣，可以[注册](https://apps.sematext.com/users-web/register.do)账号，为Docker运行SPM并且亲自体验下那些吸引你的功能。初创企业、没有或者只有很少投资资金的创业公司、非营利性企业以及学术机构有[优惠价格](http://sematext.com/logsene/#special-pricing)，快来联系我们吧。如果你可以帮助我们把SPM和Logsene做的更好，请[加入我们](http://sematext.com/about/jobs.html)吧！

**原文链接：[Innovative Docker Log Management](http://blog.sematext.com/2015/08/12/docker-log-management/) （翻译：陈杰）**

===============================================

**译者介绍**

**陈杰**，北京理工大学计算机学院在读博士，研究方向是自然语言处理在企业网络信誉评价方面的应用，平时也乐于去实现一些突发的想法。在疲于配置系统环境时发现了Docker，跟大家一起学习、使用和研究Docker。














 