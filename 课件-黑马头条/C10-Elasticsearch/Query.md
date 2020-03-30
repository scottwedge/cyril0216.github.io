# 查询

### 1 基本查询

* 根据文档ID

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/1
    curl -X GET 127.0.0.1:9200/articles/article/1?_source=title,user_id
    curl -X GET 127.0.0.1:9200/articles/article/1?_source=false
    ```

* 查询所有

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?_source=title,user_id
    ```

* 分页

    * from 起始
    * size 每页数量

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?_source=title,user_id\&size=3
    
    curl -X GET 127.0.0.1:9200/articles/article/_search?_source=title,user_id\&size=3\&from=10
    ```

* 全文检索

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?q=content:python%20web\&_source=title,article_id\&pretty
    
    curl -X GET 127.0.0.1:9200/articles/article/_search?q=title:python%20web,content:python%20web\&_source=title,article_id\&pretty
    
    curl -X GET 127.0.0.1:9200/articles/article/_search?q=_all:python%20web\&_source=title,article_id\&pretty
    ```

    * %20 表示空格

### 2 高级查询

* 全文检索  match

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search -d'
    {
        "query" : {
            "match" : {
                "title" : "python web"
            }
        }
    }'
    
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "from": 0,
        "size": 5,
        "_source": ["article_id","title"],
        "query" : {
            "match" : {
                "title" : "python web"
            }
        }
    }'
    
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "from": 0,
        "size": 5,
        "_source": ["article_id","title"],
        "query" : {
            "match" : {
                "_all" : "python web 编程"
            }
        }
    }'
    ```
    
* 短语搜索  match_phrase

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "size": 5,
        "_source": ["article_id","title"],
        "query" : {
            "match_phrase" : {
                "_all" : "python web"
            }
        }
    }'
    ```

* 精确查找 term 

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "size": 5,
        "_source": ["article_id","title", "user_id"],
        "query" : {
            "term" : {
                "user_id" : 1
            }
        }
    }'
    ```

* 范围查找 range

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "size": 5,
        "_source": ["article_id","title", "user_id"],
        "query" : {
            "range" : {
                "article_id": { 
                    "gte": 3,
                    "lte": 5
                }
            }
        }
    }'
    ```

* 高亮搜索 highlight

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d '
    {
        "size":2,
        "_source": ["article_id", "title", "user_id"],
        "query": {
            "match": {
                 "title": "python web 编程"
             }
         },
         "highlight":{
              "fields": {
                  "title": {}
              }
         }
    }
    '
    ```

* 组合查询

    - must

        文档 *必须* 匹配这些条件才能被包含进来。

    - must_not

        文档 *必须不* 匹配这些条件才能被包含进来。

    - should

        如果满足这些语句中的任意语句，将增加 `_score` ，否则，无任何影响。它们主要用于修正每个文档的相关性得分。

    - filter

        *必须* 匹配，但它以不评分、过滤模式来进行。这些语句对评分没有贡献，只是根据过滤标准来排除或包含文档。

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d '
    {
        "_source": ["title", "user_id"],
        "query": {
            "bool": {
                "must": {
                    "match": {
                        "title": "python web"
                    }
                },
                "filter": {
                    "term": {
                        "user_id": 2
                    }
                }
            }
        }
    }
    '
    ```

* 排序

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "size": 5,
        "_source": ["article_id","title"],
        "query" : {
            "match" : {
                "_all" : "python web"
            }
        },
        "sort": [
            { "create_time":  { "order": "desc" }},
            { "_score": { "order": "desc" }}
        ]
    }'
    ```

* boost 提升权重，优化排序

    ```shell
    curl -X GET 127.0.0.1:9200/articles/article/_search?pretty -d'
    {
        "size": 5,
        "_source": ["article_id","title"],
        "query" : {
            "match" : {
                "title" : {
                    "query": "python web",
                    "boost": 4
                }
            }
        }
    }'
    ```

