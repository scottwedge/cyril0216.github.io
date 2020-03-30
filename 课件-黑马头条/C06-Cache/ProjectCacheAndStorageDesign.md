# 头条项目缓存与存储设计

## 缓存设计

### 1 User Cache

用户资料

| key                    | 类型   | 说明                                            | 举例 |
| ---------------------- | ------ | ----------------------------------------------- | ---- |
| user:{user_id}:profile | string | user_id用户的数据缓存，包括手机号、用户名、头像 |      |

用户扩展资料 

| key                     | 类型   | 说明                   | 举例 |
| ----------------------- | ------ | ---------------------- | ---- |
| user:{user_id}:profilex | string | user_id用户的性别 生日 |      |

用户状态

| key                   | 类型   | 说明                | 举例 |
| --------------------- | ------ | ------------------- | ---- |
| user:{user_id}:status | string | user_id用户是否可用 |      |


| key                      | 类型 | 说明                   | 举例                     |
| ------------------------ | ---- | ---------------------- | ------------------------ |
| user:{user_id}:following | zset | user_id的关注用户      | [{user_id, update_time}] |

| key                 | 类型 | 说明                   | 举例                     |
| ------------------- | ---- | ---------------------- | ------------------------ |
| user:{user_id}:fans | zset | user_id的粉丝用户      | [{user_id, update_time}] |

| key                | 类型 | 说明                   | 举例                        |
| ------------------ | ---- | ---------------------- | --------------------------- |
| user:{user_id}:art | zset | user_id的文章          | [{article_id, create_time}] |

### 2 Comment Cache

| key                     | 类型   | 说明                                         | 举例                           |
| ----------------------- | ------ | -------------------------------------------- | ------------------------------ |
| art:{article_id}:comm   | zset   | article_id文章的评论数据缓存，值为comment_id | [{comment_id,  create_time}]   |
| comm:{comment_id}:reply | zset   | comment_id评论的评论数据缓存，值为comment_id | [{'comment_id',  create_time}] |
| comm:{comment_id}       | string | 缓存的评论数据                               |                                |

### 3 Article Cache

| key                     | 类型   | 说明           | 举例                     |
| ----------------------- | ------ | -------------- | ------------------------ |
| ch:all                  | string | 所有频道       |                          |
| user:{user_id}:ch       | string | 用户频道       |                          |
| ch:{channel_id}:art:top | zset   | 置顶文章       | [{article_id, sequence}] |
| art:{article_id}:info   | string | 文章的基本信息 |                          |
| art:{article_id}:detail | string | 文章的内容     |                          |

### 4 Announcement Cache

| key                        | 类型   | 说明 | 举例                             |
| -------------------------- | ------ | ---- | -------------------------------- |
| announce                   | zset   |      | [{'json data', announcement_id}] |
| announce:{announcement_id} | string |      | 'json data'                      |

## 持久存储设计

### 1 阅读历史

| key                        | 类型 | 说明 | 举例                       |
| -------------------------- | ---- | ---- | -------------------------- |
| user:{user_id}:his:reading | zset |      | [{article_id,  read_time}] |

### 2 搜索历史

| key                          | 类型 | 说明 | 举例                      |
| ---------------------------- | ---- | ---- | ------------------------- |
| user:{user_id}:his:searching | zset |      | [{keyword,  search_time}] |

### 3 统计数据

| key                  | 类型 | 说明             | 举例                   |
| -------------------- | ---- | ---------------- | ---------------------- |
| count:art:reading    | zset | 文章阅读数量     | [{article_id,  count}] |
| count:user:arts      | zset | 用户发表文章数量 | [{user_id,  count}]    |
| count:art:collecting | zset | 文章收藏数量     | [{article_id,  count}] |
| count:art:liking     | zset | 文章点赞数量     | [{article_id,  count}] |
| count:art:comm       | zset | 文章评论数量     | [{article_id,  count}] |


