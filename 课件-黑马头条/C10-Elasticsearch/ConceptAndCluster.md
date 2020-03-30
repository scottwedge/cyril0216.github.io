# 概念与集群

## 概念

存储数据到 Elasticsearch 的行为叫做 *索引* （indexing）

关于数据的概念

```
Relational DB -> Databases 数据库 -> Tables 表 -> Rows 行 -> Columns 列
Elasticsearch -> Indices 索引库 -> Types 类型 -> Documents 文档 -> Fields 字段/属性
```

一个 Elasticsearch 集群可以 包含多个 *索引* （indices 数据库），相应的每个索引可以包含多个 *类型*（type 表） 。 这些不同的类型存储着多个 *文档*（document 数据行） ，每个文档又有 多个 *属性* （field 列）。

## Elasticsearch 集群（cluster）

Elasticsearch 尽可能地屏蔽了分布式系统的复杂性。这里列举了一些在后台自动执行的操作：

- 分配文档到不同的容器 或 *分片* 中，文档可以储存在一个或多个节点中
- 按集群节点来均衡分配这些分片，从而对索引和搜索过程进行负载均衡
- 复制每个分片以支持数据冗余，从而防止硬件故障导致的数据丢失
- 将集群中任一节点的请求路由到存有相关数据的节点
- 集群扩容时无缝整合新节点，重新分配分片以便从离群节点恢复

![elas_cluster](/images/elas_cluster.png)

#### 节点（node）

**一个运行中的 Elasticsearch 实例称为一个 节点**，而集群是由一个或者多个拥有相同 `cluster.name` 配置的节点组成， 它们共同承担数据和负载的压力。当有节点加入集群中或者从集群中移除节点时，集群将会重新平均分布所有的数据。

当一个节点被选举成为 ***主* 节点（master）时， 它将负责管理集群范围内的所有变更**，例如增加、删除索引，或者增加、删除节点等。 而**主节点并不需要涉及到文档级别的变更和搜索等操作**，所以当集群只拥有一个主节点的情况下，即使流量的增加它也不会成为瓶颈。 任何节点都可以成为主节点。我们的示例集群就只有一个节点，所以它同时也成为了主节点。

作为用户，**我们可以将请求发送到 *集群中的任何节点* ，包括主节点**。 每个节点都知道任意文档所处的位置，并且能够将我们的请求直接转发到存储我们所需文档的节点。 无论我们将请求发送到哪个节点，它都能负责从各个包含我们所需文档的节点收集回数据，并将最终结果返回給客户端。 Elasticsearch 对这一切的管理都是透明的。

#### 分片（shard）

一个 *分片* 是一个底层的 *工作单元* ，它仅保存了 全部数据中的一部分。

索引实际上是指向一个或者多个物理 *分片* 的 *逻辑命名空间* 。

文档被存储和索引到分片内，但是应用程序是直接与索引而不是与分片进行交互。

Elasticsearch 是利用分片将数据分发到集群内各处的。分片是数据的容器，文档保存在分片内，分片又被分配到集群内的各个节点里。 当你的集群规模扩大或者缩小时， Elasticsearch 会自动的在各节点中迁移分片，使得数据仍然均匀分布在集群里。

##### 主分片（primary shard）

索引内任意一个文档都归属于一个主分片，所以主分片的数目决定着索引能够保存的最大数据量。

##### 复制分片（副分片 replica shard)

一个副本分片只是一个主分片的拷贝。 副本分片作为硬件故障时保护数据不丢失的冗余备份，并为搜索和返回文档等读操作提供服务。

**在索引建立的时候就已经确定了主分片数，但是副本分片数可以随时修改.**

初始设置索引的分片方法

```http
PUT /blogs
{
   "settings" : {
      "number_of_shards" : 3,
      "number_of_replicas" : 1
   }
}
```

- number_of_shards

    每个索引的主分片数，默认值是 `5` 。这个配置在索引创建后不能修改。

- number_of_replicas

    每个主分片的副本数，默认值是 `1` 。对于活动的索引库，这个配置可以随时修改。

2 个节点

![elas_cluster](/images/elas_cluster.png)

3 个节点

![elas_cluster_node3](/images/elas_cluster_node3.png)

分片是一个功能完整的搜索引擎，它拥有使用一个节点上的所有资源的能力。 我们这个拥有6个分片（3个主分片和3个副本分片）的索引可以最大扩容到6个节点，每个节点上存在一个分片，并且每个分片拥有所在节点的全部资源。

修改复制分片数目的方法

```http
PUT /blogs/_settings
{
   "number_of_replicas" : 2
}
```

拥有越多的副本分片时，也将拥有越高的吞吐量。

![elas_0205](/images/elas_0205.png)

#### 故障转移 failover

![elas_0206](/images/elas_0206.png)

- 选举新的主节点
- 提升复制分片为主分片

#### 查看集群健康状态

```http
GET /_cluster/health

{
   "cluster_name":          "elasticsearch",
   "status":                "green", 
   "timed_out":             false,
   "number_of_nodes":       1,
   "number_of_data_nodes":  1,
   "active_primary_shards": 0,
   "active_shards":         0,
   "relocating_shards":     0,
   "initializing_shards":   0,
   "unassigned_shards":     0
}
```

`status` 字段指示着当前集群在总体上是否工作正常。它的三种颜色含义如下：

- `green`

    所有的主分片和副本分片都正常运行。

- `yellow`

    所有的主分片都正常运行，但不是所有的副本分片都正常运行。

- `red`

    有主分片没能正常运行。