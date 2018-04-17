#配置中心-携程apollo


#client端设置
*client依赖包
<dependency>
    <groupId>com.ctrip.framework.apollo</groupId>
    <artifactId>apollo-client</artifactId>
    <version>0.7.0-SNAPSHOT</version>
</dependency>

* client application加上@EnableApolloConfig
            
* 确保存在应用中classpath:/META-INF/app.properties文件
app.id=YOUR-APP-ID

* 文件/opt/settings/server.properties
    本地环境：    env=dev
    测试环境：    env=fat
    stable环境： env=uat
    prod环境：   env=pro
    
* 从服务器缓存的数据存在了/opt/data


测试：
通过http://localhost:8021/env 查看发布的NS属性是否加载
"ApolloPropertySources:application": {
        "lala1": "dh'3",
        "greq": "ngwr",
        "key2": "badgqre",
        "env-cluster": "hahatest"
    },