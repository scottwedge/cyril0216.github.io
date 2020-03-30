# 定时修正统计数据

在`toutiao-backend/toutiao/__init__.py`中添加APScheduler调度器对象

```python
from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.executors.pool import ThreadPoolExecutor

def create_app(config, enable_config_file=False):
    ...
    
    # 添加定时任务APScheduler
    executors = {
        'default': ThreadPoolExecutor(10)
    }
    app.scheduler = BackgroundScheduler(executors=executors)

    from .schedule.statistic import fix_statistics
    
    # 每天3点执行
    app.scheduler.add_job(fix_statistics, 'cron', hour=3, args=[app])
    # 立即执行，用于测试
    # app.scheduler.add_job(fix_statistics, 'date', args=[app])

    app.scheduler.start()

    ...
```

在`toutiao-backend/toutiao`中新建schedule目录用于存放定时任务

toutiao-backend/toutiao/schedule/statistics.py

```python
from cache import statistic as cache_statistic

def fix_process(count_storage_cls):
    """
    修复处理方法
    """
    # 进行数据库查询
    ret = count_storage_cls.db_query()
    # 设置redis数据
    count_storage_cls.reset(ret)

def fix_statistics(flask_app):
    """
    修正统计数据
    """
    with flask_app.app_context():
        fix_process(cache_statistic.UserArticlesCountStorage)
        fix_process(cache_statistic.UserFollowingsCountStorage)
```

common/cache/statistic.py

```python
class CountStorageBase(object):
    """
    统计数量存储的父类
    """
    ...
    
    @classmethod
    def reset(cls, db_query_ret):
        """
        由定时任务调用的重置数据方法
        """
        # 设置redis的存储记录
        pl = current_app.redis_master.pipeline()
        pl.delete(cls.key)

        # zadd(key, score1, val1, score2, val2, ...)
        # 方式一
        # for data_id, count in db_query_ret:
        #     pl.zadd(cls.key, count, data_id)

        # 方式二
        redis_data = []
        for data_id, count in db_query_ret:
            redis_data.append(count)
            redis_data.append(data_id)

        # redis_data = [count1, data_id1, count2, data_id2, ..]
        pl.zadd(cls.key, *redis_data)
        # pl.zadd(cls.key, count1, data_id1, count2, data_id2, ..]

        pl.execute()

class UserArticlesCountStorage(CountStorageBase):
    """
    用户文章数量
    """
    key = 'count:user:arts'

    @staticmethod
    def db_query():
        ret = db.session.query(Article.user_id, func.count(Article.id)) \
            .filter(Article.status == Article.STATUS.APPROVED).group_by(Article.user_id).all()
        return ret
    

class UserFollowingsCountStorage(CountStorageBase):
    """
    用户关注数量
    """
    key = 'count:user:followings'

    @staticmethod
    def db_query():
        ret = db.session.query(Relation.user_id, func.count(Relation.target_user_id)) \
            .filter(Relation.relation == Relation.RELATION.FOLLOW)\
            .group_by(Relation.user_id).all()
        return ret
```

