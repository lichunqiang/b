title: "Redis和Memcache的区别"
date: 2016-06-07 21:57:23
tags: [Redis, Memcache, NoSQL]
category: [Redis, Memcache, NoSQL]
---


1. Redis中，并不是所有的数据都一直存在内存中，这是和memcache的最大区别
2. Redis不仅仅支持简单的k/v类型的数据，同时还提供list, set, hash等数据结构
3. Redis支持数据备份，即master-slave模式的数据备份
4. Redis支持数据的持久化，可以将内存中的数据保存到磁盘上，服务器重启的时候可以再次加载。


Redis作者对Redis和memcache进行的比对：

没有必要过多的关心性能，因为二者的性能都已经足够高了。由于Redis使用单核，而memcache可以使用多核，所以在比较上，平均每一个核上Redis在存储小数据时比memcache性能更高。而在100k以上的数据中，memcache性能高于Redis，最终，无论使用哪一个，每秒处理请求的次数都不会成为瓶颈。(比如瓶颈可能会在网卡)

如果要说内存使用效率，使用简单的key-value存储的话，memcache的内存利用率更高，而如果Redis采用hash结构来做key-value存储，由于其组合式的压缩，其内存利用率会高于memcache。

如果你对数据持久化和数据同步有所要求，那么推荐你选择Redis，因为这两个特性Memcached都不具备。即使你只是希望在升级或者重启系统后缓存数据不会丢失，选择Redis也是明智的。


当然，最后还得说到你的具体应用需求。Redis相比Memcached来说，拥有更多的数据结构和并支持更丰富的数据操作，通常在Memcached里， 你需要将数据拿到客户端来进行类似的修改再set回去。这大大增加了网络IO的次数和数据体积。在Redis中，这些复杂的操作通常和一般的 GET/SET一样高效。所以，如果你需要缓存能够支持更复杂的结构和操作，那么Redis会是不错的选择。

1、 Redis和Memcache都是将数据存放在内存中，都是内存数据库。不过memcache还可用于缓存其他东西，例如图片、视频等等。
2、Redis不仅仅支持简单的k/v类型的数据，同时还提供list，set，hash等数据结构的存储。
3、虚拟内存--Redis当物理内存用完时，可以将一些很久没用到的value 交换到磁盘
4、过期策略--memcache在set时就指定，例如set key1 0 0 8,即永不过期。Redis可以通过例如expire 设定，例如expire name 10
5、分布式--设定memcache集群，利用magent做一主多从;redis可以做一主多从。都可以一主一从
6、存储数据安全--memcache挂掉后，数据没了；redis可以定期保存到磁盘（持久化）
7、灾难恢复--memcache挂掉后，数据不可恢复; redis数据丢失后可以通过aof恢复
8、Redis支持数据的备份，即master-slave模式的数据备份


参考：

* https://segmentfault.com/a/1190000004012181
* http://stackoverflow.com/questions/19424193/difference-between-memcache-memcached-and-redis
