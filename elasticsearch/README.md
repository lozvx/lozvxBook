# ElasticSearch

ES权威指南：

{% embed url="https://elasticsearch.cn/book/elasticsearch\_definitive\_guide\_2.x/index.html" %}



 Elasticsearch 是一个开源的搜索引擎，建立在一个全文搜索引擎库 Apache Lucene™ 基础之上。 Lucene 可能是目前存在的，不论开源还是私有的，拥有最先进，高性能和全功能搜索引擎功能的库。  
  
但是 Lucene 仅仅只是一个库。为了利用它，你需要编写 java 程序，并在你的 java 程序里面直接集成 Lucene 包。 更坏的情况是，你需要对信息检索有一定程度的理解才能明白 Lucene 是怎么工作的。Lucene 是 很 复杂的。  
  
Elasticsearch 也是使用 Java 编写的，它的内部使用 Lucene 做索引与搜索，但是它的目标是使全文检索变得简单， 通过隐藏 Lucene 的复杂性，取而代之的提供一套简单一致的 RESTful API。  
  
一个分布式的实时文档存储，每个字段 可以被索引与搜索  
一个分布式实时分析搜索引擎  
能胜任上百个服务节点的扩展，并支持 PB 级别的结构化或者非结构化数据  
  
  
Elasticsearch 是 面向文档 的，意味着它存储整个对象或 文档\_。Elasticsearch 不仅存储文档，而且 \_索引 每个文档的内容使之可以被检索。  
在 Elasticsearch 中，你 对文档进行索引、检索、排序和过滤--而不是对行列数据。这是一种完全不同的思考数据的方式，也是 Elasticsearch 能支持复杂全文检索的原因。  
ES使用JSON作为文档的序列化格式。  
  
  
索引（名词）：  
  
如前所述，一个 索引 类似于传统关系数据库中的一个 数据库 ，是一个存储关系型文档的地方。 索引 \(index\) 的复数词为 indices 或 indexes 。  
  
索引（动词）：  
  
索引一个文档 就是存储一个文档到一个 索引 （名词）中以便它可以被检索和查询到。这非常类似于 SQL 语句中的 INSERT 关键词，除了文档已存在时新文档会替换就文档情况之外。  
  
倒排索引：  
  
关系型数据库通过增加一个 索引 比如一个 B树（B-tree）索引 到指定的列上，以便提升数据检索速度。Elasticsearch 和 Lucene 使用了一个叫做 倒排索引 的结构来达到相同的目的。  
  
  
  
分布式：  
  
分配文档到不同的容器 或 分片 中，文档可以储存在一个或多个节点中  
按集群节点来均衡分配这些分片，从而对索引和搜索过程进行负载均衡  
复制每个分片以支持数据冗余，从而防止硬件故障导致的数据丢失  
将集群中任一节点的请求路由到存有相关数据的节点  
集群扩容时无缝整合新节点，重新分配分片以便从离群节点恢复  
  
  
1.下载  
https://www.elastic.co/downloads/elasticsearch  
  
2.运行  
./bin/elasticsearch  
  
3.访问  
curl 'http://localhost:9200/?pretty'  
  
4下载kibana  
https://www.elastic.co/cn/downloads/kibana  
  
  
文档   
JSON  
  
文档元数据  
\_index  
文档在哪存放。一个 索引 应该是因共同的特性被分组到一起的文档集合。一个索引仅仅是逻辑上的命名空间， 这个命名空间由一个或者多个分片组合在一起  
\_type  
文档表示的对象类别。所有的产品都放在一个索引中，但是你有许多不同的产品类别，比如 "electronics" 、 "kitchen" 和 "lawn-care"。  
\_id  
文档唯一标识  
ID 是一个字符串， 当它和 \_index 以及 \_type 组合就可以唯一确定 Elasticsearch 中的一个文档。 当你创建一个新的文档，要么提供自己的 \_id ，要么让 Elasticsearch 帮你生成。



\*\*\*\*
