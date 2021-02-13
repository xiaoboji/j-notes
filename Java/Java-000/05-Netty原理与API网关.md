Netty原理及API网关
---
### 一、什么是高性能
#### 高性能的三个特点
- 高并发用户(Concurrent Users):业务角度衡量的，要能维持住这些链接，服务可用
- 高吞吐量(Throughout):QPS(接口)/TPS(一个完整的业务)技术指标，处理能力
- 低延迟(Latency):技术指标，处理速度

#### 响应时间和延迟    
- 响应时间和延迟：响应时间是针对用户的，延迟是针对系统的。
- P99，就是99%的应用在多长时间内返回、

#### 高性能副作用
- 系统复杂度X10以上
- 建设和维护成本++++
- 故障或者bug导致的破坏性X10以上

#### 应对策略(稳定性建设，混沌工程)
- 容量(举例：淘宝每天的订单量在3000-4000W左右，双十一高峰大约45W/S)
- 爆炸半径(有效的措施把影响的控制范围给缩小)
- 工程方面积累与改进

### 二、Netty如何实现高性能
#### 回顾简介
- 异步、事件驱动、基于NIO
- 服务端、客户端、TCP/UDP

#### 一步步到Netty运行原理

1. 事件处理机制
- 新起一个eventqueue，然后分发
2. Reactor模型
- 基于事件处理驱动，有一个或者对个并发输入，有一个service handler(专人专用)和多个EventHandlers(业务处理)
- 这个Service Handler会同步的将请求多路复用的分发给相应的event handler
3. Netty模型
- 更加细化引入多级的概念
- boss-group(一个单线程的不断轮询的链接，接收请求)
- worker-group
    * Task queue
    * Delay Task queue
    * Channel
    * pipeline
        + channel handler1
        + channel handler2
- Executor group(执行处理组)

#### Netty重点对象

- Bootstrap 启动线程，开启socket
- EventLoopGroup
- EventLoop
- SocketChannel lianjie 
- ChannelInitializer：初始化
- ChannelPipleine：处理器链
    * ChannelInBoundHandler进去的handler
    * 应用程序
    * ChanelOutBoundHandler出去的handler
- ChannelHandler：处理器
- Event & Handler
    * 入站事件
        + 通过激活和停用
        + 读操作事件
        + 异常事件
        + 用户事件
    * 出站事件
        + 打开链接
        + 关闭链接
        + 写入数据
        + 刷新数据

### 三、Netty网络程序优化
1.粘包和拆包

- 概念
    * 包是一个一个发出去的
    * 服务端/客户端都有个缓存区的东西，
    * 粘包和拆包就是前后两个包，到底是一个包还是两个包，这个是业务上的概念。

- 解决办法
    * 规范好什么是一段数据。
    * 两个经典的例子，反例：一个是一次考试给你传答案，有单选有多选，ABCACDB这一串你就不知道具体的是哪一到题了。正例：PPT的页码 5/20

- Netty(ByteToMessageDecoder)的解决办法
    * FixedLengthFrameDecoder：定长协议解码器，我们可以指定固定的字节数算一个完整的报文。
    * LineBasedFrameDecoder：行分隔符解码器，遇到\n或者\r\n,则认为是一个完成的报文。
    * DelimiterBasedFrameDecoder：分隔符解码器，分隔符可以自己指定。
    * LengthFieldBasedFrameDecoder：长度编码解码器，将报文划分为报文头/报文体。
    * JsonObjectDecoder：json格式解码器，当检测到匹配数量的"{","}","[","]",则认为是一个完整的json对象或者json数组。
    
- HTTP的解决办法
    * Chunk，定长的比较大的一个报文头、报文体
- Json
    * json必须以"{","["开头

2. Http的断点续传,也是类似制定规则

3. nagle与TCP_NODELAY
- MTU：Maxitum Transmissin Unit最大传输单元
- MSS：Maxitum Segment Size最大分段大小

nagel是一种优化策略
- 缓存区满发送
- 达到超时发送

4. 连接优化
- TCP三次握手
    * C：你在不在？--S：我在,你在不在？--C:我在
    * SYN 你在不在？
    * ACK 我在

- TCP四次握手
    * C:分手吧--S:好的,分手吧--S:你确定要和我分手吗？--C:好的，分手吧 
    * FIN 分手吧
    * ACK 好的，你挂吧、
    * 等待2MSL，默认一分钟，处理完之后，还要在这个状态停留2分钟，可以使用 netstat -anot 命令，可以看到很多close wait状态

5. Netty优化
- 不要阻塞EventLoop
- 系统参数优化
    * ulimit -a /proc/sys/net/ipv4/tcp_fin_timeout(回收的周期). TCPTImedWaitDelay
- 缓冲区优化
    * SO_RCVBUF(接收的缓存区)
    * SO_SUDBUF(发送的缓存区)
    * SO_BACKLOG(保持连接状态)
    * REUSEXXX(重用端口)
- 心跳机制和断线重连
- 内存与ByteBuffer优化
    * DirectBuffer和HeapBuffer
- 其他优化
    * IORatio-IO和非IO的比例
    * WaterMark-水位，缓存区写满的标识
    * TrafficShaping-流控
### 四、典型应用：API网关
1. 作用

客户端请求的统一入口，可以做鉴权、限流、负载等

2. 四大功能

- 请求接入(作为所有API接口服务请求的接入点)
- 应用聚合(类似于门卫，作为所有后端业务服务的聚合点)
- 中间策略(实现安全、验证、路由、过滤、流控等策略)
- 统一管理(对所有API服务和策略进行统一管理)

3. 分类
- 流量网关(关注稳定和安全)--Nginx
    * 全局性流控
    * 日志统计
    * 防止SQL注入
    * 防止Web攻击
    * 屏蔽工具扫描
    * 黑白名单IP
    * 证书加解密处理
- 业务网关(提供更好的服务)--zuul、zuul2
    * 服务级别流控
    * 服务降级和熔断
    * 路由与负载均衡、灰度策略
    * 服务过滤、聚合和发现
    * 权限验证与用户等级策略
    * 业务规则与参数校验
    * 多级缓存策略

4. 几个常见的网关
    * 性能非常好，适合流量网关
        + Nginx
        + Kong
        + OpenResty
    * 扩展性好，适合业务网关，二次开发
        + spring getway(基于webflux--基于netty-rx的)
        + zuul2
        + soul(带控制台，java写的，性能高，基于webflux写的)
  
### 五、手撸一个网关
- gateway 1.0
    * 拿到数据
- gateway 2.0
    * 过滤器，改变http的头，实现几个handler
- gateway 3.0
    * 加一个Router，实现一个负载均衡

### 六、其他
- 
