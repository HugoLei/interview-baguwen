# redis 内存淘汰
内存不够用时交换到磁盘

## 不淘汰，内存满了就直接报错
## 淘汰
### 针对设置过期时间的key淘汰
1. random
2. ttl：优先淘汰更早过期的key
3. lru：最久未使用的key
4. lfu：最少使用的key
### 针对所有数据淘汰
1. random
2. lru
3. lfu