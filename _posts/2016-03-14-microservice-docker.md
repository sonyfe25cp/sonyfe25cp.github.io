0 to Microservice in 5 minutes with Go, go-microservice-template and Minke

5分钟学习基于Go，go-microservice-template，Minke的微服务

###介绍

几周前我去参加一个零售环境下的技术会议，直到午饭时间都没人提及'Docker'或者'微服务'，我就找了个借口离开了。
每个人都在讨论微服务，甚至是某天公交司机都会抱怨公交迟到的原因是他们的调度微服务出了问题。
从他的描述中得知，错误的配置被推送到了服务中，却没人知道。
于是，最后一部分内容我假定你经历过这种痛苦，如果没有的话，没关系，当它发生的时候你就能体会了。
除非你的工作是核导弹发射系统，并且你可能会消灭掉半个星球上的生命，那还是不要体会的好。

尽管很多人都在讨论微服务，但是框架和开发实践却还不够成熟，有很多知名企业，如Netflix何USwitch，它们使用了这个技术很多年了，但是知识和模式依然进展缓慢。罗马不是一天造成的，这话同样适用于微服务框架。虽然有很多惊艳的东西出现，关键还是要有大规模的应用和贡献。


这篇文章将展示如何在5分钟内从零开始了解微服务，包括架设一个新服务、本地测试、编译和持续集成测试。假定你已经有Docker工具箱、Ruby和Go语言，否则这些东西可能会超过5分钟，那就与题目不符了。

###框架

当我二次开发一个由C#写的项目时，我开始构建我的框架，我希望它可以足够小，并且覆盖90%的用例。与我思路一致的是Google的Go语言，Cucumber和Ruby作为单元测试，Docker作为平台。

当我开始用Go构建微服务时，我发现我需要一遍遍的重复同样的构建步骤。
我需要为功能测试复制部分末班代码，同样还有构建脚本。
这变的不可管理直到我玩成了第二个服务，我学会了很多东西。
当我第一次构建`go-microservice-template`，尽管一段时间内的确很有效，但是它只方便与添加新的知识模板，但不便于修改现有服务。

`go-microservice-template`依然用于构建基础代码，但是构建和测试工作由单独的Rubygem完成，Minke。目前它们运行良好，当我从Jenkis服务器切换到CircleCI时，将构建和测试代码本地化到一个gem中被证实很有用，同时发现并不能使用Docker的exec命令。能够更新一个中央Gem可以节省很多复制和粘贴的时间。

###Go 微服务框架

####安装和构建一个微服务

假定你的GOPATH设置正确，它将用于下载一份模板到你的本地机器，你可以简单的执行下面语句：

	$ go get -u -v github.com/nicholasjackson/go-microservice-template
	
这会从github下载源码

	$ $GOPATH/src/github.com/nicholasjackson/go-microservice-template
	
构建一个微服务，你只需要执行下面的命令

	$ go run generate.go
	
然后，你的屏幕上将出现下图并有一堆问题：

1. 这个微服务的命名空间是什么？这里是你的github或者bitbucket路径，我的是github.com/nicholasjackson.
2. 微服务的名字是什么？这里填你的服务名。
3. 包括StatusD？如果你希望引入StatusD，我会后续来介绍它。要是日志被详细的记录了，公车司机就不会被迟到了。
4. 正确么？`y`用来确认上述配置。

这个应用将会构建微服务，并放置内容在：

	$GOPATH/src/[your_namespace]/[service_name]
	
这个服务已经为编译和部署准备好了，配有两个端点，单元测试和功能测试。

###Sprint 0

构建这个服务并部署到生产环境。如果我不是每次都因为软件或人员的服造型导致项目被延迟，我也不会写这篇博文。我设计这个模板来执行和测试，使得下一次任务可以在持续交付管道中继续运行。

我推荐不仅仅在开发和测试环境中采用构建管道，同样也要在生产环境中。你可以不需要暴露服务，但是我推荐每次当你提交到主分支时都部署到生产环境。

###深入构建服务

对于构建，我之前也提到过我是Bauhaus风格的粉丝，干净的线条和极简主义。然而个别的组件使我不得不在每个服务中都有。

1. 依赖注入。我当前的是用Facebook的注入包：github.com/facebookgo/inject
2. 路由。尝试并测试过，都用Gorilla。地址：github.com/Gorilla
3. 访问验证。如我朋友Yan Ettles说的“输入验证，输入验证，输入验证”重要的事情说三遍。安全很重要，这里采用github.com/asaskevich/govalidator.
4. 日志。度量很重要，个人喜好使用StatusD，同时如果你选择了这项，那么Graphite会收集这些度量。如果你想切换到Datadog而不是Graphite，那么也有个Datadog的客户端支持StatusD，地址：github.com/alexcesaro/statsd.


