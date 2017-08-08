---
layout: post
title: Getting Started » You Know, for Search… » Retrieving a Document
category: 技术
tags: 
- Elasticsearch
keywords: 
- Elasticsearch 
description:
---
原文地址：[Retrieving a Document](https://www.elastic.co/guide/en/elasticsearch/guide/current/_retrieving_a_document.html)

### 检索文档
ES中检索文档很容易。一个简单HTTP GET请求即可，请求参数为：（1）服务器地址 （2）index （3）type（4）文档ID。

##### 按Id检索文档
```
curl -XGET http://{ip}:{port}/{index}/{type}/{id}
```

##### 检索结果
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
                    user: [{
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
##### 检索出此index此type下的所有文档
```
curl -XGET http://{ip}:{port}/{index}/{type}/_search
```