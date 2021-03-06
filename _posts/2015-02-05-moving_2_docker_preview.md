---
layout: post
title: "《Moving to Docker》译者序"
category: docker
---

无论是大公司还是小型创业公司，都有遇到过系统环境选择的问题、开发环境和生产环境不一致带来的问题和如何快速部署的问题，这些琐碎的问题不仅消耗了大量的时间去排查，而且还容易反复再犯。于是，如何优雅的解决它们并确保它们不去分散程序开发人员的精力是一件值得认真对待的问题。

本系列讲述的是一个创业公司在这方面的探索，他们把公司基础服务从PaaS迁移到Docker上，在Amazon上搭建自己的私有registry，并实现了Rails应用的快速部署方案。这些都是为了解决系统环境的差异带来的问题和琐碎的部署问题，这其中的方法和技巧值得学习。

该系列的第一部分介绍了公司在运行环境上的选择，从Heroku到Dokku，再到基于Docker的容器化环境。不仅从创业公司需要节省成本的角度，而且从实际项目开发中遇到的问题的角度来看待选择运行环境这个问题，最终选择了基于Docker的容器化环境，既节省开支，又满足需求。

该系列的第二部分介绍公司在部署私有化registry的实现细节，包括了选择DigitalOcean的虚拟机和Amazon的S3存储服务。出于保护知识产权和节省开支的考虑，创业公司通常会选择自己搭建私有registry，这不仅能让同事之间可以共享镜像，也能提高工作效率。其中，对VPS和W3的介绍可以帮助未使用过这类服务的朋友快速上手。

该系列的第三部分介绍了如何对Rails应用做一个基于Docker快速部署的解决方案，在这个问题上译者深有感触。Rails应用严重依赖Ruby的版本和Rails的版本，运行环境的差异问题曾经让译者苦不堪言。该篇以一个简单的Rails示例来介绍如何创建镜像和运行容器，然后介绍了实际部署中的3个脚本以及脚本的详细内容，最后用Rake命令将部署实现自动化，解决的非常优雅，值得借鉴。

本系列的翻译由我一人完成，社区的李颖杰和宋瑜进行了内容校对。希望对大家有帮助，也请及时提出文中翻译不当的地方，方便改正。

陈杰

2015-02-05

北理工
