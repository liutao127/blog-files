---
title: ES学习笔记
date: 2024-10-02 16:55:35
tags: ES
---

# ES学习笔记
<!-- more -->

## 1. 基础操作

### 1.1. 索引

#### 1.1.1 查询全部索引

**请求：**

```http
get: localhost:9200/_cat/indices?v
```

**返回数据：**

health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size dataset.size
yellow open   shopping tOf7_mfST2e4S3BKaRWmDA   1   1          7            0     32.6kb         32.6kb       32.6kb

#### 1.1.2. 查询索引字段映射关系

**请求：**

```http
get: localhost:9200/user/_mapping
```

**返回数据：**

```json
{
    "user": {
        "mappings": {
            "properties": {
                "name": {
                    "type": "text"
                },
                "sex": {
                    "type": "keyword"
                },
                "tel": {
                    "type": "keyword",
                    "index": false
                }
            }
        }
    }
}
```

#### 1.1.3. 创建索引

##### 不指定映射关系创建索引

**请求：**

```http
put: localhost:9200/shopping
```

##### 指定映射关系创建索引

**请求：**

```http
put: localhost:9200/user/_mapping
```

**请求体参数：**

```json
{
    "properties": {
        "name": {
            "type": "text", // 可以被分词查询
            "index": true
        },
        "sex": {
            "type": "keyword", // 不可分词，必须完整匹配
            "index": true
        },
        "tel": {
            "type": "keyword",
            "index": false
        }
    }
}
```

#### 1.1.4. 删除索引

**请求：**

```http
delete: localhost:9200/shopping
```

#### 1.1.5. 查询单个索引

**请求：**

```http
get: localhost:9200/shopping
```

**返回数据：**

```json
{
    "shopping": {
        "aliases": {},
        "mappings": {
            "properties": {
                "category": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "images": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                },
                "price": {
                    "type": "long"
                },
                "title": {
                    "type": "text",
                    "fields": {
                        "keyword": {
                            "type": "keyword",
                            "ignore_above": 256
                        }
                    }
                }
            }
        },
        "settings": {
            "index": {
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "number_of_shards": "1",
                "provided_name": "shopping",
                "creation_date": "1727341800772",
                "number_of_replicas": "1",
                "uuid": "tOf7_mfST2e4S3BKaRWmDA",
                "version": {
                    "created": "8512000"
                }
            }
        }
    }
}
```

#### 1.1.6. 创建分片索引、索引备份

**请求：**

```http
put: localhost:1001/user
```

**请求体数据：**

```json
{
    "settings": {
        "number_of_shards": 3,
        "number_of_replicas": 1
    }
}
```

**返回数据：**

```json
// 发送后返回的数据
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "user"
}


// 重新获取索引信息返回数据
{
    "user": {
        "aliases": {},
        "mappings": {},
        "settings": {
            "index": {
                "routing": {
                    "allocation": {
                        "include": {
                            "_tier_preference": "data_content"
                        }
                    }
                },
                "number_of_shards": "3",
                "provided_name": "user",
                "creation_date": "1728096051263",
                "number_of_replicas": "1",
                "uuid": "12OOnboeRtynpz36796Z6A",
                "version": {
                    "created": "8512000"
                }
            }
        }
    }
}
```

#### 1.1.7. 调整索引副本数量

**请求：**

```http
put: localhost:1001/user/_settings
```

**请求体数据：**

```json
{
   "number_of_replicas": 2
}
```

**返回数据：**

```json
{
    "acknowledged": true
}
```



### 1.2. 文档

#### 1.2.1. 创建单个-post-（不指定ID）

**请求：**

```http
post: localhost:9200/shopping/_doc
```

**请求体参数：**

```json
{
    "title": "小米14ultra",
    "category": "小米",
    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
    "price": 7699
}
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "voyxUpIBJbX_6RwWIN41",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 17,
    "_primary_term": 4
}
```

#### 1.2.2. 创建单个-post-（指定ID）

**请求：**

```http
post: localhost:9200/shopping/_create/1006
```

**请求体参数：**

```json
{
    "title": "mateXT",
    "category": "华为",
    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
    "price": 39999
}
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1006",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 18,
    "_primary_term": 4
}
```

#### 1.2.3. 创建单个-put

**请求：**

```http
put: localhost:9200/shopping/_doc/1007
```

**请求体参数：**

```json
{
    "title": "小米14ultra",
    "category": "小米",
    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
    "price": 7699
}
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1007",
    "_version": 1,
    "result": "created",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 19,
    "_primary_term": 4
}
```

#### 1.2.4. 全查询

**请求：**

```http
get: localhost:9200/shopping/_search
```

**返回数据：**

