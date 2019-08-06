# netty
## netty是什么？
netty是异步事件驱动的网络编程框架
## 为什么要使用netty？
netty是基于jdk的nio？为什么我们不使用nio或者是nio的其他框架？
1.使用jdk自带的nio需要了解太多的概念，编程复杂
2.netty底层IO模型随意切换，而这一切只需要做微小的改动
3.netty自带的拆包解包，异常检测等机制让你从nio的繁重细节中脱离出来，让你只需要关心业务逻辑
4.netty解决了jdk的很多包括空轮训在内的bug
5.netty底层对线程，selector做了很多细小的优化，精心设计的reactor线程做到非常高效的并发处理
6.自带各种协议栈让你处理任何一种通用协议都几乎不用亲自动手
7.netty社区活跃，遇到问题随时邮件列表或者issue
8.netty已经历各大rpc框架，消息中间件，分布式通信中间件线上的广泛验证，健壮性无比强大
## netty的服务器启动流程
b.bind(8888).sync();
initAndRegister()
doBind0()
初始化这个服务器对应的channel，通过端口号绑定到选择器上去
### 初始化注册channel(initAndRegister())
new一个channel，初始化这个channel，注册这个channel到某个对象上去
#### 创建一个channel
我们通过反射的方式，创建channel，我们在channel这个方法中传入的是NioServerSocketChannel，我们通过channelFactory创建。
创建这个NioServerSocketChannel，就要走它的构造方法，将这个channel设置为非阻塞模式，SelectionKey.OP_ACCEPT
感兴趣的事件是接收连接，也就是nio服务器感兴趣的事件。这个时候这个channel还会带有其他的组件，id是唯一的标识，unsafe，最后是pipeline
#### 初始化channel
最开始添加配置，自己的和接入的连接的，最重要的是在serverChannel的流水线处理器加入了一个接入器，这个作用就是一直接收新的请求
#### 将这个channel register到某个对象
invokeHandlerAddedIfNeeded()执行的就是handlerAdded，然后就是
pipeline.fireChannelRegistered();控制台显示为channelRegistered
### doBind0()
doBind0()异步的执行channelActive
调用到jdk底层做端口绑定，并触发active事件，active触发的时候，真正做服务端口绑定
## netty新连接接入
1，检测到有新的连接
2，将新的连接注册到worker线程组
3，注册新连接的读事件
### 检测到有新的连接
服务端的channel会不断的检测新的连接
boss reactor线程轮询到accept事件，表示有新的连接接入了，调用channel(服务端的channel)的unsafe来操作。
unsafe就是channel的底层操作实现者
NioMessageUnsafe的read方法
### 注册到reactor线程
不断的读取消息doReadMessages ，新接入的连接都会执行pipeline.fireChannelRead()，经历服务端的洗礼pipeline.fireChannelReadComplete()
#### doReadMessages
javaChannel().accept(),有感兴趣的事件，然后获取到客户端的channel(返回jdk底层nio创建的一条channel),所有接收的channel组成一个list,其中的每一个channel的创建都是绑定read事件
#### pipeline.fireChannelRead(NioSocketChannel)
所有的客户端连接，现在已经是channel，我们进入到服务端的接收器中，执行channelRead()
child.pipeline().addLast(childHandler);
childGroup.register(child);
----->next().register(channel);//worker reactor线程组中选择一个reactor线程
----->
doRegister();
当前的channel绑定到当前线程的选择器，后续的channel的轮询事件处理，都是这个线程处理
### 注册读事件
## reactor线程
### NioEventLoop创建
EventLoopGroup mainGroup = new NioEventLoopGroup();
其中传入的是一个空的executor，所以我们NioEventLoopGroup会创建一个ThreadPerTaskExecutor，然后我们会通过这个ThreadPerTaskExecutor来执行newChild，每一个newChild就是new NioEventLoop，每一个NioEventLoop在有任务的时候就会启动。
### NioEventLoop的run方法
循环执行
select----》process selected key ----》run task
首先我们获取到该线程的所有channel事件，产生处理IO事件的channel，处理任务队列
#### select
每次一循环开始，wakeup设置为false

