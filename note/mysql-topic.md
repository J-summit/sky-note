## 1.索引失效

#### 单表查询时索引失效

1.mysql查询单表时，查询得到的结果集占数据总量很大比例，mysql会认为全表扫描会优于索引，则不走索引
好像查询数据量达到30%就不使用索引
例：比如企业人员信息表 （userInfo），字段（user_id、user_name、user_type（vachar）)，假设企业里有10w人，一千个管理层user_type为1，9万9千人为普通员工user_type为2，

2.查询时where条件后的字段类型要与表结构中该字段类型一致

例：select * from userInfo where user_type=2 ，user_type在表结构中时字符类型，查询时没用有单引号包含起来则不走索引

3.在where条件后对索引字段加了函数转换或者运算逻辑（+、-、*、/、！、<>、%、like'%_'（%放在前面）、or、in (疑问、可能存在成本问题)、exist等）的处理，比如对时间戳字段进行日期格式化函数都会引起索引失效
例:date_format()
使用in，场景适合的时候，可以使用union(去重) union all

4.如果某个数据列里包含着许多重复的值，就算为它建立了索引也不会有很好的效果

#### 多表关联查询时索引失效
在表结构设计阶段主表与关联表之间的关联字段的数据类型、数据长度、字段的编码格式以及字段的排序规则需要保持一致
#### 组合索引
当一张表的查询方式比较固定，这时候可以尝试创建聚集索引，查询时应当遵从组合索引的规则，最左原则，查询时使用最频繁的一列放在最左边

## 2.sql执行顺



## 序

1.  FROM 子句 组装来自不同数据源的数据
2.  WHERE 子句 基于指定的条件对记录进行筛选
3.  GROUP BY 子句 将数据划分为多个分组
4.  使用聚合函数进行计算
5.  使用HAVING子句筛选分组
6.  计算所有的表达式
7.  使用ORDER BY对结果集进行排序
8.  select 获取相应列
9.  limit截取结果集。