#分布式服务追踪Sleuth, Zipkin
##基本概念
Distributed tracing for Spring Cloud applications, compatiblewith Zipkin, **HTrace** and log-based(e.g. ELK)tracing
http://cloud.spring.io/spring-cloud-static/Dalston.SR4/single/spring-cloud.html#_spring_cloud_sleuth
http://m.blog.csdn.net/forezp/article/details/76795269
http://blog.csdn.net/forezp/article/details/70162074

##安装使用：
###mysql存储, HTTP同步发送
####服务端：
1. pom 添加依赖
```
<!-- Zipkin server 相关依赖 -->
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-server</artifactId>
        </dependency>
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-autoconfigure-ui</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.zipkin.java</groupId>
            <artifactId>zipkin-autoconfigure-storage-mysql</artifactId>
        </dependency>
          
```         
2. application.properties 添加配置  

 ```
   # Database data source
   zipkin.storage.type=mysql
   spring.datasource.url=jdbc:mysql://localhost:3306/zipkin?useUnicode=true&characterEncoding=utf-8&useSSL=false&autoReconnect=true
   spring.datasource.username=root
   spring.datasource.password=123456
   spring.datasource.driver-class-name=com.mysql.jdbc.Driver
   
   # 服务发现服务注册中心配置
   server.port=9411
   spring.application.name=zipkin-server
   eureka.client.serviceUrl.defaultZone=http://ip1:9001/eureka/,http://ip2:9001/eureka/   
   
```     

####客户端：
1. pom 添加依赖
```
<!-- zipkin 追踪服务 -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-zipkin</artifactId>
    </dependency>
```   
2. 添加配置

spring.zipkin.service.name=hello1 //可以修改当前监控的服务在zipkin端的显示名
spring.zipkin.base-url=http://localhost:9411 //zipkin-server的ip和端口
spring.sleuth.sampler.percentage=0.8 //抽样比例，1是全部抽样。 默认0.1
spring.zipkin.locator.discovery.enabled=true //是否显示ip

如果正常的话：你的log将会如下
**注意通用的调用并不是每次都会收集到zipkin server,由抽样比例决定。 
在客户端[bootstrap,f58419e2ceb834b9,f58419e2ceb834b9,true] 为[appname,traceId,spanId,exportable]
 最后一个boolean表示是否收集到zipkin。第二位为traceid
```
2017-10-12 13:55:15.251 DEBUG [bootstrap,f58419e2ceb834b9,f58419e2ceb834b9,true] 45689 --- [nio-8021-exec-4] o.s.web.servlet.DispatcherServlet        : Null ModelAndView returned to DispatcherServlet with name 'dispatcherServlet': assuming HandlerAdapter completed request handling
2017-10-12 13:55:15.251 DEBUG [bootstrap,f58419e2ceb834b9,f58419e2ceb834b9,true] 45689 --- [nio-8021-exec-4] o.s.web.servlet.DispatcherServlet        : Successfully completed request
2017-10-12 13:55:15.489 DEBUG [bootstrap,06e01f462feb666e,06e01f462feb666e,false] 45689 --- [nio-8021-exec-5] o.s.web.servlet.DispatcherServlet        : DispatcherServlet with name 'dispatcherServlet' processing GET request for [/health]
```
发现问题：
* log生成太多，很快压跨了mysql server
* 每个request性能影响


###Kafka存储和异步发送

###ELK


###HTrace


##Zipkin
分布式tracing系统论文鼻祖：https://research.google.com/pubs/pub36356.html  
源码： https://github.com/openzipkin/zipkin/wiki  
官网文档： http://zipkin.io/  
背景和设计：
http://cloud.spring.io/spring-cloud-static/Dalston.SR4/single/spring-cloud.html#_spring_cloud_sleuth  




##目标
code tracing: 需要抽取有用信息
debuging exception:
performance and profiling:


做关键点的trace;

