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

#### 1.1.2. 创建索引

**请求：**

```http
put: localhost:9200/shopping
```

#### 1.1.3. 删除索引

**请求：**

```http
delete: localhost:9200/shopping
```

#### 1.1.4. 查询单个索引

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

