---
layout: default
title: "两个网络程序学习网络编程和go and python语言"
category: project
---

###端口转发

功能：监听端口6666
api+原始ip+端口注册给该服务
客户端用api来调用时，将请求转发给正确端口

###代码工具

项目调用A-client，请求发送到A-server，然后A-server调用B-client，将请求发给B-server；具体代码实现在B-server上实现。

A-client为 移动端的httpclient代码
A-server为 tomcat上的servlet监听，接受rest风格的请求

B-client为 thrift生成的api的client代码, 这部分可以直接生成client，打成jar包
B-server为 实现thrift接口的具体后端代码

1. 测试thrift上增加非thrift的东西，它是否还能认
2. 如果A-server调用B-client的地方还需要人工干预，可以考虑在实现A工具的时候，读thrift.data，直接按照其接口去写，来实现跳过人工干预