```json
{
    "took": 73,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 10,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 1.0,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            },
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1004",
                "_score": 1.0,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.2.5. 主键查询

**请求：**

```http
get: localhost:9200/shopping/_doc/1004
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1005",
    "_version": 1,
    "_seq_no": 16,
    "_primary_term": 3,
    "found": true,
    "_source": {
        "title": "mateXT",
        "category": "华为",
        "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
        "price": 39999
    }
}
```

#### 1.2.6. 全量修改

**put：属于全量修改，修改后，该条数据将进行全部的替换。**

**post： 属于局部修改，修改后，该条数据的部分内容只会进行添加以及局部覆盖。**

****

**请求：**

```http
put: localhost:9200/shopping/_doc/1002
```

**请求体参数：**

```json
{
    "title": "小米14ultra",
    "category": "小米",
    "price": 9999
}
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1002",
    "_version": 4,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 20,
    "_primary_term": 4
}
```

#### 1.2.7. 局部修改

**请求：**

```http
post: localhost:9200/shopping/_update/1002
```

**请求体参数：**

```json
{
    "doc": {
        "title": "华为XT",
        "category": "华为",
        "price": 39999
    }
}
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1002",
    "_version": 5,
    "result": "updated",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 21,
    "_primary_term": 4
}
```

#### 1.2.8. 删除

**请求：**

```http
delete: localhost:9200/shopping/_doc/1002
```

**返回数据：**

```json
{
    "_index": "shopping",
    "_id": "1002",
    "_version": 6,
    "result": "deleted",
    "_shards": {
        "total": 2,
        "successful": 1,
        "failed": 0
    },
    "_seq_no": 23,
    "_primary_term": 4
}
```

### 1.3. 文档-查询

#### 1.3.1. 条件查询-url参数

**请求：**

```http
get: localhost:9200/shopping/_search?q=category:小米
```

**返回数据：**

```json
{
    "took": 79,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 7,
            "relation": "eq"
        },
        "max_score": 0.5753642,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1003",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.3.2. 条件查询-请求体参数

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match": {
            "category": "小米"
        }
    }
}
```

**返回数据：**

```json
{
    "took": 4,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 7,
            "relation": "eq"
        },
        "max_score": 0.5753642,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1007",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.3.3. 全量查询-请求体参数

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match_all": {

        }
    }
}
```

**返回数据：**

```json
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 9,
            "relation": "eq"
        },
        "max_score": 1.0,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 1.0,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1007",
                "_score": 1.0,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.3.4. 条件、分页、排序查询

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match_all": {
            
        }
    },
    "from": 2,
    "size": 2,
    "_source": ["title"],
    "sort": {
        "price": {
            "order": "desc"
        }
    }
}
```

**返回数据：**

```json
{
    "took": 31,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 9,
            "relation": "eq"
        },
        "max_score": null,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": null,
                "_source": {
                    "title": "小米14ultra"
                },
                "sort": [
                    7699
                ]
            },
            {
                "_index": "shopping",
                "_id": "PLqdLZIBt_mRXLwlpJJQ",
                "_score": null,
                "_source": {
                    "title": "小米14ultra"
                },
                "sort": [
                    7699
                ]
            }
        ]
    }
}
```

#### 1.3.5. 条件查询-多条件查询（must => and、&&）

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "bool": {
            "must": [
                {
                    "match":{
                        "category": "小米"
                    }
                },
                {
                    "match": {
                        "price": 7699
                    }
                }
            ]
        }
    }
}
```

**返回参数：**

```json
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 6,
            "relation": "eq"
        },
        "max_score": 1.5753641,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 1.5753641,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            },
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1007",
                "_score": 1.5753641,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.3.6. 条件查询-多条件查询（should => or、||）

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "bool": {
            "should": [
                {
                    "match":{
                        "category": "小米"
                    }
                },
                {
                    "match": {
                        "category": "华为"
                    }
                }
            ],
            "filter": {
                "range": {
                    "price": {
                        "gt": 9999
                    }
                }
            }
        }
    }
}
```

**返回数据：**

```json
{
    "took": 12,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 2.7725885,
        "hits": [
            {
                "_index": "shopping",
                "_id": "1005",
                "_score": 2.7725885,
                "_source": {
                    "title": "mateXT",
                    "category": "华为",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 39999
                }
            },
            {
                "_index": "shopping",
                "_id": "1006",
                "_score": 2.7725885,
                "_source": {
                    "title": "mateXT",
                    "category": "华为",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 39999
                }
            }
        ]
    }
}
```

#### 1.3.7. 条件查询-全文检索匹配（分词查询）

**因为ES在存储数据的时候会将字段进行分词的操作，并且在查询的时候传入的字段也会将其进行分词，所以一个字：牛！**

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match": {
            "category": "小华"
        }
    }
}
```

**返回结果：**

```json
{
    "took": 3,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 9,
            "relation": "eq"
        },
        "max_score": 1.3862942,
        "hits": [
            {
                "_index": "shopping",
                "_id": "1005",
                "_score": 1.3862942,
                "_source": {
                    "title": "mateXT",
                    "category": "华为",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 39999
                }
            },
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "PbqgLZIBt_mRXLwl8pIN",
                "_score": 0.2876821,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            },
            {
                "_index": "shopping",
                "_id": "1007",
                "_score": 0.2876821,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                }
            }
        ]
    }
}
```

#### 1.3.8. 条件查询-完全匹配（一）

**放在match_phrase参数下面的查询条件将不会进行分词查询，而是完全匹配查询。**

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match_phrase": {
            "category": "小华"
        }
    }
}
```

**返回数据：**

```json
{
    "took": 8,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 0,
            "relation": "eq"
        },
        "max_score": null,
        "hits": []
    }
}
```

#### 1.3.9. 条件查询-完全匹配（二）

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match_phrase": {
            "category": "华为"
        }
    }
}
```

**返回结果：**

```json
{
    "took": 2,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 2,
            "relation": "eq"
        },
        "max_score": 2.7725885,
        "hits": [
            {
                "_index": "shopping",
                "_id": "1005",
                "_score": 2.7725885,
                "_source": {
                    "title": "mateXT",
                    "category": "华为",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 39999
                }
            },
            {
                "_index": "shopping",
                "_id": "1006",
                "_score": 2.7725885,
                "_source": {
                    "title": "mateXT",
                    "category": "华为",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 39999
                }
            }
        ]
    }
}
```

#### 1.3.10. 查询-高亮显示

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "query": {
        "match_phrase": {
            "category": "小米"
        }
    },
    "highlight": {
        "fields": {
            "category": {}
        }
    }
}
```

**返回结果：**

```json
{
    "took": 35,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 7,
            "relation": "eq"
        },
        "max_score": 0.5753642,
        "hits": [
            {
                "_index": "shopping",
                "_id": "O7qcLZIBt_mRXLwl8JKA",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                },
                "highlight": {
                    "category": [
                        "<em>小米</em>"
                    ]
                }
            },
            ...
            ...
            ...
            {
                "_index": "shopping",
                "_id": "1007",
                "_score": 0.5753642,
                "_source": {
                    "title": "小米14ultra",
                    "category": "小米",
                    "images": "https://cdn.cnbj0.fds.api.mi-img.com/b2c-shopapi-pms/pms_1708570856.00833194.png",
                    "price": 7699
                },
                "highlight": {
                    "category": [
                        "<em>小米</em>"
                    ]
                }
            }
        ]
    }
}
```