###依赖注入

Facebook的框架有以下优点：

1. 共享对象的实例
2. 实例命名，当框架不能通过类型来推断时很有用，如多个类实现了同一个接口。
3. 私有对象，对于注入的类是唯一的

这看起来并不像是Go专属，并且也有一堆疑问关于为什么要在Go里用依赖注入，毕竟可以通过共享引用包来解决。对我来说，很大程度上是因为使用共享包时代码的丑陋和脆弱，同时Facebook的注入包还有一些不错的特性，例如当创建一个对象时，它会自动遍历树结构来创建和注入子依赖。以`handlers/health.go`和`handlers/health_test.go`为例。

type HealthResponseBuilder struct {
	statusMessage string
}

func (b *HealthResponseBuilder) SetStatusMessage(message string) *HealthResponseBuilder {
	b.statusMessage = message
	return b
}

func (b *HealthResponseBuilder) Build() HealthResponse {
	var hr HealthResponse
	hr.StatusMessage = b.statusMessage
	return hr
}

type HealthDependenciesContainer struct {
	// if not specified will create singleton
	SingletonBuilder *HealthResponseBuilder `inject:""`

	// statsD interface must use a name type as injection cannot infer ducktypes
	Stats logging.StatsD                    `inject:"statsd"`

	// if not specified in the graph will automatically create private instance
	PrivateBuilder *HealthResponseBuilder  `inject:"private"`
}

需要警告的是，在health里还有一些奇怪的代码。我通常不会这么写代码，但这是一个简单的方法作为Facebook注入的例子。

###路由

谈到路由，自从我的第一个go项目就在使用Gorilla，它速度快且满足我的需要。handler包处理路由，并且用Gorilla创建个新的路由也很简单。

r.Get("/v1/health", HealthHandler)

r.Add("POST", "/v1/echo", requestValidationHandler(
	ECHO_HANDLER+POST,
	reflect.TypeOf(Echo{}),
	RouterDependencies.StatsD,
	http.HandlerFunc(EchoHandler),
))

我们采用两种方法来注册，Get请求的处理器（一个路径和一个函数），而Add则更复杂。这些复杂的选项可以方便在处理器调用前添加中间件，也就是我们增加输入验证的地方，这个在下一节会介绍。

###请求验证

go的请求验证包非常给力，地址： github.com/asaskevich/govalidator。它可以使用结构标注来定义属性所对应的验证，然后当发生错误时，仅通过调用验证器上的一个方法就可以返回一组错误信息和一个错误对象。

type Echo struct {
	Echo string `json:"echo" valid:"stringlength(1|255),required"`
}

request = Echo{}
request.Echo = "Valid String"

errors, err = govalidator.ValidateStruct(request)

当把这个代码放入中间件中，如requestValidationHandler，确认已经让路由在到达处理器前先经过它，这也就是说我们可以为每个处理器写一个请求验证。govalidator对每一种可能的场景都有考虑，包括验证字符串是否是url或者网络地址，这些在文档中都有。

###日志

日志是必要的，我推荐使用StatusD或者类似提供一堆一堆度量的工具。有很多可选的项目，如Gauges,Counters, Timing Summary Statistics和Sets，不论你选择选择哪个，正确的日志数据不仅可以帮助你调试，而且可以帮助建立预警系统，例如：如果运行失败，Datadog可以半夜把你叫醒。在模板中的简单实现采用了Increment(Counter)工具，但是添加其他度量也是很容易的。当你运行这个应用时，Graphite也会运行，可以通过http://192.168.99.100:8080/来访问，其中192.168.99.100是指你的docker机器ip。

我已经在handler包的`const.go`中设定了可选来预定义各种标签。尽管你不想在你的微服务中只定义一种通用度量标签格式，但是可以认为这是一种编码标准。在所有的微服务中都坚持用同一种格式，它可以让控制台简便1000倍，而且你也不需要花一周时间在20个微服务中去重构所有的标签，因为它们实在太难懂了。

package handlers

const GET = ".get"
const POST = ".post"
const CALLED = ".called"
const SUCCESS = ".success"
const BAD_REQUEST = ".bad_request"
const INVALID_REQUEST = ".invalid_request"
const VALID_REQUEST = ".valid_request"

