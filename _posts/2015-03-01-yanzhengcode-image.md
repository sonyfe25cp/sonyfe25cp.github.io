---
layout: default
title: "验证码识别"
category: project
---

###设计

一个服务端，两种客户端

####消费者客户端：

1. image fetchImage(remoteServer)
返回一个验证码图片
2. code returnImage(remoteServer, code, imageId)
返回验证码内容，获得结果

####生产者客户端：

1. code sendImage(remoteServer, Binary)
发送Binary给server，等待code回来

####服务端：

1. 接收Image，然后保存imageId，放入Quene B
2. 接收fetch请求，从Quene A中取图，若Quene A为空，则从Quene B中取
3. 接收验证码结果，次数小于阈值，放入Quene A，并与Quene A中同imageId的结果进行比较，然后返回当前请求的结果；当次数大于阈值，判断正确结果，然后返回给生产客户端结果。

###流程

用户访问dns时，判断QueneB的状态，若QueneB不为空，则启动代理，将请求指向fake server，然后用户跳转到fake server的验证码页面，然后用户输入验证码，返回正确页面，记录该用户ip。



