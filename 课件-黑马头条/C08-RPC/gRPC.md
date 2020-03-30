# gRPC

# 简介

- gRPC是由Google公司开源的高性能RPC框架。

- gRPC支持多语言

    gRPC原生使用C、Java、Go进行了三种实现，而C语言实现的版本进行封装后又支持C++、C#、Node、ObjC、 Python、Ruby、PHP等开发语言

- gRPC支持多平台

    支持的平台包括：Linux、Android、iOS、MacOS、Windows

- gRPC的消息协议使用Google自家开源的Protocol Buffers协议机制（proto3）  序列化

- gRPC的传输使用HTTP/2标准，支持双向流和连接多路复用

![grpc](/images/grpc.png)

## 架构

C语言实现的gRPC支持多语言，其架构如下

![grpc架构](/images/grpc架构.png)

## 使用方法

安装

```shell
pip install grpc
pip install grpcio-tools
```

使用

1. 使用Protocol Buffers（proto3）的IDL接口定义语言定义接口服务，编写在文本文件（以`.proto`为后缀名）中。
2. 使用protobuf编译器生成服务器和客户端使用的stub代码
3. 编写补充服务器和客户端逻辑代码