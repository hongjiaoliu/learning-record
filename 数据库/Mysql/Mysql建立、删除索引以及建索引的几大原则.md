> [转自：http://www.yuanrengu.com/index.php/2017-01-13.html](http://www.yuanrengu.com/index.php/2017-01-13.html)

### 索引的创建、删除

 索引的类型：
  + UNIQUE(唯一索引)：不可以出现相同的值，可以有NULL值
  + INDEX(普通索引)：允许出现相同的索引内容
  + PROMARY KEY(主键索引)：不允许出现相同的值
  + Fulltext index(全文索引)：可以针对值中的某个单词，但效率确实不敢恭维
  + 组合索引：实质上是将多个字段建到一个索引里，列值的组合必须唯一

温馨提示：根据《阿里巴巴Java开发手册》里的mysql规约，唯一索引建议命名为uk_字段名，普通索引名则为idx_字段名。（uk_即unique key; idx_即index的简称）。

1. 使用ALTER TABLE语句创建索引

```sql
ALTER TABLE 表名 ADD 索引类型 （unique,primary key,fulltext,index）[索引名]（字段名）

```

```sql
//普通索引
alter table table_name add index index_name (column_list) ;
//唯一索引
alter table table_name add unique (column_list) ;
//主键索引
alter table table_name add primary key (column_list) ;
```

ALTER TABLE可用于创建普通索引、UNIQUE索引和PRIMARY KEY索引3种索引格式，table_name是要增加索引的表名，column_list指出对哪些列进行索引，多列时各列之间用逗号分隔。索引名index_name可选，缺省时，MySQL将根据第一个索引列赋一个名称。另外，ALTER TABLE允许在单个语句中更改多个表，因此可以同时创建多个索引。

2. 使用CREATE INDEX语句对表增加索引

  CREATE INDEX可用于对表增加普通索引或UNIQUE索引，可用于建表时创建索引。


```sql
CREATE INDEX index_name ON table_name(username(length));
```  

如果是CHAR，VARCHAR类型，length可以小于字段实际长度；如果是BLOB和TEXT类型，必须指定 length。

```sql
//只能添加这两种索引;
CREATE INDEX index_name ON table_name (column_list)
CREATE UNIQUE INDEX index_name ON table_name (column_list)
```

table_name、index_name和column_list具有与ALTER TABLE语句中相同的含义，索引名不可选。另外，不能用CREATE INDEX语句创建PRIMARY KEY索引。

3. 删除索引
删除索引可以使用ALTER TABLE或DROP INDEX语句来实现。DROP INDEX可以在ALTER TABLE内部作为一条语句处理，其格式如下：

```sql
drop index index_name on table_name ;

alter table table_name drop index index_name ;

alter table table_name drop primary key ;
```
其中，在前面的两条语句中，都删除了table_name中的索引index_name。而在最后一条语句中，只在删除PRIMARY KEY索引中使用，因为一个表只可能有一个PRIMARY KEY索引，因此不需要指定索引名。如果没有创建PRIMARY KEY索引，但表具有一个或多个UNIQUE索引，则MySQL将删除第一个UNIQUE索引。

如果从表中删除某列，则索引会受影响。对于多列组合的索引，如果删除其中的某列，则该列也会从索引中删除。如果删除组成索引的所有列，则整个索引将被删除。

4. 组合索引与前缀索引

在这里要指出，组合索引和前缀索引是对建立索引技巧的一种称呼，并不是索引的类型。为了更好的表述清楚，建立一个demo表如下。

```sql
create table USER_DEMO
(
   ID                   int not null auto_increment comment '主键',
   LOGIN_NAME           varchar(100) not null comment '登录名',
   PASSWORD             varchar(100) not null comment '密码',
   CITY                 varchar(30) not null comment '城市',
   AGE                  int not null comment '年龄',
   SEX                  int not null comment '性别(0:女 1：男)',
   primary key (ID)
);
```
为了进一步榨取mysql的效率，就可以考虑建立组合索引，即将LOGIN_NAME,CITY,AGE建到一个索引里：

```sql
ALTER TABLE USER_DEMO ADD INDEX name_city_age (LOGIN_NAME(16),CITY,AGE);
```
建表时，LOGIN_NAME长度为100，这里用16，是因为一般情况下名字的长度不会超过16，这样会加快索引查询速度，还会减少索引文件的大小，提高INSERT，UPDATE的更新速度。

如果分别给LOGIN_NAME,CITY,AGE建立单列索引，让该表有3个单列索引，查询时和组合索引的效率是大不一样的，甚至远远低于我们的组合索引。虽然此时有三个索引，但mysql只能用到其中的那个它认为似乎是最有效率的单列索引，另外两个是用不到的，也就是说还是一个全表扫描的过程。

建立这样的组合索引，就相当于分别建立如下三种组合索引：

LOGIN_NAME,CITY,AGE
LOGIN_NAME,CITY
LOGIN_NAME

为什么没有CITY,AGE等这样的组合索引呢？这是因为mysql组合索引“最左前缀”的结果。简单的理解就是只从最左边的开始组合，并不是只要包含这三列的查询都会用到该组合索引。也就是说name_city_age(LOGIN_NAME(16),CITY,AGE)从左到右进行索引，如果没有左前索引，mysql不会执行索引查询。

如果索引列长度过长,这种列索引时将会产生很大的索引文件,不便于操作,可以使用前缀索引方式进行索引，前缀索引应该控制在一个合适的点,控制在0.31黄金值即可(大于这个值就可以创建)。

```sql
SELECT COUNT(DISTINCT(LEFT(`title`,10)))/COUNT(*) FROM Arctic; -- 这个值大于0.31就可以创建前缀索引,Distinct去重复

ALTER TABLE `user` ADD INDEX `uname`(title(10)); -- 增加前缀索引SQL,将人名的索引建立在10,这样可以减少索引文件大小,加快索引查询速度
```

### 索引的使用及注意事项

EXPLAIN可以帮助开发人员分析SQL问题,explain显示了mysql如何使用索引来处理select语句以及连接表,可以帮助选择更好的索引和写出更优化的查询语句。

使用方法,在select语句前加上Explain就可以了：

```sql
Explain select * from user where id=1;
```

尽量避免这些不走索引的sql：

```sql
SELECT `sname` FROM `stu` WHERE `age`+10=30;-- 不会使用索引,因为所有索引列参与了计算

SELECT `sname` FROM `stu` WHERE LEFT(`date`,4) <1990; -- 不会使用索引,因为使用了函数运算,原理与上面相同

SELECT * FROM `houdunwang` WHERE `uname` LIKE'后盾%' -- 走索引

SELECT * FROM `houdunwang` WHERE `uname` LIKE "%后盾%" -- 不走索引

-- 正则表达式不使用索引,这应该很好理解,所以为什么在SQL中很难看到regexp关键字的原因

-- 字符串与数字比较不使用索引;
CREATE TABLE `a` (`a` char(10));
EXPLAIN SELECT * FROM `a` WHERE `a`="1" -- 走索引
EXPLAIN SELECT * FROM `a` WHERE `a`=1 -- 不走索引

select * from dept where dname='xxx' or loc='xx' or deptno=45 --如果条件中有or,即使其中有条件带索引也不会使用。换言之,就是要求使用的所有字段,都必须建立索引, 我们建议大家尽量避免使用or 关键字

-- 如果mysql估计使用全表扫描要比使用索引快,则不使用索引
```
索引虽然好处很多，但过多的使用索引可能带来相反的问题，索引也是有缺点的：

+ 虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行INSERT,UPDATE和DELETE。因为更新表时，mysql不仅要保存数据，还要保存一下索引文件

+ 建立索引会占用磁盘空间的索引文件。一般情况这个问题不太严重，但如果你在要给大表上建了多种组合索引，索引文件会膨胀很宽

索引只是提高效率的一个方式，如果mysql有大数据量的表，就要花时间研究建立最优的索引，或优化查询语句。

### 使用索引时，有一些技巧

1. 索引不会包含有NULL的列
  只要列中包含有NULL值，都将不会被包含在索引中，复合索引中只要有一列含有NULL值，那么这一列对于此符合索引就是无效的。

2. 使用短索引
  对串列进行索引，如果可以就应该指定一个前缀长度。例如，如果有一个char（255）的列，如果在前10个或20个字符内，多数值是唯一的，那么就不要对整个列进行索引。短索引不仅可以提高查询速度而且可以节省磁盘空间和I/O操作。

3. 索引列排序
   mysql一张表查询只能用到一个索引。因此如果where子句中已经使用了索引的话，那么order by中的列是不会使用索引的。因此数据库默认排序可以符合要求的情况下不要使用排序操作，尽量不要包含多个列的排序，如果需要最好给这些列建复合索引。这一点是很多程序猿容易忽略的，如where子句的字段建了索引，排序的字段建了索引，但是分开建的，以为会走索引，其实这样的话排序的字段不会使用索引的，除非建复合索引，切记。

4. like语句操作
  一般情况下不鼓励使用like操作，如果非使用不可，注意正确的使用方式。like ‘%aaa%’不会使用索引，而like ‘aaa%’可以使用索引。

5. 不要在列上进行运算

6. 不使用NOT IN 、<>、！=操作，但<,<=，=，>,>=,BETWEEN,IN是可以用到索引的

7. 索引要建立在经常进行select操作的字段上。

  这是因为，如果这些列很少用到，那么有无索引并不能明显改变查询速度。相反，由于增加了索引，反而降低了系统的维护速度和增大了空间需求。

8. 索引要建立在值比较唯一的字段上。

9. 对于那些定义为text、image和bit数据类型的列不应该增加索引。因为这些列的数据量要么相当大，要么取值很少。

10. 在where和join中出现的列需要建立索引。

11. where的查询条件里有不等号(where column != …),mysql将无法使用索引。

12. 如果where字句的查询条件里使用了函数(如：where DAY(column)=…),mysql将无法使用索引。

13. 在join操作中(需要从多个数据表提取数据时)，mysql只有在主键和外键的数据类型相同时才能使用索引，否则即使建立了索引也不会使用。

14. 在进行联表查询时，建立关联的表的字段类型最好一样且长度一致，这样能更好的发挥索引的作用。

15. 组合索引时切记此条约束：组合索引中有多个字段，其中一个字段是有范围判断，则需将此字段在最后面。如

ALTER TABLE USER_DEMO ADD INDEX name_age (NAME,AGE);  因为age会有范围判断，则建组合索引时将AGE字段放在后面。

16. 字符集字段比较，UTF8与UTF-BIN联合查询是不能走索引的。

如某张表的order_no字段类型为varchar(50),另一张表的order_no字段类型为varchar(50) COLLATE utf8_BIN。则此时联合查询时不能走索引的，切记。

即两张表的字段类型如下：

```sql
`order_no` varchar(50) COLLATE utf8_bin NOT NULL DEFAULT '' COMMENT '订单号',
```

```sql
`order_no` varchar(50) NOT NULL DEFAULT '' COMMENT '订单号',
```
17. 以下几种情况不适合建索引：
+ 表记录太少
+ 经常插入、删除、修改的表
+ 数据重复且分布平均的表字段。如一个表有10万行记录，其中字段column1只有A和B两种值，且每个值的分布概率大约为50%，那么对这种表column1字段建索引一般不会提高数据库的查询速度

18. 给表创建主键，对于没有主键的表，在查询和索引定义上有一定的影响。

19. 避免表字段为null，建议设置默认值（如int类型设置默认值为0），这样在索引查询上，效率会高很多。

### 建立索引的几大原则：

1. 最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询(>、<、between、like)就停止匹配，比如a = 1 and b = 2 and c > 3 and d = 4 如果建立(a,b,c,d)顺序的索引，d是用不到索引的，如果建立(a,b,d,c)的索引则都可以用到，a,b,d的顺序可以任意调整。
2. =和in可以乱序，比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式
3. 尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录
4. 索引列不能参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);
5. 尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可
