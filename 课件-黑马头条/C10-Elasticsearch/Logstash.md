# Logstash导入数据

使用logstash 导入工具从mysql中导入数据

### Logstach安装

```shell
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```

在 /etc/yum.repos.d/ 中创建logstash.repo文件

```
[logstash-6.x]
name=Elastic repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

执行

```shell
sudo yum install logstash
```

```shell
cd /usr/share/logstash/bin/
sudo ./logstash-plugin install logstash-input-jdbc
sudo ./logstash-plugin install logstash-output-elasticsearch
scp mysql-connector-java-8.0.13.tar.gz python@10.211.55.7:~/
tar -zxvf mysql-connector-java-8.0.13.tar.gz
```

### 从MySQL导入数据到Elasticsearch

创建配置文件logstash_mysql.conf

```
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
         statement => "select a.article_id as article_id,a.user_id as user_id, a.title as title, a.status as status, a.create_time as create_time,  b.content as content from news_article_basic as a inner join news_article_content as b on a.article_id=b.article_id"
         use_column_value => "true"
         tracking_column => "article_id"
         clean_run => true
     }
}
output{
      elasticsearch {
         hosts => "127.0.0.1:9200"
         index => "articles"
         document_id => "%{article_id}"
         document_type => "article"
      }
      stdout {
         codec => json_lines
     }
}
```

```shell
sudo /usr/share/logstash/bin/logstash -f ./logstash_mysql.conf
```

### 