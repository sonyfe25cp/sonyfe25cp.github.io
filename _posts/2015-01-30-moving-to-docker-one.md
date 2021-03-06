---
layout: post
title: "《Moving to Docker》系列之一：为什么选择Docker"
category: docker
---



【译者的话】这个系列的文章讲述创业公司如何把基础服务迁移到Docker上，以及迁移过程中的尝试。

这是迁移至Docker系列的第一篇，这个系列介绍了我所在的公司是怎么把基础设施从PaaS迁移到Docker上的。如果你对基础已经很了解了，可以直接看本文底部的几个技术专题。

上个月，我一直在折腾开发环境。这是我个人的经验和故事，关于如何在Docker上简化Rails项目的部署工作。
当我开始创建[Touchware](http://www.touchwa.re/)的时候，我还是个独立开发者。项目比较小，也不复杂，也不需要维护，甚至不需要部署到很多机器。经过去年一年的发展，我们成长为有10名员工的公司了，同样在增长的还有我们的服务端程序和API.

####Step1 Heroku

虽然我们还是个小公司，但是我们还是需要让事情尽可能的便捷。当我们在寻找解决方案时，我们希望找到可以帮助我们减轻对硬件依赖负担的工具。由于我们主要开发RoR项目，而[Heroku](http://www.heroku.com)不仅对RoR有很好的支持，而且还提供常用的数据库（Postgres/Mongo/Redis等），于是我们就明智的使用了它。

Heroku有很好的技术支持和文档说明使得部署工作非常轻松。唯一的问题是，当你的公司还处于发展阶段，开销很多，而用Heroku也不是很划算。

####Step2 Dokku

为了尝试并降低成本，我们决定试试[Dokku](https://github.com/progrium/dokku), 引用GitHub上的一句话来说，Dokku是迷你版本的Heroku。
>Docker powered mini-Heroku in around 100 lines of Bash

我们在[DigitalOcean](http://www.digitalocean.com)上购买了很多台机器，都预装了Dokku.Dokku大多时候跟Heroku很像，但是当有的项目需要调整配置参数或者是需要特殊的依赖时，它就无能为力了。我们有一个应用，它需要对图片进行多次转换，这导致我们找不到一个适合版本的imagemagick运行在安装了Dokku的Docker容器内。尽管我们还有很多应用运行在Dokku上，但我们还是不得不迁移一些到Heroku。

####Step3 Docker

几个月前，由于开发环境和生产环境的问题，几个项目需要重新调整，我决定使用Docker。Docker简单来说就是用于容器化应用，简化部署工作。由于一个Docker容器已经包含项目运行所需要的所有依赖，只要它能在你的笔记本上运行，就能在任何一个别的远程服务器的生产环境上运行，包括Amazon的EC2和DigitalOcean上的vps。

Docker IMHO特别有意思的原因是：

* 它促进了模块化和关注点的分离：你只需要去考虑应用的逻辑部分（负载均衡：1个容器；数据库：1个容器；web服务器：1个容器）
* 在部署的配置上比较灵活：容器可以被部署在大量的HW上，也可以容易地重新部署在不同的服务器或者服务提供者上。
* 它允许非常细粒度的优化应用的运行环境：由于你可以为你的容器自己创建镜像，就可以自己去配置环境。

它也有一些缺点：

* 它的学习曲线非常的陡峭（这是从一个软件开发者的角度来看，而不是经验丰富的运维人员）
* 搭建环境不见得，尤其是还需要自己搭建一个私有的Registry。

在接下来的几篇文章中，将会介绍如何搭建一个半自动化的Docker部署系统。

* 搭建一个私有Registry(翻译中)
* 配置Rails项目的半自动化部署方案(翻译中)

**原文链接：** [**Moving to Docker**](http://cocoahunter.com/2015/01/23/docker-1/)(翻译：陈杰)

