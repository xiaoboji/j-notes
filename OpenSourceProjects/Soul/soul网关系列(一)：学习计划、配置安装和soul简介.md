> 一个异步的,高性能的,跨语言的,响应式的API优秀网关，参考了Kong，Spring-Cloud-Gateway等优秀的网关。


# 一、soul网关学习计划
- 学习动机
有幸参加了一个源码学习小组，连续28天，每天记录一点soul网关学习的心得体会，一起监督学习。
- 学习计划

```
week1-实例：
- 安装部署soul网关、使用soul网关的examples，Http、dubbo、spring cloud等的简单使用
- 做一些压力测试对比。
- 了解soul网关的基本原理、表结构、功能概述。
- 实际项目中使用soul网关。
- 翻译/吸收一些soul网关的本周相关优质文档。
week2-核心：
- 深入阅读各个核心模块的源码，熟悉方法含义，调用流程等。
- debug核心模块代码，找出代码中的可以借鉴的地方,如责任链模式。
- 翻译/吸收一些soul网关的本周优质文档。
week3-插件：
- 深入阅读几个插件的学习，熟悉三方库的功能/整合/配置/代码。
- debug插件模块代码，找出代码中的可以借鉴的地方。
- 翻译/吸收一些soul网关的本周优质文档。
week4-总结：
- 查缺补漏，总结soul网关的学习内容，分类整理。
- 结合自己公司场景下的soul使用的业务差异和分析。
- 复习相关NIO和Netty课程知识。
- 手撸一个略微丰富的网关项目。
```
- 学习要求
坚持死磕、多看多问、乐于分享、每天输出总结
# 二、soul网关基本原理、功能介绍和关键表结构
## 2.1 soul网关基本原理
### 2.1.1 微服务中为什么需要网关？
- 微服务的流行，服务之间的调用，需要统一的请求标准。
- 微服务接口，需要监控，限流，熔断，等等。
- 微服务接口需要统一的鉴权。
- 接口问题定位 A/B test等等。
### 2.1.2 soul网关概述
一个异步的,高性能的,跨语言的,响应式的API网关。参考了Kong，Spring-Cloud-Gateway等优秀的网关！
1. 自带插件: 防火墙，签名，监控，限流，代理，dubbo，springcloud
2. 所有的插件，选择器，规则，热插拔，动态配置。
3. 无缝对接http，restful，websocket，dubbo，springcloud 等协
4. 支持集群部署，支持灰度发布。
5. 当然你熟了以后，还有很多其他的功能，比如查找定位问题，A/B test 等等
### 2.1.3 soul对手概述
#### 2.1.3.1 soul pk zuul
zuul一个中间件产品，完全可以由业务系统自己去引入，性能不高，没有动态化的配置，不利于管理。
#### 2.1.3.2 soul pk kong
kong 是非常优秀的框架，某些功能要收费，基于lua语言，开发维护成本高
#### 2.1.3.3 soul pk sc-gateway
- 性能上来说，soul和sc-gateway 都采用webflux，性能相当
- sc-gateway 基于配置的，接口很多很难集成，而且配置规则不是动态化的。
- soul是插件化的思想，插件热插拔，配置灵活并且是动态化的。
- soul还可以用来排除问题，A/B test。
- soul提供了监控插件来统计qps等等信息。
- soul提供了waf，sign插件，来阻止外来攻击
## 2.2 soul功能介绍
### 2.2.1 soul架构设计
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210114212047325.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
架构图解读：
1. 绿色的客户端就是各个客户，各种语言的客户端，按照网关要求的数据格式请求网关服务
2. soul也是个http服务，底层用了webflux(一种响应式编程范式)，这个是soul网关设计的核心设计之一。
3. soul-admin 就是整个soul的整个管理后台，它配置所有的规则选择器，然后再把数据写到zookeeper。
4. soul服务在启动的时候，会拉取zookeeper的数据，写到本地JVM内存，然后继续监听 zookeeper，来动态更新JVM内存中的数据
5. 后面就是soul的执行流程了，基本上就是插件责任链模式，具体的插件执行具体的事情. 每个插件它是根据用户请求网关的数据，与admin后台配置的规则，来执行具体的逻辑，这个是soul网关设计的核心设计之一。

