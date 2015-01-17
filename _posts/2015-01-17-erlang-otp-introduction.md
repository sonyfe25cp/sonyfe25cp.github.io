---
layout: post
title: "Erlang入门之OTP介绍"
category: erlang
---

这一篇文章来介绍Erlang OTP的基本知识和如何使用。

OTP其实是Open Telecom Platform的缩写。这个名字并不能完全的概括它全部的功能。OTP是不仅是一个应用操作系统，它也是一系列依赖库和处理逻辑，用于创建一个容错、大型的分布式应用。它是Swedish爱立信创建的，并且也确实被用在了爱立信的系统中。

OTP包含了一堆工具，包括：一个完整的web server、一个ftp server、一个CORBA等。它还包含了一堆目前最好的H248, SNMP, and an ASN.1-to-Erlang工具。

