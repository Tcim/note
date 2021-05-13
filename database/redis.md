
- [1 基础数据结构](#1-基础数据结构)
  - [1.1 String](#11-string)
  - [1.2 list](#12-list)
  - [1.3 hash](#13-hash)
  - [1.4 Set](#14-set)
  - [1.5 zset 有序列表](#15-zset-有序列表)
- [2 分布式锁](#2-分布式锁)
  - [2.1 获得锁](#21-获得锁)
  - [2.2 释放锁](#22-释放锁)
- [3 位图](#3-位图)
## 1 基础数据结构
### 1.1 String
字符串是基本数据类型, 所有数据结构都是以唯一的key字符串作为名称. **Redis的字符串是动态字符串, 可以修改, 采用预分配冗余空间的方式来减少内存空间频繁分配**. 扩容时每次增加1M, 最大长度为512M.

设置键值对:
```shell
# 设置和获取单个键值对
> set key value
> get key
# 批量键值对
> mset key1 value1 key2 value2
> mget key key2
```
键值对过期和set命令
```shell
> set key value
# 设置过期时间
> expire key 10 # 10s 后过期
# 等价于
> setex key 10 value
```
计数
如果value是一个整数, 可以对它进行自增操作.
```shell
> set key 30
# 自增
> incr key
# 加减运算
> incrby key 10
```
### 1.2 list
Redis的列表是以链表的存储的. 可以使用`lpush`, `rpush`插入数据, 使用`lpop`, `lpush`弹出数据. 基于此操作, 可以使用list灵活地表示队列或栈.

> list并不完全是以链表存储的, 在元素较少的情况下, redis会使用连续的存储空间, 构成一个`ziplist`, 这个`ziplist`内容连续, 成为压缩链表. 数据量变大时, 无法使用连续的内存空间, 此时redis会将`ziplist`使用双向指针组合成为`quicklist`.

- 右进左出队列
  ```shell
  > rpush key value1 value2     # 右边进队列
  > lpop key                    # 左边出队列(value1)
  ```
- 右进右出栈
  ```shell
  > rpush key value1 value2     # 右边进栈
  > rpop key                    # 右边出栈(value2)
  ```
list可以基于index来访问元素(`lindex key index`), 但由于list是用链表实现的, 这样的访问的时间复杂度为`O(n)`. 其它相关命令:
```shell
> lrange key index1 index2  # 获得两个索引间的子列表, O(n)
> ltrim key index1 index2   # 仅保留索引间列表, 删除两端子链表, O(n)
> llen key                  # 获得链表长度
```
### 1.3 hash
Redis的`hash`与Java的`HashMap`结构相似, 都是采用数组 + 链表存储结构. 但Redis中的hash的value只能是字符串. 在扩容rehash过程中, Redis采用的是渐进式扩容方式. 
![](https://i.loli.net/2021/05/11/kuCsMnoI1x3Y5i9.png)
扩容时并不阻塞线程, 同时保留两个结构. 查询时也会同时查询两个结构. 后期通过定时任务完成渐进式结构迭代.
```shell
> hset hash key1 value1     # hash 为字典名称
> hgetall hash              # 得到所有键值对
> hget hash key1            # 查询
> hlen hash                 # 大小
```
hash字典的单个键值也可以进行技术操作:
```shell
> hset hash key1 20     # hash.key1 = 20
> hincrby hash key1 5   # hash.key1 = 25
```
### 1.4 Set
Set(集合)是无序的, 集合中的元素保证是唯一的. 
```shell
> sadd set value1 value2    # set为集合名称
> smembers set              # 获得所有集合元素(无序)
> sismember set value1      # 查询是否存在于集合中
> scard set                 # 获得长度
> spop set [count]          # 弹出一个元素, 可选数量
```
### 1.5 zset 有序列表
zset是一种有序集合, 它保证集合中元素的唯一性. 同时允许给元素设置一个排序权重, 使集合中的元素是**逻辑有序**的, 其内部实现原理为**跳跃列表**.
```shell
> zadd zset score1 member1 score2 member2
> zrange zset startindex stopindex          # 对startindex-stopindex的有序元素输出
> zrevrange zset startindex stopindex       # 对startindex-stopindex的有序元素逆序输出
> zcard zset                                # 长度
> zrank zset member1                        # member1的排名
> zrangebyscore zset score_start score_stop [withscores]    # 排序权重区间元素, 可选同时返回score
> zrem zset member1                         # 删除元素
```
## 2 分布式锁
Redis可以实现分布式锁. 所谓分布式锁, 就是在分布式系统中的并发控制器. Java中多线程使用`RecurrentLock`等同步器来保证并发下数据的安全性, 这是一种本地锁, 对进程中的多线程起作用.

在分布式系统中, 同一个业务往往分布在多个机器上, 以承载高并发的业务访问. 分布式系统中存在的高并发安全问题不能用本地锁解决, 需要使用分布式锁来解决. 分布式锁通常使用Redis来实现.
### 2.1 获得锁
Redis中的命令`setnx`(set if not exists)可以在Redis中设置一个键值对, 但当key已存在的情况下, 则设置失败. 由于该命令下单个key在Redis中是独占的, 利用该机制, 我们可以实现分布式锁. 即客户端使用`setnx`命令获得独占锁. 在该客户端设置的key已经存在时, 其他客户端不能进行重复设置, 即不能获得该分布式锁.
```shell
> setnx lock true   # 设置分布式锁, lock为锁的名字, true为值(不具备实际含义, 可以任何值)
```
### 2.2 释放锁
释放锁有两种方式:
- 由客户端手动释放
  ```shell
  > del lock    # 删除键值对
  ```
- 超时自动释放锁
  ```shell
  > expire lock 5   #5秒后自动释放锁, 即设置Redis数据的过期时间
  ```
由于设置锁`setnx`和释放锁的命令并不是原子操作, 所以可能会导致死锁的情况出现. 由于`del`或`expire`命令执行异常, 使锁释放失败, 由此会导致死锁发生. 对于`expire`命令, Redis引入新的命令使分布式锁的操作具有原子性:
```shell
> set lock true ex 5 nx OK      # setnx 与 expire 组合的原子指令
```
> 超时问题: 对于设置锁的过期时间, 可能会因为执行同步操作时间太长, 而导致锁已经释放, 但同步操作还没有执行完的问题. 所以, 分布式锁不应用于较长时间的同步任务.

## 3 位图
