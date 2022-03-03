### Redis简介

数据库的cache功能并不够好，所以需要其他的cache来缓解数据库的压力。

如下是一个带缓存系统架构图：

![image-20220301211137742](https://gitee.com/cao_ziqiang/img/raw/master/20220301211137.png)

### memcache和redis



`memcache`代码层次类似与`hash`

区别主要在于：

- 前者只支持简单的数据类型，redis的数据类型更加丰富；
- 前者不支持数据持久化存储，一旦数据库宕机，数据将丢失，后者支持数据磁盘的持久化存储；
- 前者不支持主从同步，后者支持主从同步；
- 前者不支持分片（分片是指将数据分到若干个物理分区），后者支持分片；

简单的`key-value`存储可以选择`memcache`，`redis`适合有高级需求的系统；

### redis快的原因

- 完全基于内存，绝大部分请求都是纯粹的内存操作，执行效率高；

- 数据结构简单，对数据的操作也比较简单，不像mysql建立关系，只是通过`key-value`关联；

- 采用单线程，单线程也能处理高并发请求，想多核也可以启动多实例。redis的单线程主要指其主线程是单线程的，包括`I/O`的处理和请求时间的处理，还有包括过期键处理和复制处理，这些非`I/O`的任务被封装为了周期性的执行，对于客户端的所有请求，都由主线程串行处理，避免了上下文切换和锁竞争；

- 使用了多路`I/O`复用模型，非阻塞`I/O`；

### 多路复用I/O

FD：File Description，文件描述符；

一个打开的文件通过唯一的描述符进行引用，该描述符是打开文件的元数据到文件本身的映射，在Unix中，FD通过用一个整数表示；

传统的阻塞`I/O`模型：

![image-20220303190954152](https://gitee.com/cao_ziqiang/img/raw/master/20220303190954.png)

当应用程序使用`read`或`write`对某个文件描述符FD进行读写时，如果当前`FD`不可读或不可谢，那么应用程序就不会做出响应，也是在同步地阻塞等待数据被加载进用户态缓存。

在`I/O`多路复用中，引入了`select`这个系统调用，能够同时监控多个文件描述符的可读、可写状态描述，当其中的某些文件描述符变为可读可写情况时，`select`系统调用就会返回可读、可写的FD个数。

![image-20220303191242403](https://gitee.com/cao_ziqiang/img/raw/master/20220303191242.png)

在Linux中，还有其他的多路复用技术如`epoll`、`kqueue`、`evport`。

`redis`因为本身跨平台和高扩展性，在不同平台编译会选择不同的多路复用技术，提供给上层统一的接口；

`redis`优先选择时间复杂度为`O(1)`的`I/O`多路复用函数作为底层实现，如在`Solaries 10`采用`evport`，在Linux中采用`epoll`，在·`macOS`和`freeBSD`中使用`kqueue`。

以时间复杂度为`O(n)`的`select`作为保底；

![img](https://gitee.com/cao_ziqiang/img/raw/master/20220303194649.webp)

多路复用 I/O 模型的做法是，用一个线程将这一万个建立成功的链接陆续的放入 `event_poll`，`event_poll` 会为这一万个长连接注册回调函数，当某一个长连接准备就绪后（建立连接成功、数据读取完成等），就会通过回调函数写入到 `event_poll` 的就绪队列 `rdlist` 中，这样这个单线程就可以通过读取 `rdlist` 获取到需要的数据。

### redis的数据类型

String：最基本的数据类型，二进制安全，底层是`SDS`动态字符串，大小为512M。指令（`set`，`get`）

Hash：String元素组成的字典，适合用于存储对象。指令·（`hmset`，`hget`，`hset`）

List：列表，按照String元素的插入顺序排序，底层是双端队列，大约能存储40亿个成员。指令（`lpush`，`lpop`，`rpush`，`rpop`，`lrange`，`rrange`）

Set：String元素组成的无序集合，通过哈希表实现，不允许重复。指令（`sadd`，`smembers`）

SortSet：String元素组成的有序集合，通过分数来为集合中的成员进行从小到大的排序。指令(`zadd`，`zrangebyscore`)

此外，新版的redis中还引入了`HyperLogLog`，用于支持存储地理位置信息的`Geo`。

![image-20220303200820874](https://gitee.com/cao_ziqiang/img/raw/master/20220303200820.png)

### 从海量key中查询出某一固定前缀的key

一定要根据数据范围来回答。

`keys pattern`：查找所有符合给定模式`pattern`的`key`，但是`keys`指令一次性返回所有匹配的key，key数量过大会使服务卡顿。

可以使用`SCAN cursor [MATHCH pattern] [COUNT count]`指令进行匹配，这条指令是基于游标的迭代器，需要基于上一次的游标延续之前的迭代过程；

以0作为游标开始一次新的迭代，直到命令返回游标0完成一次遍历；

不保证每次执行都返回某个给定数量的元素，支持模糊查询；

一次返回的数量不可控，只能是大概率符合count参数；

### 分布式锁

`setnx`指令和`EXPIRE key`指令设置key的生存时间，当key过期时，会被自动删除；

![image-20220303211831762](https://gitee.com/cao_ziqiang/img/raw/master/20220303211831.png)

但是原子性得不到保证；

从redis2开始，就可以使用`set`操作将·`setnx`和`expire`相结合：

`set key value [ex seconds] [px milliseconds] [nx|xx]`

`ex seconds` 表示设置建的过期时间为seconds，后者为毫秒；

NX表示不存在创建，XX表示已经存在时创建；![image-20220303212725941](https://gitee.com/cao_ziqiang/img/raw/master/20220303212726.png)

### 大量key同时过期

redis中大量的key集中过期由于清除大量的key很耗时，会造成系统短暂性卡顿；

解决方案：在设置key的过期时间的时候，给每个key加上随机值；

