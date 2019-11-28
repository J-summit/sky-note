## 1.sql查询执行过程

项目中我们平时curd中主要是在于查，

__客户端-->连接器（连接管理，权限验证）-->分析器（如果查询缓存中存在直接返回结果）-->优化器-->执行器-->存储引擎__

从mysql基础架构上看：Mysql主要划分为__Server层和存储引擎层__

存储引擎:

myisam(不支持事务，只有表锁，注重性能)

innodb(支持事务,有行锁和表锁)

memory（内存）

## 2.sql更新过程

当mysql收到更新通知时，不会立即写到磁盘中，这样非常占IO，所有Innodb引擎会先把记录写到__redo log__里面，并更新内存，这个更新已经算完成了。存储引擎会在适当的时候，将这个记录更新到磁盘中（空闲时）;

而redo log大小时固定的，写到末尾从头开始写，所以它会有两个点记录write pos记录当前位置，checkpoint擦除的位置，保证数据能完整的写入磁盘中；

__bin log__ mysql server的日志模块，可以用于恢复数据




