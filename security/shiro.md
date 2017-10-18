# shiro 安全框架

## shiro介绍
提供了哪些基本功能：
* 身份认证
* 权限认证
* session管理
* web集成
* 缓存

见Apache Shiro Features
https://shiro.apache.org/introduction.html#apache-shiro-features

### 构架
https://shiro.apache.org/architecture.html

### 名词解释
花一定的时间，理解shiro的Terminology是深入了解shiro的关键所在。
https://shiro.apache.org/terminology.html

## shiro使用
### 与spring boot的集成
### 与spring cloud zuul的集成
### 与redis的集成

## shiro源码解读
### shiro-cache： 可以参考其实现，作为对任意的业务层Cache的管理和封装
* CacheManager: 实现了对拥有的cache类型进行统一管理，和资源的释放。  
    TODO： 可以进一步改进的地方， 完成的全套，依据业务查看当前的cache情况
* Cache：是一层对通用Cache的抽象
    TODO: 实现一个MemoryCache，包括容量控制和过期时间
   
### shiro-core
#### Annotation and AOP
#### authc
* authc.pam: plug-in Authenticator Realm, 值得看的是多个real验证时，三种可能的组合策略：都成功、至少一个成功、第一个成功
* ahthz.permission: 提供了AllPermission, DomainPermission, WildcardPermission三种。
    值得一提的是DomainPermission, 可用于在JPA层提供类似Hibernate的Annotation的权限验证，来用于对query进行验证Domain, action, 目标列。
    比如允许用户对某个表(domain)在指定列上执行更新action。
* env:     
* mgt:     

### shiro-crypto
#### Hash算法：


### shiro-event
### shiro-lang
### shiro-support
### shiro-web
#### shiro-web
#### spring-web
#### spring-web与spring web的集成和区别

### shiro-tools


## 权限系统
### 通用基于角色的权限系统的模型设计
### 细粒度的权限系统的设计————基于实例的权限系统的设计


# shiro集成问题汇总    
1. sprint transaction 未起作用，后调查到时目标类的代理未生成，注释掉, @transactional annotation生效。
   该解决方案的前提是：应用在不需要用shiro的annotation。cha
   否则可以使用aspectj解决。或者不使用annotation直接调用TransactionProxyFactoryBean创建
 ```//    @Bean
    //    @DependsOn("lifecycleBeanPostProcessor")
    //    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
    //        DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator = new DefaultAdvisorAutoProxyCreator();
    //        defaultAdvisorAutoProxyCreator.setProxyTargetClass(true);
    //        defaultAdvisorAutoProxyCreator.setExposeProxy(true);
    //        return defaultAdvisorAutoProxyCreator;
    //    }
    ```

  原因：DefaultAdvisorAutoProxyCreator 用于shiro 的annotation
  
2. IncorrectCredentialsException: 
   Solution: 清楚credential相关cache，并且重新调用currentUser.login()，可能由于credential改到引起

3. UnknownSessionException
   可能服务器端session过期，先调用currentUser.logout();再重新登录currentUser.login()
   
4. AuthenticationException: 其他的AuthenticationException, 如果是数据不一致引起，重新登录
   
   
   
   


