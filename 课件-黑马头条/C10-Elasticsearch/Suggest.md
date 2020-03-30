# 联想提示

### 1 拼写纠错

对于已经建立的articles索引库，elasticsearch还提供了一种查询模式，suggest建议查询模式

```shell
curl 127.0.0.1:9200/articles/article/_search?pretty -d '
{
    "from": 0,
    "size": 10,
    "_source": false,
    "suggest": {
        "text": "phtyon web",
        "word-phrase": {
            "phrase": {
                "field": "_all",
                "size": 1
            }
        }
    }
}'
```

当我们输入错误的关键词`phtyon web`时，es可以提供根据索引库数据得出的正确拼写`python web`

### 2 自动补全

使用elasticsearch提供的自动补全功能，因为文档的类型映射要特殊设置，所以原先建立的文章索引库不能用于自动补全，需要再建立一个自动补全的索引库

```http
curl -X PUT 127.0.0.1:9200/completions -H 'Content-Type: application/json' -d'
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

```http
curl -X PUT 127.0.0.1:9200/completions/_mapping/words -H 'Content-Type: application/json' -d'
{
     "words": {
          "properties": {
              "suggest": {
                  "type": "completion",
                  "analyzer": "ik_max_word"
              }
          }
     }
}
'
```

#### 使用logstash导入初始数据

编辑logstash_mysql_completion.conf

```shell
input{
     jdbc {
         jdbc_driver_library => "/home/python/mysql-connector-java-8.0.13/mysql-connector-java-8.0.13.jar"
         jdbc_driver_class => "com.mysql.jdbc.Driver"
         jdbc_connection_string => "jdbc:mysql://127.0.0.1:3306/toutiao?tinyInt1isBit=false"
         jdbc_user => "root"
         jdbc_password => "mysql"
         jdbc_paging_enabled => "true"
         jdbc_page_size => "1000"
         jdbc_default_timezone =>"Asia/Shanghai"
         statement => "select title as suggest from news_article_basic"
         clean_run => true
     }
}
output{
      elasticsearch {
         hosts => "127.0.0.1:9200"
         index => "completions"
         document_type => "words"
      }
}
```

执行命令导入数据

```shell
sudo /usr/share/logstash/bin/logstash -f ./logstash_mysql_completion.conf
```

#### 自动补全建议查询


```shell
curl 127.0.0.1:9200/completions/words/_search?pretty -d '
{
    "suggest": {
        "title-suggest" : {
            "prefix" : "pyth", 
            "completion" : { 
                "field" : "suggest" 
            }
        }
    }
}
'

curl 127.0.0.1:9200/completions/words/_search?pretty -d '
{
    "suggest": {
        "title-suggest" : {
            "prefix" : "python web", 
            "completion" : { 
                "field" : "suggest" 
            }
        }
    }
}
'
```