### 2.2.2 soul模块概述
- soul-admin : Plugins and other information configuration management background(负责插件和其它配置的后端管理)
- soul-bootstrap : With the startup project, users can refer to(负责项目启动)
- soul-client : User fast access with Spring MVC, Dubbo, Spring Cloud.(用户可以快速接入spring mvc ，dubbo，spring cloud)
- soul-common : Framework common class(框架公用类)
- soul-dist : build project(负责构建工程)
- soul-metrics : metrics impl by prometheus.(使用prometheus实现的Metrics)
- soul-plugin : soul provider plugin collection.(负责soul的插件集合)
- soul-spi : soul spi define.(负责soul的spi定义)
- soul-spring-boot-starter : Support for the spring boot starter(负责soul的spring-boot 快速启动)
- soul-sync-data-center : provider zookeeper, http, websocket, nacos to sync data(负责zookeeper,http,websocket,nocos数据同步)
- soul-test/examples : the rpc test project(负责soul的测试)
- soul-web : Core processing packages including plugins, request routing and forwarding, and so on(soul的web管理，包含插件，请求路由和转发)
## 2.3 soul关键表结构说明
- 插件采用数据库设计，来存储插件，选择器，规则配置数据，以及对应关系
- 一个插件对应多个选择器，一个选择器对应多个规则。
- 一个选择器对应多个匹配条件，一个规则对应多个匹配条件。
- 每个规则在对应插件下，不同的处理表现为handle字段，这个一个不同处理的json字符串。具体的可以在admin使用过程中进行查看。

数据库表UML类图：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115002912858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
具体几张表的含义
1. meta_data：对dubbo泛化调用使用，每条记录对应一个dubbo接口的方法，http协议不会保存，而springcloud协议，只会存储一条数据， path为 ：/contextPath/**
2. plugin：存储当前支持插件，我们对应配置的插件相关参数，就会更新这样表
3. rule：插件管理中，我们配置的具体规则。实际在这里我们也可以看出Soul的三大核心：plugin，rule，selector
4. rule_condition：rule表中配置的，对应的具体匹配规则
5. selector：规则表
6. selector_condition：规则条件表
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115003001556.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115003011965.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
# 三、soul安装和测试
## 3.1 soul安装运行
1. 安装mysql，windows下直接解压，修改data存储路径，cmd管理启动
2. 安装数据库client,这里使用的是DBeaver，可以用其他mysql client替代
3. 安装postman/jmeter，作http请求使用
4. 创建soul项目，使用master分支
5.  mvn install 项目
6. 启动soul-admin项目，自动创建数据库，soul-admin是一个单纯的后端控制服务
7. 启动soul-bootstrap，整个项目的核心，直接run起来就可以，注意端口号为http://localhost:9095/
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210114234144966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
## 3.2 soul网关转发测试
1. 启动模拟业务项目，使用soul-test-http中的相关实例
2. 相关配置，在业务中使用soul，配置相应的path前缀，实现转发
![在这里插入图片描述](https://img-blog.csdnimg.cn/202101150014408.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
3. 在OrderController中使用@SoulSpringMvcClient注解，相应的接口已经注册至soul
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115001508692.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115001514879.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
4. 使用postman测试转发功能
正常访问接口
![在这里插入图片描述](https://img-blog.csdnimg.cn/202101150015256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
使用soul网关访问接口
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210115001535534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JvX2Jvb2s=,size_16,color_FFFFFF,t_70)
## 3.3 几个问题小插曲
1. ```Error:(20, 14) java: 程序包lombok不存在```

在项目内，命令行运行 mvn idea:idea后解决

2. ```Caused by: com.mysql.cj.exceptions.InvalidConnectionAttributeException: The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone. You must configure either the server or JDBC driver (via the serverTimezone configuration property) to use a more specifc time zone value if you want to utilize time zone support.```

 时区问题，在jdbc连接后面添加&serverTimezone=UTC 后解决
# 四、参考链接
- [soul官方文档 https://dromara.org/zh-cn/docs/soul/soul.html](https://dromara.org/zh-cn/docs/soul/soul.html)
- [server time zone问题参考链接](https://blog.csdn.net/qq_33811662/article/details/80532699)
