#ES基础
##Index:*
本质上是拥有同类特征文档集合。基本上对应了领域模型。
##Type:
本质上是对Index进行类别或者partition的划分。对于日志可以以日期划分type

#ES构架

#并发控制（更新操作）
###通过版本号

#搜索关键：把握搜索者意图  HOW
设计上，通过Strategy策略，来做个意图猜测组件，然后对不同的，匹配不同的搜索策略。。。。


##查询和过滤
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/_queries_and_filters.html
* 过滤不评分，filter被缓存
* 查询参与评分
  （个性化搜索）
  
#索引、类型、文档
？？类型映射

#重建索引
即使你认为现在的索引设计已经很完美了，在生产环境中，还是有可能需要做一些修改的。  
做好准备：在你的应用中使用别名而不是索引名。然后你就可以在任何时候重建索引。别名的开销很小，应该广泛使用。  

##相关性_score、查询的打分、排序
###排序与多字段
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/multi-fields.html

###相关性
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/relevance-intro.html
Elasticsearch 的相似度算法 被定义为检索词频率/反向文档频率， TF/IDF ，包括以下内容：

检索词频率
检索词在该字段出现的频率？出现频率越高，相关性也越高。 字段中出现过 5 次要比只出现过 1 次的相关性高。
反向文档频率
每个检索词在索引中出现的频率？频率越高，相关性越低。检索词出现在多数文档中会比出现在少数文档中的权重更低。
**字段长度准则**
字段的长度是多少？长度越长，相关性越低。 检索词出现在一个短的 title 要比同样的词出现在一个长的 content 字段权重更大。

相关度与分片
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/relevance-is-broken.html

#分析器Analyzer
自定义分析器
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/custom-analyzers.html


#分片的内部原理 
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/inside-a-shard.html  


问题：
#散弹式搜索、 长尾
#评分的影响因素 HOW 
* 大部分搜索其实关注的是最近发生的文档，最近文档应该获取较高分

#如何衡量搜索引擎：查全、查准
https://elasticsearch.cn/book/elasticsearch_definitive_guide_2.x/most-fields.html
全文搜索被称作是 召回率（Recall） 与 精确率（Precision） 的战场：
 召回率 ——返回结果中的所有文档都是相关的； 精确率 ——返回结果中没有不相关的文档。
 目的是在结果的第一页中为用户呈现最为相关的文档。
 
 提高全文相关性精度的常用方式是为同一文本建立多种方式的索引， 每种方式都提供了一个不同的相关度信号 signal
 
#es more like this 内容推荐

###建索引别名
 
##现有搜索数据收集

##搜简历-> 搜人

#使用手册高级用户笔记
##查询--匹配
1. 查询方式：字段中心还是词中心 field-centric or term-centric
    * field-centric: 从单个field视觉看查询，查询串中的每个字段都最好在单个field字段中出现。
    * term-centric: 从搜索串的角度看，最好多个field(如cross-fields场景)，**分别**匹配了搜索串的每个词。

2. multifields 多字段映射
    * 对同一个字段索引两次， 一次词干模型，一次非词干，查询时候用multi_match匹配多个fields， 并且可以设置每个field对应的权重
    https://www.elastic.co/guide/cn/elasticsearch/guide/current/most-fields.html  

3. cross-fields entity search 跨字段实体搜索
    * 适合实体类搜索，如人(first_name, last_name)、产品、地址等，多个字段用于标识唯一。 multi-match,most_fields
      https://www.elastic.co/guide/cn/elasticsearch/guide/current/_cross_fields_queries.html
      search across multiple fields as if they were one big field


4. Multi-value field多值行为
    * 默认"position_increment_gap": 100
    https://www.elastic.co/guide/en/elasticsearch/reference/5.5/array.html


5. _source, _all
    * _source, 只是以json形式保存所有字段，不被index，不能被搜索
    * _all： 只有被index的字段，可以被搜索
6.  reindex TODO
    https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-reindex.html

##排序--相关性
    默认情况下，按照相关性排序。 _score
    