#### 1.3.11. 聚合查询-分组查询

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "aggs": { // 聚合操作
        "price_group": { // 名称：随意起名
            "terms": { // 分组
                "field": "price" // 分组字段
            }
        }
    },
    "size": 0 // 如果不加size，那么结果会同时返回命中记录
}
```

**返回结果：**

```json
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 9,
            "relation": "eq"
        },
        "max_score": null,
        "hits": []
    },
    "aggregations": {
        "price_group": {
            "doc_count_error_upper_bound": 0,
            "sum_other_doc_count": 0,
            "buckets": [
                {
                    "key": 7699,
                    "doc_count": 6
                },
                {
                    "key": 39999,
                    "doc_count": 2
                },
                {
                    "key": 4999,
                    "doc_count": 1
                }
            ]
        }
    }
}
```

#### 1.3.12. 聚合查询-求取平均值

**请求：**

```http
get: localhost:9200/shopping/_search
```

**请求体参数：**

```json
{
    "aggs": { // 聚合操作
        "price_avg": { // 名称：随意起名
            "avg": { // 平均值
                "field": "price" // 分组字段
            }
        }
    },
    "size": 0 // 如果不加size，那么结果会同时返回命中记录
}
```

**返回结果：**

```json
{
    "took": 1,
    "timed_out": false,
    "_shards": {
        "total": 1,
        "successful": 1,
        "skipped": 0,
        "failed": 0
    },
    "hits": {
        "total": {
            "value": 9,
            "relation": "eq"
        },
        "max_score": null,
        "hits": []
    },
    "aggregations": {
        "price_avg": {
            "value": 14576.777777777777
        }
    }
}
```

### 1.4. 集群

#### 1.4.1. 集群状态查询

**请求：**

```http
get: localhost:1001/_cluster/health
```

**返回数据：**

```json
{
    "cluster_name": "my-application",
    "status": "green",
    "timed_out": false,
    "number_of_nodes": 3,
    "number_of_data_nodes": 3,
    "active_primary_shards": 3,
    "active_shards": 6,
    "relocating_shards": 0,
    "initializing_shards": 0,
    "unassigned_shards": 0,
    "delayed_unassigned_shards": 0,
    "number_of_pending_tasks": 0,
    "number_of_in_flight_fetch": 0,
    "task_max_waiting_in_queue_millis": 0,
    "active_shards_percent_as_number": 100.0
}
```



#### 1.4.2. 集群资源使用情况

**请求：**

```http
get: localhost:1001/_nodes/stats
```

**返回数据：**

```json
{
    "_nodes": {
        "total": 3,
        "successful": 3,
        "failed": 0
    },
    "cluster_name": "my-application",
    "nodes": {
        "tNKMEgWBSOO5e1MbjaMFYw": {...info...},
        "5-BKblyfQl2OiC28Hqdg9g": {...info...},
        "N5zro8OsTx-yOjImfd1EPw": {...info...}
    }
}
```

### 1.5. 分析器

​		如果要使用外部分析器，可以引入到es下的plugins文件夹下，再进行相关配置后，即可使用外部分词器，并且可以配置自定义的分词规则。

#### 1.5.1. 标准分析器

**请求：**

```http
get: localhost:1001/_analyze
```

**请求体数据：**

```json
{
    "analyzer": "standard",
    "text": "Text to analyze"
}
```

**返回结果：**

```json
{
    "tokens": [
        {
            "token": "text",
            "start_offset": 0,
            "end_offset": 4,
            "type": "<ALPHANUM>",
            "position": 0
        },
        {
            "token": "to",
            "start_offset": 5,
            "end_offset": 7,
            "type": "<ALPHANUM>",
            "position": 1
        },
        {
            "token": "analyze",
            "start_offset": 8,
            "end_offset": 15,
            "type": "<ALPHANUM>",
            "position": 2
        }
    ]
}
```

#### 1.5.2. IK分词器

**请求：**

```http
get: localhost:1001/_analyze
```

**请求体数据：**

```json
{
    "text": "测试单词",
    "analyzer": "ik_max_word" // ik_smart
}
```

**返回结果：**

```json
{
    "tokens": [
        {
            "token": "测试",
            "start_offset": 0,
            "end_offset": 1,
            "type": "<IDEOGRAPHIC>",
            "position": 0
        },
        {
            "token": "单词",
            "start_offset": 1,
            "end_offset": 2,
            "type": "<IDEOGRAPHIC>",
            "position": 1
        }
    ]
}
```

#### 1.5.3. 自定义分析器

自定义分析器包含三部分：字符过滤器、分词器、词单元过滤器。

**请求：**

```http
put: http://localhost:1001/my_index
```

**请求体数据：**

```json
{
    "settings": {
        "analysis": {
            "char_filter": {
                "&_to_and": {
                    "type": "mapping",
                    "mappings": ["&=>and"]
                }
            },
            "filter": {
                "my_stopwords": {
                    "type": "stop",
                    "stopwords": ["the", "a"]
                }
            },
            "analyzer": {
                "my_analyzer": { // 分词器名称
                    "type": "custom", // 分词器类型
                    "char_filter": ["html_strip", "&_to_and"], // 字符过滤器
                    "tokenizer": "standard", // token
                    "filter": ["lowercase", "my_stopwords"] // 过滤器
                }
            }
        }
    }
}
```

**返回数据：**

```json
{
    "acknowledged": true,
    "shards_acknowledged": true,
    "index": "my_index"
}
```

#### 1.5.4. 验证自定义过滤器

**请求：**

```http
get: http://localhost:1001/my_index/_analyze
```

**请求体数据：**

```json
{
    "text": "The quick & brown fox",
    "analyzer": "my_analyzer"
}
```

**返回结果：**

```json
{
    "tokens": [
        {
            "token": "quick",
            "start_offset": 4,
            "end_offset": 9,
            "type": "<ALPHANUM>",
            "position": 1
        },
        {
            "token": "and",
            "start_offset": 10,
            "end_offset": 11,
            "type": "<ALPHANUM>",
            "position": 2
        },
        {
            "token": "brown",
            "start_offset": 12,
            "end_offset": 17,
            "type": "<ALPHANUM>",
            "position": 3
        },
        {
            "token": "fox",
            "start_offset": 18,
            "end_offset": 21,
            "type": "<ALPHANUM>",
            "position": 4
        }
    ]
}
```



## 2. 核心概念

### 2.1. 路由计算

#### 路由计算

**含义：**保存数据时如果有多个分片，应该往第几个分片节点中存储数据的计算规则。

**算法：**hash算法

**公式：**hash（id）% 主分片数量（3） = 【0,1,2】

一旦保存成功后，副本也会同时进行数据的备份。

### 2.2. 分片控制

​		**分片控制（轮训操作）：**用户可以访问任何一个节点获取数据，这个节点称之为**协调节点**（调度协调）。==》查询数据时无论是主分片还是副本，都可以进行访问获取数据，因为存储数据的方式相同，所以查询数据的方式也可以相同。

### 2.3. 写数据流程

#### 正常流程

1. 客户端请求集群节点（任意）-协调节点；
2. 协调节点将请求转换到指定节点；
3. 主分片将数据保存；
4. 主分片将数据发送给副本；
5. 副本保存数据后，进行反馈；
6. 反馈客户端成功请求。

#### 参数干预流程

**通过安全的代价换取快速的响应。**

**一致性参数：consistency。**

- one：只要主分片状态ok就允许执行写操作。
- all：必须主分片和所有副本分片的状态没问题才允许执行写操作。
- quorum（默认值）：大多数分片副本状态没问题就允许执行写操作。

规定数量的计算结果：int(( primary_count + replicas_count ) / 2 ) + 1 = ?

**超时参数：timeout。**

默认为1分钟，如果没有足够的副本分片，es会等待，希望出现更多的分片，如果需要可以调整timeout参数使它更早终止：100 - 100毫秒，30s - 30秒。

### 2.4. 读数据流程

1. 客户端发送查询请求到协调节点；
2. 协调节点计算数据所在的分片以及全部的副本位置；
3. 为了负载均衡，轮训所有的节点；
4. 将请求转发给具体的节点（当前压力小的节点）；
5. 节点返回查询结果，将结果反馈给客户端。

### 2.5. 更新流程

#### 单文档更新

部分更新一个文档会结合上面的读和写流程（因为要先读才能写）。

1. 客户端发送更新请求到协调节点；
2. 协调节点将请求转发到主分片所在的节点；
3. 主分片所在节点检索文档，修改 _source 字段中的JSON，并且尝试重新索引主分片的文档。如果文档已经被另一个进程修改（文件锁），它会重新尝试步骤3，超过retry_on_confict次后放弃；
4. 如果主分片成功更新了文档，他会将新的文档并行转发到其他节点的副本分片，重新建立索引。一旦所有副本分片都返回成功，主分片节点向协调节点也返回成功，协调节点再向客户端返回成功。

#### 多文档更新

相似于单文档更新操作。

1. 在查询时可以将查询操作并行的分配到不同节点来进行查询（还是单点操作）；
2. 批量写入时和单点写入相同。

### 2.6. 分片原理

分片是es最小的工作单元，写入、读取操作都是基于分片来完成的。

1. 收到写入操作请求；
2. 路由计算分片位置；
3. 进行写入操作；
4. 写入完成后进行**倒排索引**建立；
5. 后续查询中用到倒排索引，就可以快速查询到数据。

### 2.7. 倒排索引

es使用的是一种倒排索引的结构，它适用于快速的全文搜索。

```
# 传统数据存储
1001    my name is zhangsan

