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

## Hello World
+ 可以通过引入依赖使用spring-boot-starter-web
```
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-web</artifactId>
<version>3.0.4</version>
```
+ 可以用spring initialiezr网站 start.spring.io 
+ 可以安装 spring boot helper插件，新建项目,建好后在project文件夹上右键“Add Framework Support”选择Maven
> 若出现spring版本不对，则换一个低的parent的版本
+ 启动类需要在高层次、controller在低层次
``` java
// src/main/java/com/example/demo/DemoApplication.java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}

// src/main/java/com/example/demo/ctrl/Hello.java
package com.example.demo.ctrl;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class Hello {

    @RequestMapping(value = "/")
    @ResponseBody
    public String hello() {
        return "hello world";
    }
}
```
+ 可以在点击application的箭头运行，也可以打成jar包运行

## 术语
+ 端点： Endpoint，即一个url路径
+ label：版本控制信息
+ profile：配置文件对应的环境dev、prod等
```
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```
+ 可以用yml也可以用properties文件配置
+ 在application.properties设置spring.profiles.active=test则会启用application-test.properties，也可以在运行时指定该参数
+ 可以用注解确定环境 @Profile("prod")
+ @Value注解可以注入配置项内容
```
@Value("${springcss.order.point}")
private int point;
```
+ @ConfigurationProperties 可以用前缀注入多个值,或者注入到HashMap中
```
@ConfigurationProperties(prefix="springcss.order")
```
+ @PropertySource 可以指定从哪个配置文件中获得配置信息
+ @PropertySources 可以指定从哪些配置文件中获得配置信息
+ spring.config.location 可以指定配置文件的默认加载位置
+ 多个地方存在application.properties时，则高优先级的会覆盖低优先级的值

## AutoConfiguration 自动配置
+ @SpringBootApplication 包含下面三个注解
+ @ComponentScan 扫描基于@Component类所在包下所有的需要注入的类，并把相关Bean定义批量加载到容器中
+ @SpringBootConfiguration 是空注解，使用了@Configuration注解，提供就JavaConfig配置类的实现
+ @EnableAutoConfiguration 包含以下2个注解
+ @AutoConfigurationPackage 对该注解下的包进行自动配置
+ @Import(AutoConfigurationPackages.Registrar.class) 用来动态创建Bean