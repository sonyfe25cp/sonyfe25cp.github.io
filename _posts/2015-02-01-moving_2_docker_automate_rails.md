---
layout: post
title: "《Moving to Docker》系列之二：搭建一个私有registry服务"
category: docker
---



###Moving to Docker（三）基于Docker的Rails自动化部署

【译者的话】本文是《Moving to Docker》系列的最后一篇，整个系列讲述了创业公司如何把基础服务迁移到Docker上，以及迁移过程中的经验教训。本文主要是讲述了如何讲一个Rails应用自动化部署在Heroku上，并详细介绍了所需要的镜像的创建、自动化脚本的编写、rake打包执行和基本测试。

这是本系列的第三篇，整个系列介绍了我们公司如何把基础框架从PaaS移植到Docker上。

* 第一篇：介绍了我们在接触Docker之前的探索过程。
* 第二篇：介绍了如何搭建一个内网安全的私有registry。


在这最后一篇，我们用一个真实的例子来介绍如何自动化整个部署过程。

###基本的Rails应用

我们来进入主题并启动一个基本的Rails应用。在这个demo中，我将使用Ruby 2.20 和Rails 4.11。

在终端中运行：

    $ rvm use 2.2.0
	$ rails new  && cd docker-test
	
然后我们来创建个基本的控制器：

	$ rails g controller welcome index

然后修改`routes.rb`文件，使这个项目的根路径指向我们刚创建的`welcome#index`方法：

	root 'welcome#index'  
	
在终端执行 `rails s`来启动服务并打开`http://localhost:3000`就可以看到首页了。我们不准备做其他功能了，这只是个例子来证明我们可以创建并在容器里部署且正常工作。

###创建webserver

我们将用Unicorn做我们的webserver。添加`gem 'unicorn'`和`gem 'foreman'`到Gemfile然后安装它们（在终端执行`bundle install`）。

