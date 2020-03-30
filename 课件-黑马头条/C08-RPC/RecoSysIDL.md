# 推荐系统接口定义

### 接口原型

接口名称： user_recommend

调用参数：

```python
UserRequest:
    user_id       # 用户id
    channel_id    # 频道id
    article_num   # 推荐的文章数量
    time_stamp    # 推荐的时间戳
```

返回数据：

```python
ArticleResponse:
	expousre	     # 曝光埋点数据
    time_stamp       # 推荐的时间戳
    recommends:      # 推荐结果
        article_id   # 文章id
        track:		 # 关于文章的埋点数据
        	click    # 用户点击行为的埋点参数
            collect  # 用户收藏的埋点参数
            share    # 用户分享的埋点参数
            read     # 用户进入文章详情的埋点参数
```

### 使用Protobuf 定义的接口如下

**使用protobuf定义的接口文件通常以proto作为文件后缀名**

在toutiao-backend/common/rpc目录下新建reco.proto文件

```protobuf
syntax = "proto3";

message UserRequest {
    string user_id=1;
    int32 channel_id=2;
    int32 article_num=3;
    int64 time_stamp=4;
}

message Track {
    string click=1;
    string collect=2;
    string share=3;
    string read=4;
}

message Article {
    int64 article_id=1;
    Track track=2;
}

message ArticleResponse {
    string exposure=1;
    int64 time_stamp=2;
    repeated Article recommends=3;
}

service UserRecommend {
    rpc user_recommend(UserRequest) returns(ArticleResponse) {}
}

```

## 代码生成

编译生成代码

```shell
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. reco.proto
```

- `-I`表示搜索proto文件中被导入文件的目录
- `--python_out`表示保存生成Python文件的目录，生成的文件中包含接口定义中的数据类型
- `--grpc_python_out`表示保存生成Python文件的目录，生成的文件中包含接口定义中的服务类型

在toutiao-backend/common/rpc目录下执行上述命令，会自动生成如下两个rpc调用辅助代码模块：

* reco_pb2.py  保存根据接口定义文件中的数据类型生成的python类
* reco_pb2_grpc.py 保存根据接口定义文件中的服务方法类型生成的python调用RPC方法