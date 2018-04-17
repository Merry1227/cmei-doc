#Spring boot 2.0改动
Spring 5: https://content.pivotal.io/blog/reacting-to-spring-framework-5-0
https://www.ibm.com/developerworks/library/j-whats-new-in-spring-framework-5-theedom/

## actuator 
* 所有的endpoint的根目录变了，变成了application/xxx
* 可以通过management.context-path =/ 指定路径

##各种class not found 问题
* 基本上解决方案，是有些依赖需要自己手动加上了， 至于原因，目前我还没搞清楚
```
    <dependency>
        <groupId>org.springframework.kafka</groupId>
        <artifactId>spring-kafka</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.retry</groupId>
        <artifactId>spring-retry</artifactId>
    </dependency>
```
