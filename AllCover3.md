- [数据库SQL学习](#数据库SQL学习)
  - [存储引擎myisam和innodb](#存储引擎myisam和innodb)
  - [mysql索引](#mysql索引)
  - [事务](#事务)
  - [大表优化](#大表优化)
  - [Mysql底层实现过程](#Mysql底层实现过程)
  - [数据库SQL学习](#数据库SQL学习)
  - [数据库SQL学习](#数据库SQL学习)
  - [数据库SQL学习](#数据库SQL学习)
  - [数据库SQL学习](#数据库SQL学习)
  - [数据库SQL学习](#数据库SQL学习)



# 数据库SQL学习
## 存储引擎myisam和innodb
> myisam是mysql 5.5之前的默认引擎，后来被Innodb代替为默认引擎。

> 当你不需要处理高并发、不要求事务处理，不讲求事务崩溃后的数据安全问题那么就用myisam,它处理速度快。innnodb：支持事务、支持行级锁，有处理事务崩溃的ACID

## mysql索引
> mysql 查询多为单条查询时用哈希索引，否则用b+树索引。

> myisam的b+树data域存储地址，地址里面存值，得去地址找值这叫“非聚簇索引”，地址散，值散，谈何聚；
innodb的data域存储主键的值（key），叶子结点才存值，叶子结点有所有的值卫星数据这叫聚簇索引、主索引，
data域那些叫辅助索引，查询时查到辅助索引再跳到主索引里找值。

## 事务
> 不可重复读强调的是同一个事务的两次相同读操作，因为中途被另一事物的写给中断了，导致了两次读的结果不一样。确实感觉破坏了事务的原子性

> 前面说到mysql 的 innodb存储引擎支持行级锁，行级锁有三：Record/Gap/Next key Lock;mysql默认事务隔离级别是可重复读，就是因为用了Next key lock算法
弥补了幻读的缺陷。这个算法综合了前两种算法，实现了可锁，还锁了一个范围，如给 8 for update时索引范围可能是（5,8]，（8,11]，那么5~11的插入就会被阻塞。

## 大表优化
> 限制只能看一个月的订单；读写分离？；垂直分区，拆成多个表；水平分区，把列拆分，可以存很多数据，但是这样分片事务操作会有逻辑上的复杂。

## Mysql底层实现过程
> 连接器记录你的用户登录和得到你的访问权限——》分析器分析词法语法——》查找缓存或是进入优化器为你智能选择索引策略——》之后执行器执行命令去存储引擎里去数据

> 存储引擎Innodb他们都是插件式的引擎，所以有些功能得写成两个模块合作。mysql都有binlog(归档日志），Innodb有自己的redolog(重做日志，用于系统崩溃恢复数据）     插件式所以两个模块，日志执行顺序要考虑服务器挂了的情况：先归档恢复时归档日志有记录但是没有重做日志来恢复数据，数据不一致；先重做恢复时数据不管归没归档都恢复，数据不一致；所以才有了重做还要处于预提交的状态，归档之后在改预提交为提交（逻辑是希望恢复数据时能有归档记录——》没有归档就不要恢复数据）

![Mysql底层实现过程](https://mmbiz.qpic.cn/mmbiz_jpg/iaIdQfEric9TzWuuhjqx58LnibzsWR0Pf8x9nVefLe59Q8SBNcZGIGn1VGNFfNUVQyOwQksDoyvIOUJicgzU6ICVLg/640?wx_fmt=jpeg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)
## 一条sql语句 执行慢的可能原因
> 偶尔慢： 刷新脏页的等待（redolog从预提交到提交的等待过程和查询的数据页不在内存时，内存不足等待它淘汰一些数据页）  别人先拿到锁了 

> 一直慢： 字段没有用索引就得全局扫描   能走主键索引就走，非主键索引查到的是主键的值还得再来一遍主键索引   数据库可能抽样过程中计算出错误基数而选错不使用索引

## 数据库命令规范的重要性
> 某一列的列类型和关联列的列类型不一致就会隐式转换，影响查询速度 

> 临时库表必须以 tmp_为前缀并以日期为后缀，备份表必须以 bak_为前缀并以日期 (时间戳) 为后缀?

> [规范可以提高性能](<https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485117&idx=1&sn=92361755b7c3de488b415ec4c5f46d73&chksm=cea24976f9d5c060babe50c3747616cce63df5d50947903a262704988143c2eeb4069ae45420&token=79317275&lang=zh_CN#rd>)

## 关于锁
### X/S锁和行级锁、表级锁的关系  意向锁有什么用，它的兼容性为什么是那样
> 读写锁可以是行级锁也可以是表级锁 意向锁的作用是例如给多行数据加了行级锁X，没有IX意向锁他就得遍历一遍只有才知道能不能写，有了IX表级意向锁，就可以知道有没有人在这所有行级锁加上哪怕一个写锁。 意向锁不是真的锁，是表示有意向去加锁，所以彼此之间可以兼容
### 锁协议
> 一级锁协议用X锁解决丢失修改 二级锁协议用S锁，读完事务没完就放解决读脏数据 三级锁协议S锁直到事务结束才放来解决不可重复读