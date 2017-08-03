---
layout: post
title: Elasticsearch Reference [2.3] » Mapping » Field datatypes » Nested datatype
category: 技术
tags: 
- Elasticsearch
keywords: 
- Elasticsearch 
description:
---

原文链接：[Elasticsearch Reference [2.3] »  Mapping » Field datatypes » Nested datatype](https://www.elastic.co/guide/en/elasticsearch/reference/current/nested.html)

### Nested datatype
嵌入类型（nested type）是一种特殊的对象数据类型。有了它，ES可以为对象的数组创建索引，并且对数组的每个对象可以进行独立查询。
### 对象数组的扁平化
对象数组的索引方式可能跟你预想的不一样。由于Lucene中没有对象类型的field，ES把对象类型的field都转为一个field和value的列表。例如：
##### 将document写入index
	curl -XPUT http://10.5.237.212:8411/order/orderDetail/1 -d {"group" : "fans","user" : [ {"first" : "John","last" :  "Smith"},{"first" : "Alice","last" :  "White"}]}
上面添加的user这个field 是一个对象数组，这个document按id查出如下：
##### 查出id为1的记录
```
curl -XGET http://10.5.237.212:8411/order/orderDetail/1

{
    _index: "order",
    _type: "orderDetail",
    _id: "1",
    _version: 1,
    found: true,
    _source: {
                group: "fans",
                user: [
                        {
                            first: "John",
                            last: "Smith"
                        },
                            {
                            first: "Alice",
                            last: "White"
                        }
                ]
    }
}
```
而这个document在ES内部会被转化为类似下面这种结构，

```
{
    group: "fans",
    user.first: ["alice","john"],
    user.last: ["smith","white"]
}
```

##### 按first和last查
```
curl -XGET http://10.5.237.212:8411/order/_search -d
    '{
        "query":{
                "bool":{
                        "must":[
                                {"match":{"user.first":"Alice"}},
                                {"match":{"user.last":"Smith"}}
                        ]}
        }
    }
```
##### 检索结果
```
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 1,
        "max_score": 0.2712221,
        "hits": [{
            "_index": "order",
            "_type": "orderDetail",
            "_id": "1",
            "_score": 0.2712221,
            "_source": {
                "group": "fans",
                "user": [{
                    "first": "John",
                    "last": "Smith"
                }, {
                    "first": "Alice",
                    "last": "White"
                }]
            }
        }]
    }
}
```
### 将对象数组映射为nested field
若你想在给对象数组建索引的时候保持每个对象的相互独立（不被扁平化存储），就应该选用nested类型而不是object类型来存。在ES内部，nested类型把数组的每个对象都索引为一个独立的内部document，这就意味着，按nested检索方式，每个对象能够被独立检索出来。

|JSON type|Field type|
|---|---|
|Boolean:true or false|boolean|
|Whole number: 123|long|
|Floating point:123.45|double|
|String in valid date: 2014-09-15|date|
|String: foo bar|string|

>注意：如果你把值为“123”的field加入索引，ES会把它当做string。但如果这个field已经被mapping为long，ES将把它转为long，如果无法解析则抛异常。

##### 查看Mapping
```
curl -XPUT http://10.5.237.212:8411/order/orderDetail/1 -d
  {
    "group" : "fans",
    "user" : [
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]}
```
##### 返回结果
```
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 5,
        "successful": 5,
        "failed": 0
    },
    "hits": {
        "total": 1,
        "max_score": 0.2712221,
        "hits": [{
            "_index": "order",
            "_type": "orderDetail",
            "_id": "1",
            "_score": 0.2712221,
            "_source": {
                "group": "fans",
                "user": [{
                    "first": "John",
                    "last": "Smith"
                }, {
                    "first": "Alice",
                    "last": "White"
                }]
            }
        }]
    }
}
```