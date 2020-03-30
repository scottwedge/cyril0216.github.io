# 头条首页新闻推荐接口编写

在toutiao-backend/toutiao/resources/news/article.py中编写

```python
from rpc import reco_pb2, reco_pb2_grpc

class ArticleListResource(Resource):
    """
    获取推荐文章列表数据
    """
    def _feed_articles(self, channel_id, timestamp, feed_count):
        """
        获取推荐文章
        :param channel_id: 频道id
        :param feed_count: 推荐数量
        :param timestamp: 时间戳
        :return: [{article_id, trace_params}, ...], timestamp
        """
        user_request = reco_pb2.UserRequest()
        user_request.user_id = g.user_id or 'annoy'
        user_request.channel_id = channel_id
        user_request.article_num = feed_count
        user_request.time_stamp = round(time.time() * 1000)

        stub = reco_pb2_grpc.UserRecommendStub(current_app.rpc_reco)
        ret = stub.user_recommend(user_request)
        return ret.recommends, ret.time_stamp

    def get(self):
        """
        获取文章列表
        """
        qs_parser = RequestParser()
        qs_parser.add_argument('channel_id', type=parser.channel_id, required=True, location='args')
        qs_parser.add_argument('timestamp', type=inputs.positive, required=True, location='args')
        args = qs_parser.parse_args()
        channel_id = args.channel_id
        timestamp = args.timestamp
        per_page = constants.DEFAULT_ARTICLE_PER_PAGE_MIN
        try:
            feed_time = time.strftime('%Y-%m-%dT%H:%M:%S', time.localtime(time.time()))
        except Exception:
            return {'message': 'timestamp param error'}, 400

        results = []

        # 获取推荐文章列表
        feeds, pre_timestamp = self._feed_articles(channel_id, timestamp, per_page)

        # 查询文章
        for feed in feeds:
            article = cache_article.ArticleInfoCache(feed.article_id).get()
            if article:
                article['pubdate'] = feed_time
                article['trace'] = {
                    'click': feed.track.click,
                    'collect': feed.track.collect,
                    'share': feed.track.share,
                    'read': feed.track.read
                }
                results.append(article)

        return {'pre_timestamp': pre_timestamp, 'results': results}

```