const HEALTH_HANDLER = "helloworld.health_handler"
const ECHO_HANDLER = "helloworld.echo_handler"

###测试

用`go-microservice-template`测试可以分两步：

1. 单元测试（Go）
2. 功能测试（Cucumber）

在写代码时，我采用了外部的方法学，这是我所偏好的，于是我的框架也是这么做的。我确实说过这是一个主观的框架，对吧？

####单元测试

对于Go来说，可以通过HTTP服务器直接独立测试handler，这使得测试handler的逻辑很快速。与依赖注入框架合在一起可以排除依赖或者注入的问题，这样会有一个高效的测试驱动开发。

下面的代码是其中一个测试，模拟请求并检查返回结果，由于这里并没有HTTP服务器引入，使得这些测试可以快速的执行。这使得我们可以像其他逻辑测试一样写全面覆盖handler的测试。唯一不能测试到的就是路由部分和服务启动，但是这些可以通过功能测试覆盖。

func TestEchoHandlerCorrectlyEchosResponse(t *testing.T) {
	echoTestSetup(t)

	var responseRecorder *httptest.ResponseRecorder
	var request http.Request

	responseRecorder = httptest.NewRecorder()

	echo := Echo{Echo: "Hello World"}
	context.Set(&request, "request", &echo)

	EchoHandler(responseRecorder, &request)

	body := responseRecorder.Body.Bytes()
	response := Echo{}
	json.Unmarshal(body, &response)

	assert.Equal(t, 200, responseRecorder.Code)
	assert.Equal(t, response.Echo, "Hello World")
}

####功能测试

所有的功能测试都在_build/features文件夹，并且用Gherkin代码写的。Cucumber是一个选择，Ruby和Rspec正好可以实现这个功能，同时确实只有少量的Cucumber插件，于是只能自己实现一些。照着下面的方法，可以学会创建一些重要的集成测试，同时学会Cucumber中服务的表达方法，它们定义了你和客户端的交互。

Scenario: Echo returns same data as posted
	Given I send a POST request to "/v1/echo" with the following:
	| echo | Hello World |
	Then the response status should be "200"
	And the JSON response should have "$..echo" with the text "Hello World"
	
通过使用一个类似`cucumber-rest-api`的gem，上述Cucumber特性将会被执行而不需要额外文件。这并不难掌握，所有反对Cucumber的人都在抱怨它的缓慢和脆弱的测试套件。我尝试去做一个声明，你不会喜欢它并且它可能成为评论中的大部分，但是我就是要用它。

####并不是Cucumber弱，而是你弱

测试你交互的行为，小的整合和确信你的单元测试来覆盖剩余部分，旺旺在功能测试的测试覆盖率实在是太高了。微服务的另一个好处是你的测试覆盖率将分布在多个库中。

###Minke

Minke对Docker来说，是一个基于像`go-microservice-template`一样的微服务主观性编译系统，它封装了所有的样板构建脚本并封装起来，这样你只需要像下面的提取那样来处理配置文件。对Minke我不会介绍太多，可以去阅读https://github.com/nicholasjackson/minke，接下来将看一下用于编译和测试微服务的命令。

---
go:
  namespace: 'github.com/nicholasjackson'
  application_name: 'helloworld'
docker_registry:
  url: <%= ENV['DOCKER_REGISTRY_URL'] %>
  user: <%= ENV['DOCKER_REGISTRY_USER'] %>
  password: <%= ENV['DOCKER_REGISTRY_PASS'] %>
  email: <%= ENV['DOCKER_REGISTRY_EMAIL'] %>
  namespace: <%= ENV['DOCKER_NAMESPACE'] %>
  
在`_build`文件夹中是一个Gemfile，它里面有所有的Ruby依赖，如Minke，在编译服务之前需要安装这些包。这里我推荐使用RVM来做Ruby解释器，并管理不同版本的Ruby和它的依赖。

1. 确认Docker服务器正在运行，并配置相应的docker环境变量。
2. 用`bundle install`安装相应gems
3. 用`rake app:build_server`来编译服务，它会下载go依赖包、执行单元测试、编译linux下的应用，并打包放入Docker镜像中
4. 用`rake app:cucumber`执行功能测试，并采用Docker Compose来调试环境并执行Cucumber测试。运行正常的话，将在终端下看到下面的界面；如果不能正常运行，很有可能是你没有下载好相关依赖，如Docker是否正确安装并运行。

###Consul

