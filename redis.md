# redis

## redis命令

### redis通用命令

keys：查看符合模板的所有key（查询时间较长、且redis单线程、不建议生产环境下使用）

del：删除一个指定的key

exists：删除一个指定的key

expire：给一个key设置有效期，到期删除该key

ttl：查看一个key的剩余有效期（-2表示已经不存在，-1表示永久存在）

### String类型常用命令

set：添加或修改已经存在的一个string类型的键值对

get：根据key获取string类型的value

mset：批量添加多个string类型的键值对

mget：根据多个key获取多个string类型的value

incr：让一个整形的key自增1

incrby：让一个整形的key自增并指定步长

incrbyfloat：让一个浮点型数字自增并指定步长

setnx：添加一个string类型的键值对，若该key已存在则不执行

setex：添加一个string类型的键值对，并指定有效期

### Hash类型的常见命令

hset key field value：添加或修改hash类型的key的field的值

hget key field：获取一个hash类型key的field的值

hmset：批量添加多个hash类型key的field的值（会覆盖）

hmget：批量获取多个hash类型key的field的值

hgetall：获取一个hash类型的key中所有的field和value

hkeys：获取一个hash类型的key中所有的field

hvals：获取一个hash类型的key中所有的value

hincrby：让一个hash类型key的字段值自增并指定步长 

hsetnx：添加一个hash类型的key的field值，前提是这个field不存在，否则不执行（只新增）

### List类型的常见命令

lpush key element：向列表左侧插入一个或多个元素

lpop key：移除并返回列表左侧的第一个元素，没有则返回nil

rpush key element：向列表右侧插入一个或多个元素

rpop key：移除并返回列表右侧的第一个元素t

lrange key start end：返回一段角标范围内的所有元素

blpop和brpop：与lpop和rpop类似，但是在没有元素时会等待指定时间（若中途插入，则获取到元素并返回）

### Set类型的常见命令

sadd key member：向set中添加一个或多个元素

srem key member：移除set中的指定元素

scard key：返回set中元素的个数

sismember key member：判断一个元素是否存在于set中

smembers key：获取该key中的所有元素

sinter key1 key2：求key1和key2的交集

sdiff key1 key2：求key1和key2的差集

sunion key1 key2：求key1和key2的并集

### SortedSet类型的常见命令

zadd key score member：添加一个或多个元素到sortedset，若已存在则更新score值

zrem key member：删除sortedset中的一个指定元素

zscore key member：获取sortedset中的指定元素的score值

zrank key member：获取sortedset中的指定元素的排名

zcard key：获取sortedset中的元素的个数

zcount key min max：统计score值在给定范围内的所有元素的个数

zincrby key increment member：让sortedset中的指定元素自增，步长为指定的increment值

zrange key min max：按照score排序后，获取指定排名范围内的元素

zrangebyscore key min max：按照score排序后，获取指定score范围内的元素

zdiff、zinter、zunion：求差集、交集、并集

注意：所有排名都为升序，若需降序则在z后加rev