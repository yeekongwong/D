# Redis-集群篇

---


## 1.主从+哨兵模式
> + 主从 - 主要为了降低每个redis服务器的负载，一个redis负责写数据，其他的负责读数据，主服务器的数据会自动同步给其他的从服务器。
> + 配置 redis.conf文件
    - #bind 127.0.0.1 服务器之间要保持连通
    - daemonize yes
    - protected-mode no
    - 对于从服务器的配置修改：slaveof 主服务ip 主服务端口
    - ./redis-cli / info replication(查看主从状态)
> + 实现原理：
    - salve第一次或者重连连接到master后，会向master发送一个SYNC的命令
    - master收到SYNC的时候会做两个事情
        - 执行bgsave（rdbk快照文件）
        - master会把新收的修改命令存入到缓存区
    - 为了避免读到脏数据，需要加上slave-serve-state-data yes的配置；保证必须完成同步之后，才可以对slave进行读取操作，因为slave会有历史数据
>    master
     /   \
  slave  slave
    |      |
  slave  slave
  
## 2.哨兵机制(仅仅是高可用，非高性能)
>+ 功能：
    - 监控master和slave是否正常运行
    - 若master出现故障，则会把其中一台slave升级为master
+ 配置：
    - sentinel monitor mymaster 127.0.0.1 6379 2[投票数]
    - sentinel down-after-millseconds mymaster 3000 表示多长时间就表明master挂了
    - ./redis-sentinel sentinel.conf
+ 故障转移的自动化
+ 两个概念：
    - 主观下线(sdwon)：指单个sentinel实例对服务器做出下线判断
    - 客观下线(odown)： 多个sentinel实例对同一个服务器做出下线判断，这时候才会故障转移
+ 哨兵集群：哨兵之间会相互监控，一般要求3个哨兵以上，且是奇数

## 3.集群模式(redis3.0之后的功能)(高性能)
> + 根据key的hash值取模服务器的数量？？？
+ 集群原理：
    - slot(槽)的概念：集群中默认有16384个槽
    - 根据key的CRC16算法，得到结果再对16384进行取模，假设有3个节点 
        - node1 0 5460
        - node2 5461 10922
        - node3 10921 16384
    - 当新增一个新节点后,会对槽重新分配，数据需要手动迁移(redis-trib.rb)
+ 市面上成熟集群方案：
    - redis shardding(一致性hash)，且jedis客户端支持shardding操作 SharddingJedis；
        - 在增加和减少节点的问题；preshardding 本在3台虚拟机上部署9个redis节点，一个虚拟机3个节点；可以增加到9个虚拟机来部署这9个节点
    - codis 豌豆荚(在应用层和客户端中做代理)
    - twemproxy(在应用层和客户端中做代理)
    



