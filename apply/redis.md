# Redis笔记

## redis快的原理

redis快的原因:  
1\)基于内存  
2\)数据结构简单  
3\)采用单线程  
4\)多路i/o复用

## redis的持久化方案

redis的持久化方案有两种:

* RDB 持久化可以在指定的时间间隔内生成数据集的时间点快照（point-in-time snapshot）; 
* AOF 持久化记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集。 AOF 文件中的命令全部以 Redis 协议的格式来保存，新命令会被追加到文件的末尾。 Redis 还可以在后台对 AOF 文件进行重写（rewrite），使得 AOF 文件的体积不会超出保存数据集状态所需的实际大小。

## redis删除过期字段

* 在get时检查是否过期；
* 定期清理，采用最近最久未使用算法。
* 当内存不足时，主动清理

## redis的分布式解决方案

### Redis Cluster

由Redis官网推出，可线性扩展到1000个节点；采用无中心架构；通过一致性哈希思想来进行数据的分配；客户端直连redis服务，免去了代理的损耗

Redis Cluster采用哈希槽\(Hash slot\)，Key通过crc16算法获得一个值，然后通过crc16\(key\)/18384获取到被分到16384个槽，再根据槽与redis server的映射关系，存储到对应的server中

redis集群官方链接: [https://redis.io/topics/cluster-spec](https://redis.io/topics/cluster-spec)

### codis

cosdis为豌豆荚开源的proxy-based的redis集群方案，支持透明的扩/缩容，运维友好同类产品中最全面的redis命令支持，GUI监控界面和管理工具

codis也是采用哈希槽的方式，通过crc32算法来分配1024个槽

与redis cluster比，codis功能更加丰富，更加强大

**Codis模块简介**

* Codis Server
  * 基于 redis-2.8.21 分支开发。增加了额外的数据结构，以支持 slot 有关的操作以及数据迁移指令。
* Codis Proxy
  * 客户端连接的 Redis 代理服务, 实现了 Redis 协议。
  * 对于同一个业务集群而言，可以同时部署多个 codis-proxy 实例
  * 不同 codis-proxy 之间由 codis-dashboard 保证状态同步。
* Codis Dashboard
  * 集群管理工具，支持 codis-proxy、codis-server 的添加、删除，以及据迁移等操作。
  * 所有对集群的修改都必须通过 codis-dashboard 完成。
* Codis Admin
  * 集群管理的命令行工具。
  * 可用于控制 codis-proxy、codis-dashboard 状态以及访问外部存储。
* Codis FE
  * 集群管理界面。
* Codis HA
  * 为集群提供高可用。
  * 依赖 codis-dashboard 实例，自动抓取集群各个组件的状态；
  * 会根据当前集群状态自动生成主从切换策略，并在需要时通过 codis-dashboard 完成主从切换。
* Storage
  * 为集群状态提供外部存储。
  * 目前仅提供了 Zookeeper 和 Etcd 两种实现，但是提供了抽象的 interface 可自行扩展。

**Codis主从切换**

* Codis-HA：自动切换主从的工具。
* 通过RESTful API从 codis-dashboard 中拉取集群状态
* 默认以 5s 为周期
* 该工具会在检测到 master 挂掉的时候主动应用主从切换策略，向codis-dashboard发送主从切换命令。
* 对自动主从切换的支持比较弱
  * 主从切换的限制较多，必须在系统其他组件状态健康，且距离上次主从同步数据在一定时间间隔内才可以执行。
  * 多slave场景，提升其中的一个slave为master，其他的slave仍然会从旧的master同步数据，需要管理员手动操作。

**Codis数据迁移流程**

* 前提：Codis单条数据迁移是原子操作
* 单条数据迁移过程：
  * 随机选取指定 slot 中的一个 &lt;K,V&gt;
  * 将这个&lt;K,V&gt;传输给另外一个 codis-server
  * 传输成功后，把本地的这个 &lt;K,V&gt;删除

