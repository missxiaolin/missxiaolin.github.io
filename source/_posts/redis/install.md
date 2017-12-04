---
title: redis命令大全
date: 2017-12-04 15:30:46
categories: ["PHP"]
tags: 'php'
---

### redis 命令大全

| 备注 |  | 命令 |
| ------ || ------ |
| 设置密码 || CONFIG set requirepass "xiaolin" |
| 使用redis || AUTH xiaolin |
| 清楚所有key || FLUSHALL |
| 删除key || del |
| 检查key是否存在 || exists |
| 取出所有key || keys * |
| 过期时间 || expire key seconds |
| 查看key剩余过期时间 || ttl key |
| 去掉key过期时间 || persist key |
| 返回key类型 || type  key (tring、hash、list、set、zset、none) |

### 字符串

| 备注 |  | 命令 |
| ------ || ------ |
| key自增1 || incr key  |
| key自减1 || decr key  |
| key自增k || incrby key k  |
| key自减k || decrby key k  |
| 字符串设置 || set key value  |
| 字符串获取 || get key  |
| 设置新值返回旧值 || getset key value  |
| 将value追加到旧的value || append key value  |
| 返回字符串长度 || strlen key  |

### 哈希

| 备注 |  | 命令 |
| ------ || ------ |
| 获取hash key 对应的value || hget key field  |
| 设置hash key 对应的value || hset key field value  |
| 获取hash key 对应的key value || hgetall key |
| 获取hash key 对应的field 的value || hvals key |
| 获取hash key 对应所有的field || hkeys key |
| 删除对应的hash key || hdel key field |
| 判断hash key 是否存在field || hexists key field |
| 获取hash key 数量 || hlen key |
| 批量获取hash key 一批field对应的值 ||  hmget key field1 field2 |
| 批量设置hash key 一批field对应的值 ||  hmset key field1 field2 |

### 列表 list

| 备注 |  | 命令 |
| ------ || ------ |
| 从列表右端插入值 || rpush key value1 value2  |
| 从列表左端插入值 || lpush key value1 value2  |
| 在list指定的值前、后插入newValue || linsert key before、after value2 newValue |
| 从左边弹出一个元素 || lpop key |
| 从右边边弹出一个元素 || rpop key |
| 按照索引范围修建列表 || ltrim key start end |
| 获取列表指定范围内所有item || lrange key start end |
| 获取列表指定索引的item || lindex key index |
| 获取列表长度 || llen key |
| 设定列表指定索引值为newValue || lset key index newValue |
| lpop阻塞版本，timeeout是阻塞超时时间，timeout=0为永不阻塞 || blpop key timeout |
| brpop阻塞版本，timeeout是阻塞超时时间，timeout=0为永不阻塞 || brpop key timeout |

### 集合

| 备注 |  | 命令 |
| ------ || ------ |
| 向集合key添加element 如果存在则添加失败 || sadd key element |
| 将集合key中的element移除掉 || srem key element |
| 计算集合大小 || scard key |
| 判断是否在集合中 || sismember key value |
| 从集合中随机挑count个元素 || srandmember key count |
| 从集合中随机弹出一个元素 || spop key |
| 获取集合中所有元素 || smembers key |
| 差集 || sdiff key1 key2 |
| 交集 || sinter key1 key2 |
| 并集 || sunion key1 key2 |

### 有序集合

| 备注 |  | 命令 |
| ------ || ------ |
| 添加有序结合 || zadd key score element |
| 删除有序结合 || zrem key element |
| 返回元素分数 || zscore key element |
| 增加或减少分数 || zincrby key increScore element |
| 返回元素个数 || zcard key |
| 返回指定索引范围内的升序元素 || zrange key start end withscores |
| 返回指定分数范围内的升序元素 || zrangebyscore key minscore maxscore withscores |
| 返回有序集合内在指定分数范围内的个数 || zcount key minscore maxscore |
| 删除指定排名内的升序元素 || zremrangebyrank key start end |
| 删除指定分数内的升序元素 || zremrangebyscore key minscore maxscore |











