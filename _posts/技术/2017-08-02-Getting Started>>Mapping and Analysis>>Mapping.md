---
layout: post
title: ElasticSearch学习笔记
category: 技术
tags: ElasticSearch
keywords: ElasticSearch, ES
description: 
---
原文链接: [Getting Started>>Mapping and Analysis>>Mapping](https://www.elastic.co/guide/en/elasticsearch/guide/current/mapping-intro.html)

Mapping

为了能够正确的区分document中各种field的类型，比如：把date field分析为dates，numeric field分析为numbers，string field分析为可分词（full-text）或不可分词（exact-value）的string。ES需要知道每个field的数据类型是什么。这都是在mapping中设置的。
ES中的一个index下的每个type都有自己的mapping。

ES支持如下field类型：

- String：string
- Whole number: byte, short, integer, long
- Floating-point: float, double
- Boolean: boolean
- Date: date

当你在ES中给一个document创建索引时，如果mapping中没有这个document中field对应的类型，ES会用dynamic mapping的方式来给field指定一个类型。
规则如下：

|JSON type|Field type|
|:-------------:|:-------------:|
|Boolean:true or false|boolean|
|Whole number: 123|long|
|Floating point:123.45|double|
|String in valid date: 2014-09-15|date|
|String: foo bar|string|

>注意：如果你把值为“123”的field加入索引，ES会把它当做string。但如果这个field已经被mapping为long，ES将把它转为long，如果无法解析则抛异常。

#####查看Mapping

	curl -XGET http://{ip}:{port}/{index_name}/_mapping/{type_name}
#####返回结果
```
{
   "{index_name}": {
      "mappings": {
         "{type_name}": {
            "properties": {
               "{field_name}": {
                  "type": "date",
                  "format": "strict_date_optional_time||epoch_millis"
               },
               "{field_name}": {
                  "type": "string"
               },
               "{field_name}": {
                  "type": "long"
               }
            }
         }
      }
   }
}
```
###1.1 自定义Field Mapping
尽管基本的field数据类型已经满足大部分情况，有时也需要为某些field定制mapping。你可以：

- 指定可分词（full-text）或不可分词（exact-value）的string field。
- 指定某语种的分析器
- 部分匹配某field
- 指定date field的显示格式（date format）
- 其他
type是mapping中最重要的属性。你只需指定field的type属性即可满足大多数业务要求，但数据类型为string的field除外。

```
{
   "number_of_clicks": {
        "type": "integer"
    }
}
```
string field默认为可分词的（full-text）。可分词的string field在建立索引之前要先经过analyzer分析；随后在这个field上的查询string也会先被analyzer分析后再查询。

string field的两个最重要的mapping属性是index和analyzer。
index 取值：

- analyzed：对field先分析再建立索引。即将field指定为可分词的。（默认）
- not_analyzed：直接建立索引。即field不可分词。
- no：不对此field建立索引。

>注意
其他简单类型如long，double，date等也有index属性，但是只能指定为no或not_analyzed。

analyzer：此属性用来指定为string field建立索引和按索引搜索时的analyzer。默认情况ES采用standard analyzer，也可指定为其他的内置analyzer，例如：whitespace，simple或english。

###1.2 更新Mapping
当创建索引时你可以指定mapping；索引创建后，也可以给一个mapping添加新field并为其指定type。
但是，你不应去修改一个已经存在的field的Mapping设置，因为此field的索引已经创建，若修改了它的Mapping，索引就不可用了。
#####删除Index
	curl -XDELETE http://10.5.237.212:8411/order
#####创建index和type并制定mapping
	curl -XPUT http://10.5.237.212:8411/order -d '{"mappings":{"orderDetail":{"properties":{"tag":{"type":"string","index":"not_analyzed"}}}}}'
#####为mapping添加新field并指定type属性
	curl -XPUT http://10.5.237.212:8411/order/_mapping/orderDetail -d '{"properties":{"tag_new":{"type":"string","index":"analyzed"}}}'
###1.3 测试Mapping
可以用analyze API来测试mapping
#####测试type为not_analyze的field
	curl -XGET http://10.5.237.212:8411/order/_analyze -d '{"field":"tag","text":"New York"}'
#####测试结果
```
{
    "tokens":[
            {
                "token":"New York",
                "start_offset":0,
                "end_offset":8,
                "type":"word",
                "position":0
            }]
}
```
#####测试type为analyze的field
	curl -XGET http://10.5.237.212:8411/order/_analyze -d '{"field":"tag_new","text":"New York"}'
#####测试结果
```
{
    "tokens":[
            {
                "token":"new",
                "start_offset":0,
                "end_offset":3,
                "type":"<ALPHANUM>",
                "position":0
            },
            {
                "token":"york",
                "start_offset":4,
                "end_offset":8,
                "type":"<ALPHANUM>",
                "position":1
            }]
}
```