#倒排索引数据存储
name 1001
zhang 1001
zhangsan 1001
```

搜索过程：

1. 先在词典中查询该单词是否存在，如果不存在直接进行查询返回结果；
2. 在词典中，查询该单词在倒排列表中的指针，通过该指针获取文档对应id列表，通过文档id去拿数据，返回结果。

### 2.8. 分词器

```
keyword 不可以分词
text 	可以分词
ik_max_word 最细粒度的拆分（能拆就拆）
ik_smart 最粗粒度的拆分
```

- 词条：索引中最小存储和查询单元（英文：单词；中文：词组）。
- 词典（字典）：词条的集合，B+ tree、HashMap。
- 倒排表：关键词出现的位置、频率。每条记录被称为倒排项。

### 2.9. 文档搜索

​		早期全文检索会为整个文档集合建立一个很大的倒排索引并将其写入到磁盘，一旦新的索引就绪，旧的就会被其替换，这样最近的变化便可以被检索到。倒排索引被写入磁盘后是不可改变的：它永远不会修改。不变性的价值如下：

- 不需要锁。如果从来不更新索引，就不需要担心多进程同时修改数据的问题。
- 一旦索引被读入内核的文件系统缓存，便会留在那里，由于不变性，只要系统缓存足够大，那么大部分读取请求直接请求内存，性能很大提升。
- 其他缓存（如filter），在索引生命周期内始终有效。他们不需要再每次数据改变时被重建，因为数据不会变化。
- 写入单个大的倒排索引允许数据被压缩，减少磁盘I/O和需要被缓存到内存的索引的使用量。

不好的地方如下，最主要的事实是 **不可修改** ：

- 如果需要让一个新的文档可被搜索，需要重建整个索引。这样要么对一个索引能包含的数据量造成很大的限制，要么对索引可被更新的频率造成了很大的限制。

-------------------------------------------------------------------------------------------------------

**新的补充索引**

​		通过新的补充索引来反映最近的修改，而不是直接重写整个倒排索引。每一个倒排索引都会被轮询到，从最早的开始查询完后再对结果进行合并。

​		es基于Lucene引入了按段搜索的概念。每一段本身都是一个倒排索引，但索引在Lucene中除表示所有段的集合外，还增加了提交点的概念（一个列出了所有已知段的文件）。

​		因为在查询中段是不可被改变的，那么数据如果删除了的时候，会将该数据进行标记（软删除），在查询时候将该数据进行过滤，就相当于删除。

​		合并：当把多个倒排索引合并为一个的时候，就会将原来标记过的删除数据进行硬删除，这样就会真正的删除。

### 2.10. 近实时（准实时）搜索

延迟：主分片的延时 + 并行写入副本的最大延时。

{% asset_img  1.jpg %}

**refresh、flush以及合并概念**

{% asset_img  2.jpg %}

### 2.11. 文档分析

分析流程：

1. 将一块文本分成适合于倒排索引的独立的“词条”；
2. 将这些词条统一化为标准格式以提高它们的“可搜索性”，或者recall分析器执行上面的工作。

分析器是将三个功能封装到一个包里：

- 字符过滤器：首先，字符串按顺序通过每个字符过滤器。他们的任务是在分词前整理字符串。一个字符过滤器可以用来去掉HTML，或者将&转化为and。
- 分词器：其次字符串被分词器分为单个的词条。一个简单的分词器遇到空格和标点时，可能会将文本拆分为词条。
- Token过滤器：最后，词条按顺序通过token过滤器。这个过程可能会改变词条（例如：小写化Quick），删除词条（例如，像a，and，the等无用词），或者增加词条（例如，像jump、leap这种同义词）。

内置分析器：

- 标准分析器（默认）
- 简单分析器
- 空格分析器
- 语言分析其

### 2.12. 文档冲突

#### 悲观并发控制

​		这种方法被关系型数据库广泛使用，它假定有变更冲突可能发生，因此阻塞访问资源以防止冲突。例如：读取一行数据之前先将其锁住，确保只有放置锁的线程能够对这行数据进行修改。

#### 乐观并发控制

​		假定冲突时不可能发生的，并且不会阻塞正在尝试的操作。然而，如果数据源在读写当中被修改，更新将会失败。应用程序接下来将决定该如何解决冲突。例如，可以重复更新、使用新数据、或者将相关情况报告给用户。

​		通过使用序列号进行更新，如果当前序列号为最新序列号，那么会成功更新，反之则失败。

#### 外部系统版本控制

​		修改数据时，在url后面添加：**?version=X&version_type=external**，其中X >= 当前数据版本号+1。

## 3. es优化

### 3.1. 硬件选择

- SSD：使用固态硬盘；
- RAID0：代表所有RAID级别中最高的存储性能，提高存储性能原理：把连续的数据分散到多个磁盘中存取，这样可以并行的进行I/O处理；
- 使用多块硬盘，可以并行I/O；
- 不要使用远程挂载的存储，比如NFS、SMB/CIFS，远程操作会对查询造成引入的延迟。

### 3.2. 分片策略

- 一个分片底层即为一个lucene索引，会消耗一定文件句柄、内存、CPU运转。
- 每一个搜索请求都需要命中索引中的每一个分片，如果分片都处于不同的节点还好，但是如果多个分片都需要再同一个节点竞争使用相同的资源就不好了。
- 用于计算相关度的词项统计是基于分片的，如果有许多分片，每一个都只有很少的数据会导致很低的相关度。

**具体的分片分配，需要对业务有一个预判，对数据容量有一个预估。**

- 控制每个分片占用硬盘容量不超过ES的最大JVM堆空间设置（一般不超过32G）；
- 考虑节点数量，一般一个节点有时候就是一台物理机，如果分片过多，大大超过了节点数数，会导致一个节点上存在多个分片，一旦该节点故障，即使保持了1个以上的副本，同样有可能导致数据丢失，集群无法恢复。所以一般都设置分片数不超过节点数的3倍。
- 节点数 <= 主分片数 * （副本数 + 1）

### 3.3. 推迟分片分配

​		如果某个节点宕机了，默认情况下，集群会等待一分钟来查看节点是否会重新加入，如果节点重新加入，那么重新恢复后的节点会保持其现有的节点，不会触发新的分片分配。这样就可以减少es再自动平衡可用分片时所带的极大开销。

​		通过修改delayed_timeout，可以延长再均衡的时间，可以全局设置页可以在索引级别进行修改。

```http
put /all/_settings
{
	“settings”： {
		“index.unassigned.node_left.delayed_timeout”: "5m"
	}
}
```

### 3.4. 路由选择

一般为：路由计算方式计算，但是路由计算需要在路由参数中携带文档的id（也可以是自定义值，比如用户id）。

- 不带路由参数（routing）：分发（请求到达协调节点后，协调节点将查询请求分发到每个分片上），聚合（协调节点搜集到每个分片上的查询结果，在将查询结果进行排序，之后返回给用户）。
- 带路由参数（routing）：直接通过参数获取到数据位置，不需要匹配所有节点。

### 3.5. 写入速度优化

- 加大Translog Flush，目的是降低Iops（每秒输入输出）、Writeblock（写锁操作）；
- 增加Index Refresh 间隔，目的是减少 Segment Merge 的次数；
- 调整 Bulk（批量数据处理） 线程池和队列；
- 优化节点间的任务分布；
- 优化 Lucene 层的索引建立，目的是减低CPU和IO的使用。

### 3.6. 批量数据提交

​		因为es提供了Bulk API支持批量操作，当我们有大量的写任务时，可以使用Bulk来进行批量写入。

​		通用的策略：Bulk 默认设置批量提交的数据量不能超过100M。数据条数一般根据文档的大小和服务器性能而设定的，但是单词批处理的数据大小应从 5M ~ 15M 逐渐增加，当性能没有提升时，把这个数据量作为最大值。

### 3.7. 优化存储设备

​		es是一种密集使用磁盘的应用，在段合并的时候会频繁操作磁盘，所以对磁盘要求较高，当磁盘速度提升之后，集群的整体性能会大幅度提高。

### 3.8. 合理使用合并

​		由于新的数据写入索引时，lucene就会自动创建一个新的段，随着段数量的变化，段的数量也会越来越多，消耗的多文件句柄数及CPU就越多，查询效率就会下降。由于lucene段合并的计算量庞大，会消耗大量的I/O，所以es默认采用较为保守的策略，让后台定期进行段合并，如果可以合理的进行段合并，性能也会提升。

### 3.9. 减少refresh的次数

​		lucene在新增数据时，采用了延迟写入的策略，默认情况下索引的refresh_interval 为1秒，也就是会先将待写入的数据写到内存中，超过1秒时就会触发一次refresh，然后refresh会把内存中的数据刷新到操作系统的文件缓存系统中。如果对搜索的时效性要求不高，可以将refresh周期延长，例如30秒。这样还可以有效的减少段刷新次数，但同时意味着需要消耗更多的Heap内存。

### 3.10. 加大flush设置

​		Flush的主要目的是把文件缓存系统中的段持久化到硬盘，当translog的数量达到512M或30分钟时，就会触发一次Flush。

​		index.tanslog.flush_threshold_size参数的默认值是512MB，我们进行修改。所以需要为操作系统的文件缓存系统留下足够的空间。

### 3.11. 减少副本数量

​		如果存在大量的写入操作，可以先禁止Replica复制，设置index.number_of_replicas: 0关闭副本。在写入完成后，Replica修改回正常的状态。

### 3.12. 内存设置

​		修改jvm.option文件，根据当前系统内存合理的分配es内存。

- 不超过物理内存的50%，因为es在refresh的时候会将部分数据存在到系统的文件缓存中。
- 堆内存最好不超过32G，和底层的指针大小相关，一般设置为32G。

### 3.13. 重要配置项

| 参数名                             | 参数值        | 说明                                                         |
| ---------------------------------- | ------------- | ------------------------------------------------------------ |
| cluster.name                       | elasticsearch | 配置es集群名称，es会自动发现在同一网段下的集群名称相同节点   |
| node.name                          | node-1        | 统一集群中不能重复                                           |
| node.master                        | true          | 是否有资格选举为主节点                                       |
| node.data                          | true          | 当前节点是不是数据节点，能不能索引数据，能不能增删改查       |
| index.number_of_shards             | 1             | 索引的分片数量                                               |
| index.number_of_replicas           | 1             | 索引的副本数量，副本越多，集群可用性越好，但同步的数据也会越多 |
| transport.tcp.compress             | true          | 节点之间传输数据的时候是否压缩，压缩会变快                   |
| discovery.zen.minimum_master_nodes | 1             | 设置选举master节点时需要参与的最小候选节点数，默认为1.如果使用默认值，则当网络不稳定时可能会出现脑裂。<br />合理的数值为（master_eligible_nodes/2）+1，其中master_eligible_nodes表示集群中的候选节点数 |
| discovery.zen.ping.timeout         | 3s            | 设置在集群中自动发现其他节点时Ping连接超时时间，默认3秒。在较差的网络环境下需要设置的大一点，防止因误判该节点的存货状态而导致分片的转移 |

## 4. 知识点

### 4.1. es是什么？

"ES" 通常是指 "Elasticsearch"，这是一个基于开源搜索引擎库 Lucene 的搜索引擎。Elasticsearch 提供了分布式、多租户能力的全文搜索引擎，具有 HTTP web 接口和无模式的 JSON 文档的特点。它可以快速地存储、搜索、分析数据。es使用的是一种倒排索引的结构，它适用于快速的全文搜索（倒排索引+分词策略）。它是基于Lucene开发的，所以也就是Lucene的倒排索引。

以下是 Elasticsearch 的一些关键特性：

1. **分布式**：Elasticsearch 设计为分布式搜索引擎，可以在多台服务器上运行，提供高可用性和扩展性。
2. **实时搜索**：Elasticsearch 提供快速的搜索能力，可以实时索引和搜索数据。
3. **高可扩展性**：可以轻松地通过增加更多的节点来扩展集群。
4. **多租户**：支持在同一集群上运行多个独立的索引。
5. **全文搜索**：支持复杂的搜索功能，包括全文搜索、模糊查询、地理位置搜索等。
6. **RESTful API**：提供易于使用的 RESTful API，方便进行数据的索引、搜索和维护。
7. **JSON 文档**：使用 JSON 格式存储数据，易于理解和使用。
8. **分析和聚合**：提供强大的数据分析和聚合功能，可以进行各种统计分析。
9. **插件生态系统**：拥有丰富的插件生态系统，可以扩展 Elasticsearch 的功能。

Elasticsearch 通常与 Logstash（数据处理管道）和 Kibana（数据可视化工具）一起使用，这三个工具合起来被称为 ELK Stack（现在是 Elastic Stack），用于构建实时的数据搜索、日志分析和可视化平台。

### 4.2. 为什么要用es？

​		系统中的数据，随着业务的发展，时间的推移，将会非常多，而业务中往往采用模糊査询进行数据的搜索，而模糊査询会导致查询引擎放弃索引，导致系统査询数据时都是全表扫描，在百万级别的数据库中,查询效率是非常低下的，而我们使用ES做一个全文索引，将经常査询的系统功能的某些字段，比如说电商系统的商品表中商品名，描述、价格还有id 这些字段我们放入 ES索引库里，可以提高査询速度。

### 4.3. es的master选举流程？

- Elasticsearch 的选主是 ZenDiscovery,模块负责的,主要包含 Ping(节点之间通过这个 RPC来发现彼此)和 Unicast(单播模块包含一个主机列表以控制哪些节点需要 ping通)这两部分；
- 对所有可以成为master 的节点(node.master: true)根据 nodeId 字典排序，每次选举每个节点都把自己所知道节点排一次序，然后选出第一个(第0位)节点，暂且认为它是 master 节点;
- 如果对某个节点的投票数达到一定的值(可以成为master 节点数 n2+1)并且该节点自己也选举自己,那这个节点就是 master。否则重新选举一直到满足上述条件;
- master 节点的职责主要包括集群、节点和索引的管理,不负责文档级别的管理; data 节点可以关闭 http功能。

### 4.4. es集群脑裂问题？

#### 造成原因

- 网络问题：集群间的网络延迟导致一些节点访问不到 master，认为master 挂掉了从而选举出新的master，并对 master 上的分片和副本标红，分配新的主分片；
- 节点负载：主节点的角色既为master 又为 data，访问量较大时可能会导致ES 停止响应造成大面积延迟，此时其他节点得不到主节点的响应认为主节点挂掉了，会重新选取主节点；
- 内存回收：data 节点上的 ES进程占用的内存较大，引发JVM的大规模内存回收，造成 ES 进程失去响应。

#### 解决方案

- 减少误判：discovery.zen.ping_timeout 节点状态的响应时间，默认为 3s，可以适当调大，如果 master在该响应时间的范围内没有做出响应应答，判断该节点已经挂掉了。调大参数(如6s，discovery.zen.ping timeout:6)，可适当减少误判；
- 选举触发: discovery.zen.minimum_master_nodes:1，该参数是用于控制选举行为发生的最小集群主节点数量。当备选主节点的个数大于等于该参数的值且备选主节点中有该参数个节点认为主节点挂了，进行选举。官方建议为(n/2)+1，n为主节点个数(即有资格成为主节点的节点个数)；
- 角色分离：角色分离:即 master 节点与 data 节点分离，限制角色。主节点配置为:node.master: true，node.data: false；从节点配置为:node.master: false，node.data: true。

### 4.5. es检索文档流程？

{% asset_img  3.jpg %}

1. 协调节点默认使用文档 ID 参与计算（也支持通过 routing），以便为路由提供合适的分片： shard = hash(document_id) % (num_of_primary_shards）；
2. 当分片所在的节点接收到来自协调节点的请求后，会将请求写入到 Memory Buffer，然后定时（默认 是每隔 1 秒）写入到 Filesystem Cache，这个从 Memory Buffer 到 Filesystem Cache 的过程就叫做 refresh；
3. 当然在某些情况下，存在 Momery Buffer 和 Filesystem Cache 的数据可能会丢失，ES 是通过 translog 的机制来保证数据的可靠性的。其实现机制是接收到请求后，同时也会写入到 translog 中，当 Filesystem  cache 中的数据写入到磁盘中时，才会清除掉，这个过程叫做 flush；
4. 在 flush 过程中，内存中的缓冲将被清除，内容被写入一个新段，段的 fsync 将创建一个新的提交点， 并将内容刷新到磁盘，旧的 translog 将被删除并开始一个新的 translog；
5. flush 触发的时机是定时触发（默认 30 分钟）或者 translog 变得太大（默认为 512M）时。

### 4.6. es更新和删除文档的流程？

1. 删除和更新也都是写操作，但是 Elasticsearch 中的文档是不可变的，因此不能被删除或者改动以展示 其变更；
2. 磁盘上的每个段都有一个相应的.del 文件。当删除请求发送后，文档并没有真的被删除，而是在.del 文件中被标记为删除。该文档依然能匹配查询，但是会在结果中被过滤掉。当段合并时，在.del 文件中被标记为删除的文档将不会被写入新段。
3. 在新的文档被创建时，Elasticsearch 会为该文档指定一个版本号，当执行更新时，旧版本的文档在.del 文件中被标记为删除，新版本的文档被索引到一个新段。旧版本的文档依然能匹配查询，但是会在结 果中被过滤掉。

### 4.7. es搜索的流程？

{% asset_img  4.jpg %}

1. 搜索被执行成一个两阶段过程，我们称之为 Query Then Fetch；
2. 在初始查询阶段时，查询会广播到索引中每一个分片拷贝（主分片或者副本分片）。 每个分片在本 地执行搜索并构建一个匹配文档的大小为 from + size 的优先队列。PS：在搜索的时候是会查询 Filesystem Cache 的，但是有部分数据还在 Memory Buffer，所以搜索是近实时的；
3. 每个分片返回各自优先队列中 所有文档的 ID 和排序值 给协调节点，它合并这些值到自己的优先队 列中来产生一个全局排序后的结果列表；
4. 接下来就是取回阶段，协调节点辨别出哪些文档需要被取回并向相关的分片提交多个 GET 请求。每 个分片加载并丰富文档，如果有需要的话，接着返回文档给协调节点。一旦所有的文档都被取回了， 协调节点返回结果给客户端；
5. Query Then Fetch 的搜索类型在文档相关性打分的时候参考的是本分片的数据，这样在文档数量较少 的时候可能不够准确，DFS Query Then Fetch 增加了一个预查询的处理，询问 Term 和 Document  frequency，这个评分更准确，但是性能会变差。

### 4.8. es在部署时，对linux的设置有哪些优化方法？

- 64 GB 内存的机器是非常理想的，但是 32 GB 和 16 GB 机器也是很常见的。少于 8 GB 会适得其反。
- 如果你要在更快的 CPUs 和更多的核心之间选择，选择更多的核心更好。多个内核提供的额外并发远胜过稍微快一点点的时钟频率。
- 如果你负担得起 SSD，它将远远超出任何旋转介质。 基于 SSD 的节点，查询和索引性能都有提升。 如果你负担得起，SSD 是一个好的选择。
- 即使数据中心们近在咫尺，也要避免集群跨越多个数据中心。绝对要避免集群跨越大的地理距离。
- 请确保运行你应用程序的 JVM 和服务器的 JVM 是完全一样的。 在 Elasticsearch 的几个地方，使 用 Java 的本地序列化。
- 通过设置 gateway.recover_after_nodes、gateway.expected_nodes、gateway.recover_after_time 可以在集群 重启的时候避免过多的分片交换，这可能会让数据恢复从数个小时缩短为几秒钟。
- Elasticsearch 默认被配置为使用单播发现，以防止节点无意中加入集群。只有在同一台机器上运行的 节点才会自动组成集群。最好使用单播代替组播。
- 不要随意修改垃圾回收器（CMS）和各个线程池的大小。
- 把你的内存的（少于）一半给 Lucene（但不要超过 32 GB！），通过 ES_HEAP_SIZE 环境变量设置。
- 内存交换到磁盘对服务器性能来说是致命的。如果内存交换到磁盘上，一个 100 微秒的操作可能变 成 10 毫秒。 再想想那么多 10 微秒的操作时延累加起来。 不难看出 swapping 对于性能是多么可 怕。
- Lucene 使用了大量的文件。同时，Elasticsearch 在节点和 HTTP 客户端之间进行通信也使用了大量 的套接字。 所有这一切都需要足够的文件描述符。你应该增加你的文件描述符，设置一个很大的值， 如 64,000。

**补充：索引阶段性能提升方法**

- 使用批量请求并调整其大小：每次批量数据 5–15 MB 大是个不错的起始点。
- 存储：使用 SSD
- 段和合并：Elasticsearch 默认值是 20 MB/s，对机械磁盘应该是个不错的设置。如果你用的是 SSD， 可以考虑提高到 100–200 MB/s。如果你在做批量导入，完全不在意搜索，你可以彻底关掉合并限流。 另外还可以增加 index.translog.flush_threshold_size 设置，从默认的 512 MB 到更大一些的值，比如 1  GB，这可以在一次清空触发的时候在事务日志里积累出更大的段。
- 如果你的搜索结果不需要近实时的准确度，考虑把每个索引的 index.refresh_interval 改到 30s。
- 如果你在做大批量导入，考虑通过设置 index.number_of_replicas: 0 关闭副本。

### 4.9. GC方面，在使用es时要注意什么？

- 倒排词典的索引需要常驻内存，无法 GC，需要监控 data node 上 segment memory 增长趋势。
- 各类缓存，field cache, filter cache, indexing cache, bulk queue 等等，要设置合理的大小，并且要应该根 据最坏的情况来看 heap 是否够用，也就是各类缓存全部占满的时候，还有 heap 空间可以分配给其他 任务吗？避免采用 clear cache 等“自欺欺人”的方式来释放内存。
- 避免返回大量结果集的搜索与聚合。确实需要大量拉取数据的场景，可以采用 scan & scroll api 来实现。
- cluster stats 驻留内存并无法水平扩展，超大规模集群可以考虑分拆成多个集群通过 tribe node 连接。
- 想知道 heap 够不够，必须结合实际应用场景，并对集群的 heap 使用情况做持续的监控。

### 4.10. es对于大数据量（上亿量级别）的聚合如何实现？

​		Elasticsearch 提供的首个近似聚合是 cardinality 度量。它提供一个字段的基数，即该字段的 distinct 或者 unique 值的数目。它是基于 HLL 算法的。HLL 会先对我们的输入作哈希运算，然后根据哈希运算的 结果中的 bits 做概率估算从而得到基数。其特点是：可配置的精度，用来控制内存的使用（更精确 ＝ 更 多内存）；小的数据集精度是非常高的；我们可以通过配置参数，来设置去重需要的固定内存使用量。无 论数千还是数十亿的唯一值，内存使用量只与你配置的精确度相关。

### 4.11. 在并发情况下，Elasticsearch 如果保证读写一致？

1. 可以通过版本号使用乐观并发控制，以确保新版本不会被旧版本覆盖，由应用层来处理具体的冲突；
2. 另外对于写操作，一致性级别支持 quorum/one/all，默认为 quorum，即只有当大多数分片可用时才允 许写操作。但即使大多数可用，也可能存在因为网络等原因导致写入副本失败，这样该副本被认为故 障，分片将会在一个不同的节点上重建。
3. 对于读操作，可以设置 replication 为 sync(默认)，这使得操作在主分片和副本分片都完成后才会返回； 如果设置 replication 为 async 时，也可以通过设置搜索请求参数_preference 为 primary 来查询主分片， 确保文档是最新版本。

### 4.12. 如何监控es集群状态？

​		elasticsearch-head 插件

​		通过 Kibana 监控 Elasticsearch。你可以实时查看你的集群健康状态和性能，也可以分析过去的集群、 索引和节点指标。

### 4.13. 是否了解字典树？

常用字典数据结构如下所示：

{% asset_img  5.jpg %}

​		字典树又称单词查找树，Trie 树，是一种树形结构，是一种哈希树的变种。典型应用是用于统计，排 序和保存大量的字符串（但不仅限于字符串），所以经常被搜索引擎系统用于文本词频统计。它的优点是： 利用字符串的公共前缀来减少查询时间，最大限度地减少无谓的字符串比较，查询效率比哈希树高。

​		Trie 的核心思想是空间换时间，利用字符串的公共前缀来降低查询时间的开销以达到提高效率的目的。 它有 3 个基本性质：

- 根节点不包含字符，除根节点外每一个节点都只包含一个字符。
- 从根节点到某一节点，路径上经过的字符连接起来，为该节点对应的字符串。
- 每个节点的所有子节点包含的字符都不相同。

​        对于中文的字典树，每个节点的子节点用一个哈希表存储，这样就不用浪费太大的空间，而且查询速度上 可以保留哈希的复杂度 O(1)。

### 4.14. es中的集群、节点、索引、文档、类型是什么？

- 集群是一个或多个节点（服务器）的集合，它们共同保存您的整个数据，并提供跨所有节点的联合索 引和搜索功能。群集由唯一名称标识，默认情况下为“elasticsearch”。此名称很重要，因为如果节点设 置为按名称加入群集，则该节点只能是群集的一部分。
- 节点是属于集群一部分的单个服务器。它存储数据并参与群集索引和搜索功能。
- 索引就像关系数据库中的“数据库”。它有一个定义多种类型的映射。索引是逻辑名称空间，映射到一 个或多个主分片，并且可以有零个或多个副本分片。 MySQL =>数据库 Elasticsearch =>索引。
- 文档类似于关系数据库中的一行。不同之处在于索引中的每个文档可以具有不同的结构（字段），但 是对于通用字段应该具有相同的数据类型。 MySQL => Databases => Tables => Columns / Rows  Elasticsearch => Indices => Types =>具有属性的文档。
- 类型是索引的逻辑类别/分区，其语义完全取决于用户。

### 4.15. es中的倒排索引是什么？

​		倒排索引是搜索引擎的核心。搜索引擎的主要目标是在查找发生搜索条件的文档时提供快速搜索。ES 中的倒排索引其实就是 lucene 的倒排索引，区别于传统的正向索引，倒排索引会再存储数据时将关键词和数据进行关联，保存到倒排表中，然后查询时，将查询内容进行分词后在倒排表中进行查询，最后匹配数 据即可。

