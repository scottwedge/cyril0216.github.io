# 头条全文检索实现

### elasticsearch python客户端使用

https://elasticsearch-py.readthedocs.io/en/master/>

```shell
pip install elasticsearch
```

对于elasticsearch 5.x 版本 需要按以下方式导入

```python
from elasticsearch5 import Elasticsearch

# elasticsearch集群服务器的地址
ES = [
    '127.0.0.1:9200'
]

# 创建elasticsearch客户端
es = Elasticsearch(
    ES,
    # 启动前嗅探es集群服务器
    sniff_on_start=True,
    # es集群服务器结点连接异常时是否刷新es结点信息
    sniff_on_connection_fail=True,
    # 每60秒刷新结点信息
    sniffer_timeout=60
)
```

搜索使用方式

```python
query = {
    'query': {
        'bool': {
            'must': [
                {'match': {'_all': 'python web'}}
            ],
            'filter': [
                {'term': {'status': 2}}
            ]
        }
    }
}
ret = es.search(index='articles', doc_type='article', body=query)
```

### 头条项目搜索接口视图实现

在toutiao-backend/toutiao/resources/search目录中新建search.py

```python
from flask_restful import Resource
from flask_restful.reqparse import RequestParser
from flask_restful import inputs
from flask import g, current_app
from redis.exceptions import RedisError

from . import constants
from cache import article as cache_article
from cache import user as cache_user
from models.user import Search
from models import db

class SearchResource(Resource):
    """
    搜索结果
    """
    def get(self):
        """
        获取搜索结果
        """
        qs_parser = RequestParser()
        qs_parser.add_argument('q', type=inputs.regex(r'^.{1,50}$'), required=True, location='args')
        qs_parser.add_argument('page', type=inputs.positive, required=False, location='args')
        qs_parser.add_argument('per_page', type=inputs.int_range(constants.DEFAULT_SEARCH_PER_PAGE_MIN, constants.DEFAULT_SEARCH_PER_PAGE_MAX, 'per_page'), required=False, location='args')
        args = qs_parser.parse_args()
        q = args.q
        page = 1 if args.page is None else args.page
        per_page = args.per_page if args.per_page else constants.DEFAULT_SEARCH_PER_PAGE_MIN

        # Search from Elasticsearch
        query = {
            'from': (page-1)*per_page,
            'size': per_page,
            '_source': False,
            'query': {
                'bool': {
                    'must': [
                        {'match': {'_all': q}}
                    ],
                    'filter': [
                        {'term': {'status': 2}}
                    ]
                }
            }
        }
        ret = current_app.es.search(index='articles', doc_type='article', body=query)

        total_count = ret['hits']['total']

        results = []

        hits = ret['hits']['hits']
        for result in hits:
            article_id = int(result['_id'])
            article = cache_article.ArticleInfoCache(article_id).get()
            if article:
                results.append(article)

        # Record user search history
        if g.user_id and page == 1:
            try:
                cache_user.UserSearchingHistoryStorage(g.user_id).save(q)
            except RedisError as e:
                current_app.logger.error(e)

        return {'total_count': total_count, 'page': page, 'per_page': per_page, 'results': results}
```

在toutiao-backend/toutiao/resources/search目录中新建constants.py

```python
# 搜索结果分页默认每页数量 下限
DEFAULT_SEARCH_PER_PAGE_MIN = 10

# 搜索结果页默认每页数量 上限
DEFAULT_SEARCH_PER_PAGE_MAX = 50
```

### 添加ES新文章索引数据

在自媒体平台发布文章接口中，除了保存文章外，还要向es库中添加新文章的索引

```python
doc = {
          'article_id': article.id,
          'user_id': article.user_id,
          'title': article.title,
          'content': article.content.content,
          'status': article.status,
          'create_time': article.ctime
      }
current_app.es.index(index='articles', doc_type='article', body=doc, id=article.id)
```

