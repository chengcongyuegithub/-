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
### 初始化注册channel
new一个channel，初始化这个channel，注册这个channel到某个对象上去
#### 创建一个channel
我们通过反射的方式，创建channel，我们在channel这个方法中传入的是NioServerSocketChannel，我们通过channelFactory创建。
创建这个NioServerSocketChannel，就要走它的构造方法，将这个channel设置为非阻塞模式，SelectionKey.OP_ACCEPT
感兴趣的事件是接收连接，也就是nio服务器感兴趣的事件。这个时候这个channel还会带有其他的组件，id是唯一的标识，unsafe，最后是pipeline
#### 初始化channel
最开始添加配置，自己的和接入的连接的，最重要的是在serverChannel的流水线处理器加入了一个接入器，这个作用就是一直接收新的请求
#### 将这个channel register到某个对象
