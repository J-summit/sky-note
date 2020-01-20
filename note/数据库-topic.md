## 1.mysql与oracle区别

```
1. mysql和oracle都是关系型数据库，可以应用于各种平台。我们用的oracle的版本是oracle11g ,用的mysql的版本是mysql5.5。mysql最开始是瑞典一个公司开发的，开源的，但是后来被sun公司收购，后来sun又被oracle收购，所以现在可以说mysql属于甲骨文公司了！现在用mysql的公司也有很多，mysql价钱便宜些，处理千万级别的数据不成问题的，并且开源，很友好！

2. mysql默认端口：3306 默认用户root，oracle默认端口 1521 默认用户system

3. mysql的安装卸载简单，oracle很麻烦，动不动就要害的大家重做系统（迷醉）

4. oracle在命令行用命令登陆：sqlplus---然后录入账号密码

   mysql在命令行用命令登陆： mysql -hlocalhost -uroot -p123123

5.  在初学阶段，图形化工具，oracle 一般用PLSQL ，mysql 一般用navicat。假如别的你用着习惯比如sqlyog小海豚啥的当然也没有问题。

6.  关于数据库的层次结构  ：oracle：创建一个数据库，数据库下有好多用户：sys,system,scott等，不同用户下还有好多表。我们自己练习一般就创建一个数据库用。mysql：默认用户是root，用户下可以创建好多数据库，每个数据库下还有好多表。我们一般自己练习就用默认用户，不会创建多个用户

7. 数据库中表字段的类型：

   oracle：number（数值型），varchar2,varchar,char (字符型)，date 日期型 等

   mysql：int,float,double等数值型，varchar,char字符型，date,datetime,time,year,timestamp等日期型。

   其中char(2)这样定义，这个单位在oracle中2代表两个字节，mysql中代表两个字符。

   其中varchar在mysql中 必须给长度例如varchar(10) 不然插入的时候出错。

8. 主键递增操作：

   oracle:可以借助序列

   mysql:利用自增 auto_increment

9. 单表sql语法
删除表：这里不同

oracle: delate from myuser ; ---from 可有可无

mysql: delete from myuser; ---必须有from

10. 

11. 

12. 分页：

    （1）oracle分页复杂：

    第2页:6-10条记录

    select * from

    (select rownum rr,a.* from (select * from emp order by sal) a )

    where rr>5 and rr<=10;

    （2）mysql分页简单一句话:

    select * from help_category order by parent_category_id limit 10,5

    10,5含义：

    记录从0开始，所以其实10代表的是第11条记录，从第11条记录开始，取5个数据
13.事务隔离级别
mysql默认可重复读
oracle默认读已提交
```