传递配置到服务中，我们将采用`consul`和`consul template`，这个`consul template`能够在`_build/dockerfile/[servicename]/config.ctmpl`
中找到。Consul模板在容器间作为服务运行，同时与Consul服务器检测变更。它会根据相应的key来写入value，同时在检测到变更时重启服务器。配置文件存储在`consul_keys.yaml`文件中，当运行应用或者启动测试时，Minke会自动导入consul。下面是一个简单的Consul模板例子，Consul用Go语言编写，同样模板也是Go模板。

{
  "stats_d_server": "{ {key "app/stats_d_server"} }"
}

###Docker容器

Docker是我的最爱，事实上我已经不想用任何其他工具来打包和运行应用了。至于其他服务，如Docker Cloud、Amazon Container Service、Google Container Service全都一去不复返了。Docker就是这么完美，它带来的痛苦将远低于不用它带来的痛苦。

Minke编译你的代码并打包，可从`_build/dockerfile/[servicename]/`来查看更多内容，但是`go-microservice-template`创建的dockerfile是基于Alpine系统，它可以创建更小的镜像。如果想了解更多内容，可以阅读另一篇博文，地址是： http://nicholasjackson.github.io/other/micro-docker-images-for-microservices/.

###Docker Compose

当你运行或者测试应用时，Minke采用Docker Compose。如果你查看compose文件，你会看到consul和statsD服务器组件。当我的服务在复杂度上增长的像文件中描述的这样，我通常用Mimic来控制各种依赖。我试图将数据库、队列等实际依赖降到最低，坚持微服务就应该尽可能小的原则，毕竟如果你需要连接多个数据库，你的微服务可能有点大了。所有的其他连接都应该被其他服务所管理。

helloworld_test:
  image: helloworld
  ports:
    - "8001:8001"
  environment:
    - "CONSUL=consul:8500"
  links:
    - consul:consul
    - statsd:statsd
consul:
  image: progrium/consul
  ports:
    - "9400:8400"
    - "9500:8500"
    - "9600:53/udp"
  hostname: node1
  command: "-server -bootstrap -ui-dir /ui"
statsd:
  image: hopsoft/graphite-statsd
  ports:
    - "8080:80"
    - "2003:2003"
    - "8125:8125/udp"
    - "8126:8126"
    
    
    
###CI/CD

我先前提到过`Sprint 0`使得`helloword`在CI下运行并部署到环境中，但是我并没有强调它可以多大程度上保持CI管道的健康。我最近切换到CircleCI下，因为它们可以像编译微服务一样编译iOS和Android。为了使得基于Go的微服务在CircleCI下运行，首先花了点时间去调研它的自动探测和编译过程是靠谱的，并不需要用Minke去做。如下面的配置所示，它在配置方面足够简单了。

为了让服务编译顺利，你所需要做的就是修改依赖部分的路径，指向你的命名空间和项目名称。我超爱CircleCI，现在它只要4分钟就可以编译服务并且这些时间是来搭建环境，而且UI也很简单易用。当我要找到这个配置时，它SSH到编译宿主的能力也是非常棒的。

machine:
  pre:
    - curl -sSL https://s3.amazonaws.com/circle-downloads/install-circleci-docker.sh | bash -s -- 1.10.0
  Ruby:
    version: 2.2.4
  services:
    - docker
  environment:
    GOPATH: /home/ubuntu/go

dependencies:
  override:
    - cd _build && bundle
    - mkdir -p /home/ubuntu/go/src/github.com/nicholasjackson
    - cp -R /home/ubuntu/helloworld /home/ubuntu/go/src/github.com/nicholasjackson/

test:
  override:
    - cd _build && rake app:build_server
    - cd _build && rake app:cucumber
    
###总结

本篇文章到此结束，5分钟从零开始学习微服务主要有以下步骤：

1. 构建个新服务（go run generate.go）
2. 执行编译脚本(rake app:build_server)
3. 执行测试(rake app:cucumber)
4. 创建circle.yml配置
5. 通知CircleCI监控你的项目
6. 推送代码到库
7. 倒杯水，然后等着编译变绿

对我来说，随着我学的更多这永远是工作进步，我会继续更新Minke和`go-microservice-template`。如果你有想法，欢迎给我留言、提bug，也欢迎来贡献代码。

###源码

地址：https://github.com/nicholasjackson/helloworld.

###依赖

相关项目的地址：

* Ruby https://rvm.io/
* Go https://golang.org/
* Docker Toolbox including Docker Compose https://www.docker.com/products/docker-toolbox
* CircleCI https://circleci.com/









































