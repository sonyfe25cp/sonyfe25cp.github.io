---
layout: post
title: "Docker入门之管理容器中的数据"
category: docker
---

这一篇是来介绍如何管理容器中的数据和容器之间的数据。
这里主要有两种方法：

1. 数据盘
2. 数据容器

####数据盘

数据盘是一种在一个或者多个容器间的特殊设计的文件系统，它通过联合文件系统来提供一些对持久化数据和共享数据有用的特性：

1. 数据盘可以在多个容器间重复使用和共享
2. 对数据盘的更改是立刻生效的
3. 当你更新一个镜像时，数据盘不会受影响
4. 数据盘会一直持久化直到没有容器用它了。

**创建一个数据盘**

	$ sudo docker run -d -P --name web -v /webapp training/webapp python app.py
	
其中`-v`参数就是创建一个数据盘，并且这个参数可以用多次。它会在容器内创建一个数据盘，挂在在`/webapp`

同样也可以在Dockerfile中用VOLUME来创建不同的数据盘。

**映射host的文件夹到容器内**

	$ sudo docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py
	
这个命令可以把host机器的`/src/webapp`映射到容器的`/opt/webapp`目录，注意：如果容器内本来就有`/opt/webapp`那么会被替换成host的`/src/webapp`

这个就很有用了，可以把host机器上的源代码映射进容器中，来测试代码。注意：host机器的路径必须用绝对路径，而且如果该路径不存在，会新建一个。

这种方式在Dockerfile中是不存在的，因为不能保证所有的机器都有源路径，这样不利于可移植性。

默认挂在的数据盘都是可读可写的，可以限定为只读
	
	$ sudo docker run -d -P --name web -v /src/webapp:/opt/webapp:ro training/webapp python app.py
	
其中`ro`表示`read-only`

**映射单独文件**

docker也可以只映射一个文件，而不是一个文件夹

	$ sudo docker run --rm -it -v ~/.bash_history:/.bash_history ubuntu /bin/bash
	
####创建一个数据容器

如果需要共享一些持久化数据在多个容器之间，或者想在一个非持久化运行的容器中使用这些数据，可以通过创建一个数据容器的方式来实现。

首先，来创建一个带数据盘用于共享的容器

	$ sudo docker run -d -v /dbdata --name dbdata training/postgres echo Data-only container for postgres
	
然后，可以用`--volumes-from`来在其他容器中挂载这个数据容器

	$ sudo docker run -d --volumes-from dbdata --name db1 training/postgres
另一个

	$ sudo docker run -d --volumes-from dbdata --name db2 training/postgres

这个`--volumes-from`参数也可以用多次来挂载不同的数据盘。

另外，可以扩展这个挂载链

	$ sudo docker run -d --name db3 --volumes-from db1 training/postgres
	
如果移除了这些挂载容器，甚至是dbdata这个初始容器，数据盘也不会被删掉。如果要删掉它，需要执行`docker rm -v`来删掉最后一个挂载它的容器。这也方便在容器之间迁移数据。

####备份、重存和迁移数据

另外的数据盘功能就是备份、重存和迁移数据了。

	$ sudo docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup/backup.tar /dbdata

这里，我们先启动了一个容器，然后从dbdata挂载了数据盘，同样从host挂载了/backup目录，最后，利用命令`tar cvf /backup/backup.tar /dbdata`来把 /dbdata中的东西压缩到/backup目录中，完成备份操作。

同样，也可以把数据重新存到别的数据容器中。

首先，
	
	$ sudo docker run -v /dbdata --name dbdata2 ubuntu /bin/bash
然后：
	
	$ sudo docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar

通过这样的方法，自动化备份也就容易实现了。





