Unicorn 需要在Rails应用启动前配置，我们需要在*config*文件夹中放入*unicorn.rb*配置文件。这里有个[例子](https://gist.github.com/chasseurmic/0dad4d692ff499761b20)关于Unicorn的配置。你可以直接复制粘贴这个Gist的内容。

然后我们添加包含下面内容的Procfile到项目的根目录下，这样我们就可以用*foreman*来启动项目了：

	web: bundle exec unicorn -p $PORT -c ./config/unicorn.rb

如果你用`foreman start`来运行应用，它也能正常工作并且可以在`http://localhost:5000`上进行访问。

###创建Docker镜像

接下来我们来创建运行应用的镜像文件。在Rails项目的根目录创建个Dockerfile文件，并把下面的内容贴进去：

	# Base image with ruby 2.2.0
	FROM ruby:2.2.0

	# Install required libraries and dependencies
	RUN apt-get update && apt-get install -qy nodejs postgresql-client sqlite3 --no-install-recommends && rm -rf /var/lib/apt/lists/*

	# Set Rails version
	ENV RAILS_VERSION 4.1.1

	# Install Rails
	RUN gem install rails --version "$RAILS_VERSION"

	# Create directory from where the code will run 
	RUN mkdir -p /usr/src/app  
	WORKDIR /usr/src/app

	# Make webserver reachable to the outside world
	EXPOSE 3000

	# Set ENV variables
	ENV PORT=3000

	# Start the web app
	CMD ["foreman","start"]

	# Install the necessary gems 
	ADD Gemfile /usr/src/app/Gemfile  
	ADD Gemfile.lock /usr/src/app/Gemfile.lock  
	RUN bundle install --without development test

	# Add rails project (from same dir as Dockerfile) to project directory
	ADD ./ /usr/src/app

	# Run rake tasks
	RUN RAILS_ENV=production rake db:create db:migrate  

利用上面提供的Dockerfile，通过下面的命令进行创建镜像：

	$ docker build -t localhost:5000/your_username/docker-test .
	
如果一切顺利，在长长的日志的最后能看到如下信息：

	Successfully built 82e48769506c  
	$ docker images
	REPOSITORY                                       TAG                 IMAGE ID            CREATED              VIRTUAL SIZE  
	localhost:5000/your_username/docker-test         latest              82e48769506c        About a minute ago   884.2 MB  
	
试试运行这个容器：

	$ docker run -d -p 3000:3000 --name docker-test localhost:5000/your_username/docker-test

现在应该可以用通过boot2docker虚拟机的3000端口来访问这个应用了（例如我的：http://192.168.59.103:3000）。

###用shell脚本来自动化

通过本系列的第二篇，你应该已经了解如何发布刚才新创建的镜像到私有registry并部署在一个机器上了，我们跳过这一部分来直接讨论如何自动化这个过程。

接下来将定义3个shell脚本，然后用rake将他们捆绑在一起执行。

####Clean

每次我们创建镜像并部署时，最后把所有的东西都清除掉。主要包括：

* 停止（如果已经运行），然后重启boot2docker
* 移除孤立Docker镜像（那些没有标签并且不再使用的容器）

把如下的**clean.sh**脚本文件放在项目的根目录。

	echo Restarting boot2docker...  
	boot2docker down  
	boot2docker up

	echo Exporting Docker variables...  
	sleep 1  
	export DOCKER_HOST=tcp://192.168.59.103:2376  
	export DOCKER_CERT_PATH=/Users/user/.boot2docker/certs/boot2docker-vm  
	export DOCKER_TLS_VERIFY=1

	sleep 1  
	echo Removing orphaned images without tags...  
	docker images | grep "<none>" | awk '{print $3}' | xargs docker rmi  

然后给这个文件可执行权限：

	$ chmod +x clean.sh
	
####Build

创建的过程基本上是前面的步骤重做一遍。创建一个**build.sh**文件在项目的根目录，然后把下面内容贴进去：

	docker build -t localhost:5000/your_username/docker-test .

记得给它可执行权限。

####Deploy

最后，来创建如下内容的**deploy.sh**文件：

    # Open SSH connection from boot2docker to private registry
    boot2docker ssh "ssh -o 'StrictHostKeyChecking no' -i /Users/username/.ssh/id_boot2docker -N -L 5000:localhost:5000 root@your-registry.com &" &
    
    # Wait to make sure the SSH tunnel is open before pushing...
    echo Waiting 5 seconds before pushing image.
    
    echo 5...  
    sleep 1  
    echo 4...  
    sleep 1  
    echo 3...  
    sleep 1  
    echo 2...  
    sleep 1  
    echo 1...  
    sleep 1
    
    # Push image onto remote registry / repo
    echo Starting push!  
    docker push localhost:5000/username/docker-test  

如果你对这部分有些不懂，请确认你已经理解了本系列的第二篇文章的相关部分。

同样，记得给这个文件可执行权限。

###用Rake将它们捆绑

将这3个脚本分开依次执行：

1. clean
2. build
3. deploy/push

由于开发者都是比较懒的，所以这会是一个很大的帮助。

最后一步是将这些打包起来，也就是用rake将三部分合在一起。

为了简单，你可以将这一堆代码直接粘贴在项目根目录下的Rakefile后面。打开Rakefile文件，然后把这些粘贴在最后：

namespace :docker do  
  desc "Remove docker container"
  task :clean do
    sh './clean.sh'
  end

  desc "Build Docker image"
  task :build => [:clean] do
    sh './build.sh'
  end

  desc "Deploy Docker image"
  task :deploy => [:build] do
    sh './deploy.sh'
  end
end  

尽管你可能不懂这些rake的语法（它们真的很棒！），但是我们这么做的目的很明显。我们在docker命名空间下声明了3个任务。

他们会创建以下3个任务：

* rake docker:clean
* rake docker:build
* rake docker:deploy

Deploy是依赖build的，而build依赖于clean，于是每次我们从终端这么执行：

	$ rake docker:deploy
这样，3个脚本就都会被按顺序执行。

###测试

为了验证它们可以正常工作，你只需要在应用的代码中做一点小的改动，然后执行：

	$ rake docker:deploy
然后来看看结果。一旦镜像被上传（初次执行会比较慢），你可以ssh进入你的生产环境服务器，然后pull（用ssh隧道）docker镜像并执行它。这非常简单。

好吧，可能它花了一些功夫来让所有的组成部分工作起来，但是一旦完成，在Heroku上部署的工作就变得非常简单。

P.S. 跟以前一样，请让我知道你的看法。我不确定这是最好的或者是最快的或者是最安全的方案来用Docker部署应用，但是我们确实是这么用它的。

1. 确认boot2docker启动并在运行。
2. 如果你不知道你的boot2docker虚拟机的ip，执行`boot2docker ip`
3. 如果你忘了如何用私有registry，请看本系列[第二篇](http://dockerone.com/article/174).


原文链接：[Moving to Docker](http://cocoahunter.com/2015/01/23/docker-3/)（翻译：陈杰）
＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝

**译者介绍**

**陈杰**，北京理工大学计算机学院在读博士，研究方向是自然语言处理在企业网络信誉评价方面的应用，平时也乐于去实现一些突发的想法。在疲于配置系统环境时发现了Docker，跟大家一起学习、使用和研究Docker。


