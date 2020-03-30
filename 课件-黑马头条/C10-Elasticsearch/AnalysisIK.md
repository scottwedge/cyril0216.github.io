# IK中文分析器

https://github.com/medcl/elasticsearch-analysis-ik>

将elasticsearch-analysis-ik-5.6.16.zip 复制到虚拟机中

```shell
scp elasticsearch-analysis-ik-5.6.16.zip python@10.211.55.7:~/
```

安装

```shell
sudo /usr/share/elasticsearch/bin/elasticsearch-plugin install file:///home/python/elasticsearch-analysis-ik-5.6.16.zip
```

```shell
-> Downloading file:///home/python/elasticsearch-analysis-ik-5.6.16.zip
[=================================================] 100%
-> Installed analysis-ik
```

重新启动

```shell
sudo systemctl restart elasticsearch
```

##### 测试分析器

```shell
curl -X GET 127.0.0.1:9200/_analyze?pretty -d '
{
  "analyzer": "standard",
  "text": "我是&中国人"
}'

curl -X GET 127.0.0.1:9200/_analyze?pretty -d '
{
  "analyzer": "ik_max_word",
  "text": "我是&中国人"
}'
```

### 