# 索引与类型

## 索引

#### 查看索引

```shell
curl 127.0.0.1:9200/_cat/indices
```

> 请求`curl 127.0.0.1:9200/_cat`可获取用于查询的名称

#### 创建索引

索引可以在添加文档数据时，通过动态映射的方式自动生成索引与类型。

索引也可以手动创建，通过手动创建，可以控制主分片数目、分析器和类型映射。

```http
PUT /my_index
{
    "settings": { ... any settings ... },
    "mappings": {
        "type_one": { ... any mappings ... },
        "type_two": { ... any mappings ... },
        ...
    }
}
```

**注： 在Elasticsearch 5.x版本中，设置分片与设置索引的类型字段需要分两次设置完成。**

#### 删除索引

用以下的请求来 删除索引:

```http
DELETE /my_index
```

你也可以这样删除多个索引：

```http
DELETE /index_one,index_two
DELETE /index_*
```

你甚至可以这样删除 *全部* 索引：

```http
DELETE /_all
DELETE /*
```

#### 创建头条项目文章索引库

```shell
// 文章索引
curl -X PUT 127.0.0.1:9200/articles -H 'Content-Type: application/json' -d'
{
   "settings" : {
        "index": {
            "number_of_shards" : 3,
            "number_of_replicas" : 1
        }
   }
}
'
```

## 类型和映射

*类型* 在 Elasticsearch 中表示一类相似的文档，类型由 *名称* 和 *映射* （ mapping）组成。

*映射*, mapping， 就像数据库中的 schema ，描述了文档可能具有的字段或 *属性* 、 每个字段的数据类型—比如 `string`, `integer` 或 `date`。

为了能够将时间字段视为时间，数字字段视为数字，字符串字段视为全文或精确值字符串， Elasticsearch 需要知道每个字段中数据的类型。

简单字段类型：

- #### 字符串: `text`    (在elaticsearch 2.x版本中，为string类型)
- 整数 : `byte`, `short`, `integer`, `long`
- 浮点数: `float`, `double`
- 布尔型: `boolean`
- 日期: `date`

#### 头条项目文章类型映射

```shell
curl -X PUT 127.0.0.1:9200/articles/_mapping/article -H 'Content-Type: application/json' -d'
{
     "_all": {
          "analyzer": "ik_max_word"
      },
      "properties": {
          "article_id": {
              "type": "long",
              "include_in_all": "false"
          },
          "user_id": {
              "type": "long",
              "include_in_all": "false"
          },
          "title": {
              "type": "text",
              "analyzer": "ik_max_word",
              "include_in_all": "true",
              "boost": 2
          },
          "content": {
              "type": "text",
              "analyzer": "ik_max_word",
              "include_in_all": "true"
          },
          "status": {
              "type": "integer",
              "include_in_all": "false"
          },
          "create_time": {
              "type": "date",
              "include_in_all": "false"
          }
      }
}
'
```

* `_all`字段是把所有其它字段中的值，以空格为分隔符组成一个大字符串，然后被分析和索引，但是不存储，也就是说它能被查询，但不能被取回显示。`_all`允许在不知道要查找的内容是属于哪个具体字段的情况下进行搜索。

* `analyzer`指明使用的分析器

    索引时的顺序如下：

    - 字段映射里定义的 `analyzer` 
    - 否则索引设置中名为 `default` 的分析器，默认为`standard` 标准分析器

    在搜索时，顺序有些许不同：

    - 查询自己定义的 `analyzer` 
    - 否则字段映射里定义的 `analyzer` 
    - 否则索引设置中名为 `default` 的分析器，默认为`standard` 标准分析器

* `include_in_all` 参数用于控制 `_all` 查询时需要包含的字段。默认为 true。

* `boost`可以提升查询时计算相关性分数的权重。例如`title`字段将是其他字段权重的两倍。

#### 查看映射

```shell
curl 127.0.0.1:9200/articles/_mapping/article?pretty
```

#### 映射修改

一个类型映射创建好后，可以为类型增加新的字段映射

```shell
curl -X PUT 127.0.0.1:9200/articles/_mapping/article -H 'Content-Type:application/json' -d '
{
  "properties": {
    "new_tag": {
      "type": "text"
    }
  }
}
'
```

**但是不能修改已有字段的类型映射，原因在于elasticsearch已按照原有字段映射生成了反向索引数据，类型映射改变意味着需要重新构建反向索引数据，所以并不能再原有基础上修改，只能新建索引库，然后创建类型映射后重新构建反向索引数据。**

例如，将status字段类型由integer改为byte会报错

```shell
curl -X PUT 127.0.0.1:9200/articles/_mapping/article -H 'Content-Type:application/json' -d '
{
  "properties": {
    "status": {
      "type": "byte"
    }
  }
}
'
```

需要从新建立索引

```shell
curl -X PUT 127.0.0.1:9200/articles_v2 -H 'Content-Type: application/json' -d'
{
   "settings" : {
      "index": {
          "number_of_shards" : 3,
          "number_of_replicas" : 1
       }
   }
}
'

curl -X PUT 127.0.0.1:9200/articles_v2/_mapping/article -H 'Content-Type: application/json' -d'
{
     "_all": {
          "analyzer": "ik_max_word"
      },
      "properties": {
          "article_id": {
              "type": "long",
              "include_in_all": "false"
          },
          "user_id": {
               "type": "long",
              "include_in_all": "false"
          },
          "title": {
              "type": "text",
              "analyzer": "ik_max_word",
              "include_in_all": "true",
              "boost": 2
          },
          "content": {
              "type": "text",
              "analyzer": "ik_max_word",
              "include_in_all": "true"
          },
          "status": {
              "type": "byte",
              "include_in_all": "false"
          },
          "create_time": {
              "type": "date",
              "include_in_all": "false"
          }
      }
}
'
```

##### 重新索引数据

```shell
curl -X POST 127.0.0.1:9200/_reindex -H 'Content-Type:application/json' -d '
{
  "source": {
    "index": "articles"
  },
  "dest": {
    "index": "articles_v2"
  }
}
'

```

##### 为索引起别名

为索引起别名，让新建的索引具有原索引的名字，可以让应用程序零停机。

```shell
curl -X DELETE 127.0.0.1:9200/articles
curl -X PUT 127.0.0.1:9200/articles_v2/_alias/articles
```

查询索引别名

```shell
# 查看别名指向哪个索引
curl 127.0.0.1:9200/*/_alias/articles

# 查看哪些别名指向这个索引
curl 127.0.0.1:9200/articles_v2/_alias/*
```

