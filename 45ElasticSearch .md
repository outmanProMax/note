## 数据格式

Elasticsearch 是面向文档型数据库，一条数据在这里就是一个文档。为了方便大家理解，我们将 Elasticsearch 里存储文档数据和关系型数据库 MySQL 存储数据的概念进行一个类比ES 里的 Index 可以看做一个库，而 Types 相当于表，Documents 则相当于表的行。

![image](https://cdn.staticaly.com/gh/xustudyxu/image-hosting@master/20220629/image.6natuc863q80.webp)

这里 Types 的概念已经被逐渐弱化，Elasticsearch 6.X 中，一个 index 下已经只能包含一个type，Elasticsearch 7.X 中, Type 的概念已经被删除了。

# 数据类型

常见的ELasticSearch数据类型

|   数据类型   |               具体类型               |
| :----------: | :----------------------------------: |
|  字符串类型  |         string,text,keyword          |
|   整数类型   |       integer,long,short,byte        |
|   浮点类型   | double,float,half_float,scaled_float |
|   逻辑类型   |               boolean                |
|   日期类型   |                 date                 |
|   范围类型   |                range                 |
|  二进制类型  |                binary                |
|   数组类型   |                array                 |
|   对象类型   |                object                |
|   嵌套类型   |                nested                |
| 地理坐标类型 |              geo_point               |
|   地理地图   |              geo_shape               |
|    IP类型    |                  ip                  |
| 令牌计数类型 |             token_count              |

其中嵌套类型nested会将数据进行扁平化处理（将嵌套的不同类型的数据转换为数组）

# 基本操作

[Elastic stack技术栈学习（十）— springboot集成ES API详解_esapi-CSDN博客](https://blog.csdn.net/qq_44886213/article/details/123425024)

## _cat 

GET /\_cat/nodes：查看所有节点 

GET /\_cat/health：查看 es 健康状况 

GET /\_cat/master：查看主节点 

GET /_cat/indices：查看所有索引

## 文档操作

### 增加文档

在 customer 索引下的 exte

```json
PUT customer/external/1 
{ 
	"name": "John Doe" 
}
```

- PUT 和 POST 都可以做增加操作

POST 如果不指定 id，会自动生成 id。指定 id 就会修改这个数据，并新增版本号 PUT 可以新增可以修改。

PUT 必须指定 id；由于 PUT 需要指定 id，我们一般都用来做修改 操作，不指定 id 会报错。

#### java

```java
        IndexRequest indexRequest = new IndexRequest();
        indexRequest.index("external").id("1");//或IndexRequest indexRequest = new IndexRequest("external","1");

		//设置请求体并转为json
        User user = new User();
        user.setUserName("zhangsan");
        String jsonString = JSON.toJSONString(user);
        indexRequest.source(jsonString, XContentType.JSON);  //要保存的内容

        //执行请求
        IndexResponse indexResponse  = client.index(indexRequest, RequestOptions.DEFAULT));
```



### 查询文档 

```json
GET customer/external/1
```

#### java

```java
        //查询数据
        GetRequest request = new GetRequest();
        request.index("user").id("1");
        GetResponse getResponse = esClient.get(request, RequestOptions.DEFAULT)
```

### 更新文档
```json
POST customer/external/1/_update
{ 

"doc":{ 

		"name": "John Doew"
	}
}
或者
POST customer/external/1
{ 
	"name": "John Doe2"
}
或者
PUT customer/external/1
{ 

	"name": "John Doe"
}
```

- 区别：带_update 对比元数据如果一样就不进行任何操作。
- 对于大并发更新，不带 update； 对于大并发查询偶尔更新，带 update；对比更新，重新计算分配规则。

#### java

```java
        //修改数据
        UpdateRequest updateRequest = new UpdateRequest();
        updateRequest.index("external").id("1");
    	updateRequest.doc(XContentType.JSON,"name","John Doew");

        UpdateResponse updateResponse = esClient.update(updateRequest, RequestOptions.DEFAULT);
```

### 删除文档

```json
DELETE customer/external/1
```

#### java

```java
        //删除数据
        DeleteRequest deleteRequest = new DeleteRequest();
        deleteRequest.index("external").id("1");
        DeleteResponse deleteResponse = esClient.delete(deleteRequest, RequestOptions.DEFAULT);
```

### bulk 批量操作
```json
POST /_bulk
{ "delete": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "create": { "_index": "website", "_type": "blog", "_id": "123" }}
{ "title": "My first blog post" }
{ "index": { "_index": "website", "_type": "blog" }}
{ "title": "My second blog post" }
{ "update": { "_index": "website", "_type": "blog", "_id": "123", "_retry_on_conflict" : 3} }
{ "doc" : {"title" : "My updated blog post"} }
```

#### java

```java
        BulkRequest request = new BulkRequest();
        request.add(new DeleteRequest().index("website").id("123"));
        request.add(new IndexRequest().index("website").id("123").source(XContentType.JSON,"title","My first blog postv"));

        BulkResponse response = esClient.bulk(request, RequestOptions.DEFAULT);
```

## 映射基本操作

建索引库(index)中的映射了，类似于数据库(database)中的表结构(table)。创建数据库表需要**设置字段名称，类型，长度，约束等**；索引库也一样，需要知道这个类型下有哪些字段，每个字段有哪些约束信息，这就叫做映射(mapping)。

### 创建映射

```json
PUT /student/_mapping
{
    "properties": {
        "name":{
            "type": "text",
            "index": true
        },
        "sex":{
            "type": "keyword",
            "index": true
        },
        "age":{
            "type": "keyword",
            "index": false
        }
    }
}
```

#### 映射数据说明

- 字段名：任意填写，下面指定许多属性，例如：title、subtitle、images、price

- type：类型，Elasticsearch 中支持的数据类型非常丰富，说几个关键的：

  - String 类型，又分两种：
    - text：可分词，支持模糊查询，支持准确查询，不支持聚合查询
    - keyword：不可分词，数据会作为完整字段进行匹配，支持模糊查询，支持准确查询，支持聚合查询。
  - Numerical：数值类型，分两类
    - 基本数据类型：long、integer、short、byte、double、float、half_float
    - 浮点数的高精度类型：scaled_float
  - Date：日期类型
  - Array：数组类型
  - Object：对象

- index：是否索引，默认为 true，也就是说你不进行任何配置，所有字段都会被索引。

  - true：字段会被索引，则可以用来进行搜索
  - false：字段不会被索引，不能用来搜索

- store：是否将数据进行独立存储，默认为 false

  原始的文本会存储在 _source 里面，默认情况下其他提取出来的字段都不是独立存储 的，是从 _source 里面提取出来的。当然你也可以独立的存储某个字段，只要设置 "store": true 即可，获取独立存储的字段要比从 _source 中解析快得多，但是也会占用 更多的空间，所以要根据实际业务需求来设置。

- analyzer：分词器，这里的 ik_max_word 即使用 ik 分词器

### 查看映射-GET

```json
GET /student/_mapping
```

## 高级查询操作

### 字段查询query


- "query"：这里的 query 代表一个查询对象，里面可以有不同的查询属性
- "match_all"：查询类型，例如：match_all(代表查询所有)，match，term，range 等等
- {查询条件}：查询条件会根据类型的不同，写法也有差异

#### 组合查询bool_must

`bool` 把各种其它查询通过 `must`（必须，类似 and）、`must_not`（必须不，类似 not）、`should`（应该 类似 or）的方式进行组合

```json
GET /student/_search
{
    "query": {
        "bool": {
            "must": [
                {
                    "match": {
                        "name": "zhangsan"
                    }
                }
            ],
            "must_not": [
                {
                    "match": {
                        "age": "40"
                    }
                }
            ],
            "should": [
                {
                    "match": {
                        "sex": "男"
                    }
                }
            ]
        }
    }
}
```

#### 结果过滤bool_filter

并不是所有的查询都需要产生分数，特别是那些仅用于 “filtering”（过滤）的文档。为了不 计算分数 Elasticsearch 会自动检查场景并且优化查询的执行。 

```
GET /student/_search 
{ 
"query": { 
	"bool": { 
        "must": [ {"match": { "address": "mill"}} ],
        "filter": { 
             "term": { "catalogId": "225" } 
            } 
		} 
	} 
}
```

NOTE：以下皆可以搭配bool_must和bool_filter使用

#### 单字段匹配查询match

```json
GET /student/_search
{
    "query": {
        "match": {
            "name": "zhangsan2"
        }
    }
}
```

#### 匹配查询所有文档match_all

```json
GET /student/_search
{
    "query": {
        "match_all": {}
    }
}
```

#### 多字段匹配匹配查询multi_match

```json
GET /student/_search
{
    "query": {
        "multi_match": {
            "query": "zhangsan",
            "fields": ["name","nickname"]
        }
    }
}
```

#### 单关键字精确查询term

`term` 查询，**精确**的关键词匹配查询，不对查询条件进行分词，即只能单关键字精确查询，一般用与非文本查询

```json
GET /student/_search
{
    "query": {
        "term": {
            "name": {
                "value": "zhangsan"
            }
        }
    }
}
```

#### 多关键字精确查询terms

terms 查询和 term 查询一样，但它允许你指定多值进行匹配。

```json
GET /student/_search
{
    "query": {
        "terms": {
            "name": ["zhangsan","lisi"]
        }
    }
}
```

#### 数组以相互独立的方式进行索引查询nested

```json
GET gulimall_product/_search
{
  "query": {
          "nested": {
            "path": "attrs",
            "query":{
                "match": {
            		"attrs.attrId": {"15"}
                }
            }
        }
    }
}

```

#### 范围查询range

range 查询找出那些落在指定区间内的数字或者时间。range 查询允许以下字符

| 操作符 | 说明        |
| ------ | ----------- |
| gt     | 大于 >      |
| gte    | 大于等于 >= |
| lt     | 小于 <      |
| lte    | 小于等于 <= |

```json
GET /student/_search
{
    "query": {
        "range": {
            "age": {
                "gte": 30,
                "lte": 35
            }
        }
    }
}
```



#### 模糊查询fuzzy

返回包含与搜索字词相似的字词的文档。使用的字段是 fuzzy

编辑距离是将一个术语转换为另一个术语所需的一个字符更改的次数。这些更改可以包括：

- 更改字符（box → fox）
- 删除字符（black → lack）
- 插入字符（sic → sick）
- 转置两个相邻字符（act → cat）

为了找到相似的术语，fuzzy 查询会在指定的编辑距离内创建一组搜索词的所有可能的变体或扩展。然后查询返回每个扩展的完全匹配。

通过 fuzziness 修改编辑距离。一般使用默认值 AUTO，根据术语的长度生成编辑距离。

```json
GET /student/_search
{
    "query": {
        "fuzzy": {
            "name": {
                "value": "zhangsan"
            }
        }
    }
}
```

#### 多IDs查询ids

```json
GET /student/_search
{
    "query": {
        "ids" : {
            "values" : ["1001", "1004", "1006"]
        }
    }
}
```

#### 前缀查询prefix

在 Postman 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

请求体内容：

```json
GET /student/_search
{
    "query": {
        "prefix": {
            "name": {
                "value": "zhangsan"
            }
        }
    }
}
```

### 指定返回字段_source

默认情况下，Elasticsearch 在搜索的结果中，会把文档中保存在 _source 的所有字段都返回。

如果我们只想获取其中的部分字段，我们可以添加 _source 的过滤

```json
GET /student/_search
{
    "_source": ["name","nickname"], 
    "query": {
        "terms": {
            "nickname": ["zhangsan"]
        }
    }
}
```

#### 过滤字段includes、excludes

用到的字段：

- includes：来指定想要显示的字段
- excludes：来指定不想要显示的字段

includes 使用

在 Postman 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

请求体内容：



```json
GET /student/_search
{
    "_source": {
        "includes": ["name","nickname"]
    }, 
    "query": {
        "terms": {
            "nickname": ["zhangsan"]
        }
    }
}
```

### 字段排序sort

sort 可以让我们按照不同的字段进行排序，并且通过 order 指定排序的方式。desc 降序，asc 升序。

```json
GET /student/_search
{
    "query": {
        "match": {
            "name":"zhangsan"
        }
    },
    "sort": [{
        "age": {
            "order":"desc"
        }
    }]
}
```

### 分页查询from、size

from：当前页的起始索引，默认从 0 开始。 from = (pageNum - 1) * size

size：每页显示多少条

```json
GET /student/_search
{
    "query": {
        "match_all": {}
    },
    "sort": [
        {
            "age": {
                "order": "desc"
            }
        }
    ],
    "from": 0,
    "size": 2
}
```

### 聚合查询aggs

聚合允许使用者对 es 文档进行统计分析，类似与关系型数据库中的 group by，当然还有很多其他的聚合，例如取最大值、平均值等等。

聚合查询 `aggs` 字段，该字段里的第一个字段是自定义名字，一个聚合/分组需要另一个聚合/分组需要用到自定义名字（嵌套查询）。第二个字段是聚合查询类型。查询结果不仅有聚合结果，也有设计到的详细数据。

结果长度 `size` 字段和 `aggs` 字段同级，代表只获取聚合结果，不获取涉及到的详细数据。

#### 简单聚合 avg、max、sum

- **对某个字段取最大值 max**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

请求体内容：

```json
{
    "aggs":{
        "max_age":{   // 自定义名字
            "max":{"field":"age"}
        }
    },
    "size":0  // 只获取聚合结果，不获取每一个数据
}
```

- **对某个字段取最小值 min**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

请求体内容：

```json
{
    "aggs":{
        "min_age":{ // 自定义名字
            "min":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

- **对某个字段求和 sum**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

请求体内容：

```json
{
    "aggs":{
        "sum_age":{ // 自定义名字
            "sum":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

- **对某个字段取平均值 avg**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`



```json
{
    "aggs":{
        "avg_age":{ // 自定义名字
            "avg":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

- **对某个字段的值进行去重之后再取总数**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

```json
{
    "aggs":{
        "distinct_age":{ // 自定义名字
            "cardinality":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

- **State 聚合**

stats 聚合，对某个字段一次性返回 count，max，min，avg 和 sum 五个指标

在 **Postman** 中，向 ES 服务器发 `GET` 请求 ：`http://127.0.0.1:9200/student/_search`

```json
{
    "aggs":{
        "stats_age":{ // 自定义名字a
            "stats":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

#### 桶聚合查询terms

桶聚和相当于 sql 中的 group by 语句，显示根据field进行分类的各组的统计大小

- **terms 聚合，分组统计**

在 **Postman** 中，向 ES 服务器发 `GET` 请求：`http://127.0.0.1:9200/student/_search`

```json
{
    "aggs":{
        "age_groupby":{ // 自定义名字
            "terms":{"field":"age"}
        }
    },
    "size":0 // 只获取聚合结果，不获取每一个数据
}
```

## 分词 

一个 tokenizer（分词器）接收一个字符流，将之分割为独立的 tokens（词元，通常是独立 的单词），然后输出 tokens 流。

# Spring整合

1. pom

```xml
        <properties>
            <-- 配置springboot中自带的elasticsearch版本防止冲突-->
            <elasticsearch.version>7.6.2</elasticsearch.version>
        </properties>

        <dependency>
            <groupId>org.elasticsearch.client</groupId>
            <artifactId>elasticsearch-rest-high-level-client</artifactId>
            <version>7.6.2</version>
        </dependency>
```

2. Config

```java
@Configuration
public class GulimallElasticSearchConfig {

    public static final RequestOptions COMMON_OPTIONS;
    static {
        RequestOptions.Builder builder = RequestOptions.DEFAULT.toBuilder();
        // builder.addHeader("Authorization", "Bearer " + TOKEN);
        // builder.setHttpAsyncResponseConsumerFactory(
        //         new HttpAsyncResponseConsumerFactory
        //                 .HeapBufferedResponseConsumerFactory(30 * 1024 * 1024 * 1024));
        COMMON_OPTIONS = builder.build();
    }

    @Bean
    public RestHighLevelClient esRestClient(){
        RestHighLevelClient client = new RestHighLevelClient(
                    RestClient.builder(
                            new HttpHost("192.168.200.120",9200, "http")));

        return client;
    }
}
```

3. 构建索引

```json
PUT gulimall_product
{
  "mappings": {
    "properties": {
      "skuId": {
        "type": "long"
      },
      "spuId": {
        "type": "keyword"
      },
      "skuTitle": {
        "type": "text",
        "analyzer": "ik_smart"
      },
      "skuPrice": {
        "type": "keyword"  #保证精度问题
      },
      "skuImg": {
        "type": "keyword"
      },
      "saleCount": {
        "type": "long"
      },
      "hasStock": {
        "type": "boolean"
      },
      "hotScore": {
        "type": "long"
      },
      "brandId": {
        "type": "long"
      },
      "catalogId": {
        "type": "long"
      },
      "brandName": {
        "type": "keyword"
      },
      "brandImg": {
        "type": "keyword",
        "index": false,
        "doc_value": false
      },
      "catalogName": {
        "type": "keyword"
      },
      "attrs": {
        "type": "nested",
        "properties": {
          "attrId": {
            "type": "long"
          },
          "attrName": {
            "type": "keyword",
            "index": false,
        	"doc_value": false
          },
          "attrValue": {
            "type": "keyword"
          }
        }
      }
    }
  }
}

```

Note1：index为是否可被索引

Note2： doc_values 为 fasle 时，无法基于该字段排序、聚合、在脚本中访问字段值。当 doc_values 为 true 时，ES 会增加一个相应的正排索引，这增加的磁盘占用，也会导致索引数据速度慢一些

4. 发出请求(将es语句翻译成java)

```json
GET gulimall_product/_search
{
  "query": {
    "bool": {
      "must": [ {"match": {  "skuTitle": "华为" }} ], # 检索出华为
      "filter": [ # 过滤
        { "term": { "catalogId": "225" } },
        { "terms": {"brandId": [ "2"] } }, 
        { "term": { "hasStock": "false"} },
        {
          "range": {
            "skuPrice": { # 价格1K~7K
              "gte": 1000,
              "lte": 7000
            }
          }
        },
        {
          "nested": {
            "path": "attrs", # 聚合名字
            "query": {
              "bool": {
                "must": [
                  {
                    "term": { "attrs.attrId": { "value": "6"} }
                  }
                ]
              }
            }
          }
        }
      ]
    }
  },
  "sort": [ {"skuPrice": {"order": "desc" } } ],
  "from": 0,
  "size": 5,
  "highlight": {  
    "fields": {"skuTitle": {}}, # 高亮的字段
    "pre_tags": "<b style='color:red'>",  # 前缀
    "post_tags": "</b>"
  },
  "aggs": { # 查完后聚合
    "brandAgg": {
      "terms": {
        "field": "brandId",
        "size": 50
      },
      "aggs": { # 子聚合
        "brandNameAgg": {  # 每个商品id的品牌
          "terms": {
            "field": "brandName",
            "size": 1
          }
        },
      
        "brandImgAgg": {
          "terms": {
            "field": "brandImg",
            "size": 1
          }
        }
        
      }
    },
    "catalogAgg":{
      "terms": {
        "field": "catalogId",
        "size": 20
      },
      "aggs": { # 子聚合
        "catalogNameAgg": {
          "terms": {
            "field": "catalogName",
            "size": 1
          }
        }
      }
    },
    "attrs":{
      "nested": {"path": "attrs" },
      "aggs": {
        "attrIdAgg": {
          "terms": {
            "field": "attrs.attrId",
            "size":1
          },
          "aggs": {
            "attrNameAgg": {
              "terms": {
                "field": "attrs.attrName",
                "size": 1
              }
            },
            "attrValueAgg": {
              "terms": {
                "field": "attrs.attrValue",
                "size": 50
              }
            }
          }
        }
      }
    }
  }
}

```

## 转化为Spring中的语法

```java
private SearchRequest buildSearchRequest(SearchParam param) {

    SearchSourceBuilder searchSourceBuilder = new SearchSourceBuilder();

    /**
         * 模糊匹配，过滤（按照属性，分类，品牌，价格区间，库存）
         */
    //1. 构建bool-query
    BoolQueryBuilder boolQueryBuilder=new BoolQueryBuilder();

    //1.1 bool-must
    if(!StringUtils.isEmpty(param.getKeyword())){
        boolQueryBuilder.must(QueryBuilders.matchQuery("skuTitle",param.getKeyword()));
    }

    //1.2 bool-fiter
    //1.2.1 catelogId
    if(null != param.getCatalog3Id()){
        boolQueryBuilder.filter(QueryBuilders.termQuery("catalogId",param.getCatalog3Id()));
    }

    //1.2.2 brandId
    if(null != param.getBrandId() && param.getBrandId().size() >0){
        boolQueryBuilder.filter(QueryBuilders.termsQuery("brandId",param.getBrandId()));
    }

    //1.2.3 attrs
    if(param.getAttrs() != null && param.getAttrs().size() > 0){

        param.getAttrs().forEach(item -> {
            //attrs=1_5寸:8寸&2_16G:8G
            BoolQueryBuilder boolQuery = QueryBuilders.boolQuery();


            //attrs=1_5寸:8寸
            String[] s = item.split("_");
            String attrId=s[0];
            String[] attrValues = s[1].split(":");//这个属性检索用的值
            boolQuery.must(QueryBuilders.termQuery("attrs.attrId",attrId));
            boolQuery.must(QueryBuilders.termsQuery("attrs.attrValue",attrValues));

            NestedQueryBuilder nestedQueryBuilder = QueryBuilders.nestedQuery("attrs",boolQuery, ScoreMode.None);
            boolQueryBuilder.filter(nestedQueryBuilder);
        });

    }

    //1.2.4 hasStock
    if(null != param.getHasStock()){
        boolQueryBuilder.filter(QueryBuilders.termQuery("hasStock",param.getHasStock() == 1));
    }


    //1.2.5 skuPrice
    if(!StringUtils.isEmpty(param.getSkuPrice())){
        //skuPrice形式为：1_500或_500或500_
        RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("skuPrice");
        String[] price = param.getSkuPrice().split("_");
        if(price.length==2){
            rangeQueryBuilder.gte(price[0]).lte(price[1]);
        }else if(price.length == 1){
            if(param.getSkuPrice().startsWith("_")){
                rangeQueryBuilder.lte(price[1]);
            }
            if(param.getSkuPrice().endsWith("_")){
                rangeQueryBuilder.gte(price[0]);
            }
        }
        boolQueryBuilder.filter(rangeQueryBuilder);
    }

    //封装所有的查询条件
    searchSourceBuilder.query(boolQueryBuilder);


    //2.排序，分页，高亮

    //2.1排序sort
    //形式为sort=hotScore_asc/desc
    if(!StringUtils.isEmpty(param.getSort())){
        String sort = param.getSort();
        String[] sortFileds = sort.split("_");

        SortOrder sortOrder="asc".equalsIgnoreCase(sortFileds[1])?SortOrder.ASC:SortOrder.DESC;

        searchSourceBuilder.sort(sortFileds[0],sortOrder);
    }

    //2.2分页from-size
    searchSourceBuilder.from((param.getPageNum()-1)*EsConstant.PRODUCT_PAGESIZE);
    searchSourceBuilder.size(EsConstant.PRODUCT_PAGESIZE);

    //2.3高亮highlighter
    if(!StringUtils.isEmpty(param.getKeyword())){

        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.field("skuTitle");
        highlightBuilder.preTags("<b style='color:red'>");
        highlightBuilder.postTags("</b>");

        searchSourceBuilder.highlighter(highlightBuilder);
    }



    //3.聚合分析
    //3.1. 按照品牌进行聚合aggs-terms-field-size
    TermsAggregationBuilder brand_agg = AggregationBuilders.terms("brand_agg");
    brand_agg.field("brandId").size(50);


    //3.1.1 品牌的子聚合-品牌名聚合aggs-aggs
    brand_agg.subAggregation(AggregationBuilders.terms("brand_name_agg")
                             .field("brandName").size(1));
    //3.1.2 品牌的子聚合-品牌图片聚合
    brand_agg.subAggregation(AggregationBuilders.terms("brand_img_agg")
                             .field("brandImg").size(1));

    searchSourceBuilder.aggregation(brand_agg);

    //3.2. 按照分类信息进行聚合
    TermsAggregationBuilder catalog_agg = AggregationBuilders.terms("catalog_agg");
    catalog_agg.field("catalogId").size(20);

    catalog_agg.subAggregation(AggregationBuilders.terms("catalog_name_agg").field("catalogName").size(1));

    searchSourceBuilder.aggregation(catalog_agg);

    //3.3. 按照属性信息进行聚合
    NestedAggregationBuilder attr_agg = AggregationBuilders.nested("attr_agg", "attrs");
    //3.3.1 按照属性ID进行聚合
    TermsAggregationBuilder attr_id_agg = AggregationBuilders.terms("attr_id_agg").field("attrs.attrId");
    attr_agg.subAggregation(attr_id_agg);
    //3.3.1.1 在每个属性ID下，按照属性名进行聚合
    attr_id_agg.subAggregation(AggregationBuilders.terms("attr_name_agg").field("attrs.attrName").size(1));
    //3.3.1.2 在每个属性ID下，按照属性值进行聚合
  attr_id_agg.subAggregation(AggregationBuilders.terms("attr_value_agg").field("attrs.attrValue").size(50));
    searchSourceBuilder.aggregation(attr_agg);

    log.debug("构建的DSL语句 {}",searchSourceBuilder.toString());

    SearchRequest searchRequest = new SearchRequest(new String[]{EsConstant.PRODUCT_INDEX},searchSourceBuilder);

    return searchRequest;
}
```

