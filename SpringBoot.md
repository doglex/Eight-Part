## 介绍
+ 看似简单，实则复杂，因为有很多隐式功能
+ 技术体系和组件众多
+ 微服务架构(Spring cloud)
+ 响应式编程
+ 云原生
+ Web应用
+ 事件驱动
+ Serverless架构
+ 批处理(Spring Batch)
+ 数据访问：JDBC、ORM、Transactions
+ Web服务：WebMVC、WebSocket、WebFlux
+ 核心容器：Beans(IoC)、AOP、Context、Instrument
+ 自动配置、度量监控、一键部署
+ 集成了Tomcat、Jetty、Netty、Servlet可供选择
> 约定优于配置
> 内置监控(Actuator):监控内存、JVM、GC等信息

## 框架
Spring + Web
![](assets/sprintbootframework.png)
Spring Boot异步 & Spring MVC 同步) 
![](assets/spring2.png)
> 异步和同步是可以混合使用的

## Spring MVC 到 Spring Boot
+ 繁重的xml配置可选的使用 注解、yml
+ started可以简化配置
+ 一键启动，不用额外部署war到Tomcat上
+ 自动监控Actuator，可以在REST访问性能数据

## 代码架构
![](assets/spring3.png)