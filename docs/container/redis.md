# Redis
## nosql
- not only sql
- 四种结构
  - key-value: redis
  - bson: mongodb
  - graph: geo4j
  - 列存储: Hbase 
- CAP + BASE
  - 传统ACID 原子,一致,独立,持久
  - CAP 强一致,可用,分区容错
  - CAP 只能满足3种中的2种
- BASE
  - 基本可以
  - 软状态
  - 最终一致性
- 放松对一致性的要求换取高可用
## 特点
- 支持持久化
- 数据类型多样化 list, set, zset, hash
- 支持数据备份
## 基本操作
- redis-benchmark 批量测试
- select 0-15 切换数据库
- ps -ef|grep redis 查看redis 进程
- lsof -i :6379 查看6379端口
## 数据类型
- Key 键
  - DBSIZE 查询key的数量
  - EXPIRE key seconds 设置过期时间
  - EXISTS key 判断key是否存在
  - MOVE key db 移到某个库
  - ttl key 查看还有多少秒过期
  - type key 查看key是什么类型 
- String 字符串
  - INCR key key值+1
  - DECR key key值-1
  - GETRANGE key index1 index2 截取字符串
  - SETRANGE key index value 在index后设置value
  - SETEX key second 设置过期时间
  - SETNX key value 如果key不为null生效
  - MSET key1 val1 key2 val2 多行插入
  - MGET key1 key2 多行获取
  - MSETNX key1 val1 key2 val2 多行非空插入,有一个失败整体失败
- List 列表
  - LPUSH key val1 val2 val3 从左侧插入List
  - RPUSH key val1 val2 val3 从右侧插入List
  - LRANGE key index1 index2 查询脚标内的list 
  - LPOP key 从左出一个元素
  - RPOP key 从右出一个元素
  - LINDEX key 按脚标获取元素
  - LLEN key 集合的长度
  - LREM key n val 删除集合中指定数量的值
  - LTRIM key index1 index2 截取脚标间的元素
  - LPOPRPSH key1 targetKey 从key1去值放入targetKey
  - LINSERT key before/after val val2 在val值前/后插入val2
- Set 集合
  - SADD key val1 val2 val3 添加值
  - SMEMBERS key 列出元素
  - SISMEMBER key val1 查询val1是否在set里
  - SCARD key 获取集合元素数
  - SREM key value 删除集合元素 
  - SRANDMEMBER key num 随机出num个元素
  - SPOP key 随机出栈
  - SMOVE key1 key2 val 将key1中的val赋给key2
  - SDIFF key1 key2 差集
  - SINTER key1 key2 交集
  - SUNION key1 key2 并集
- Hash 哈希
  - HSET key vkey value  放入键值对
  - HGET key vkey 获取vkey的值
  - HMSET key vkey1 val1 vkey2 val2 批量放入数据
  - HMGET key vkey1 vkey2 vkey3 批量获取数据
  - HGETALL key 获取所有key value
  - HDEL key vkey 删除vkey
  - HLEN key key的数据藏毒
  - HEXISTS key vkey 查询vkey是否存在
  - HKEYS key 获取所有的vkey
  - HVALS key 获取所有的value
  - HINCRBY key vkey 自增vkey的val
  - HSETNX key vkey sec 不存在再插入
- Zset 有序集合
  - 在set基础上添加score值
  - ZADD key 10 val1 20 val2 添加ZSET
  - ZRANGE key index1 index2 显示脚标中的val值,不含score
  - ZRANGE key index1 index2 withscores 显示脚标中的值包含score
  - ZRANGEBYSCORE key score1 score2 显示2个score值中的数据
  - ZRANGEBYSCORE key score1 (score2 显示2个score中的数据不包含score2的数据
  - ZRANGEBYSCORE key score1 score2 limit index num 显示2个score中的值,从index脚标算起,显示num个
  - ZREM key val 删除val元素
  - ZCARD key 元素个数
  - ZCOUNT key score1 score2 两个score中的元素个数
  - ZRANK key val 获取元素脚标
  - ZSCORE key val 获取val对应的score值
  - ZREVRANK key val 反序val脚标
  - ZREVRANGE key index1 index2 显示反序集合中的元素
## 配置文件
  - config get dir 确认执行目录
  - config get requriepass 获取密码
  - auth password 输入密码
  - maxclient 最大连接客户端
  - maxmemory 最大内存
  - maxmemroy-policy 过期策略 默认Noeviction,不淘汰
  - daemonize yes 守护线程
  - pidfile 进程文件地址
  - port    绑定端口
  - bind    绑定ip
  - timeout 过期时间
  - loglevel 日志级别
  - logfile 日志地址
  - dir 本地数据库存放目录
  - requirepass redis密码
  
## 持久化复制
- rdb
  - redis datebase
  - 指定时间间隔将内存的数据集快照写入磁盘
  - 缺点:最后一次持久化之后的数据会丢失
  - fork: 复制一个进程,所有数据与原进程一致.
  - config文件中 save second changes rdb策略,seconds内发生changes次变化
  - save 立即备份
  - bgsave 立即备份,备份时redis可以写入
  - save "" 禁用备份
- aof
  - 以日志形式记录写操作
  - 只能新增无法改写
  - 启动的时候从头执行一遍
  - redis-check-aof
  - 上次重写的一倍大小且大小大于64MB触发重写
  - 缺点:文件大小,执行效率
  - 
## 事务
- 一个队里中顺序,一次性,排他性的执行指令
- MULTI 标记事务块开启
- EXEC 执行事务块中的命令
- DISCARD 取消事务
- WATCH key1 key2 监视key
- UNWATCH 取消监视
## 订阅和发布
- SUBSCRIBE 订阅
- PUBLISH channel 消息发布
- PSUBSCRIBE 批量订阅
## 主从复制
- 读写分离
- 容灾恢复
- INFO replication 查看redis机器状态
- 从库配置
  - slaveof masterIp masterPort
  - slaveof no one 从机变为主机
- 哨兵模式
  - sentinel.conf
  - sentinel monitor redisname ip host role 配置监控主机和上位规则
  - 
 