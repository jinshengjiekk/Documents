#**MySQL 索引 （InnoDB)**
* ### MySQL 的索引本质上就是一种数据结构，目标是高效的获取数据。 作用类似于一本书的简介，或者一本字典的索引
* ### 以空间换时间，以损耗写时候的性能换取读时候的性能
* ### 索引是在存储引擎层面实现的，不是在MySQL Server层面实现的
* ### 不是所有的存储引擎都支持所有的索引类型。即使多个存储引擎支持某一索引类型，它们的实现和行为也可能有所差别
* ###  一次查询可能用到多个索引 （index merge)

## ** MySQL架构 **
![](http://i1.piimg.com/588926/9554a2c9e4482912.png)
![](http://i1.piimg.com/588926/faead437f0f52e34.png)

 * ##### 第一层不是Mysql特有的，大多数基于网络的客户端/服务器的工具或者服务都有类似的架构。比如连接管理、授权认证、安全。
 * ##### == 第二层是Mysql独有的部分，大多数Mysql的核心服务功能都在这一层，包括查询解析、分析、优化、缓存及所有内置函数（比如日期、时间、数学和加密），所有的跨存储引擎的功能都在这一层（比如存储过程、触发器、视图等）。==
 * ##### 第三层包含了存储引擎，负责Mysql的存储与提取。服务器通过Api与存储引擎进行通信，这些接口屏蔽了不同存储引擎之间的差异，使得这些差异对上层的查询过程透明。存储引擎包含几十个底层函数，用于执行诸如  ‘开始一个事物’ 或者 ‘根据主键提取一行数据’等操作。但存储引擎不会解析SQL，不同的存储引擎也不会互相通信，而只是简单的响应上层的请求。

## **SQL 语句的优化和执行流程**
 * ##### Mysql会解析查询，并创建内部数据结构（解析树），然后对其进行各种优化，包括重写查询、决定表的读写顺序，以及选择合适的索引
 * ##### 用户通过特殊的关键字提示优化器，影响他的决策过程(eg:force index(XXX),use index(XXX), ignore index(XXX))
 * ##### 优化器并不关心数据表使用的什么存储引擎，但存储引擎对于优化查询是有影响的。优化器会请求存储引擎提供容量或者某个操作的具体开销信息，以及表数据的统计信息。
 * ##### 对于SELECT语句，在解析查询之前，服务器会先检查查询缓存，如果命中缓存，服务器就不会进行查询解析、优化和执行的整个过程，而是直接返回查询缓存中的内容。
  > ##### MySQL将缓存存放在一个引用表（不要理解成table，可以认为是类似于HashMap的数据结构），通过一个哈希值索引，这个哈希值通过查询本身、当前要查询的数据库、客户端协议版本号等一些可能影响结果的信息计算得来。所以两个查询在任何字符上的不同（例如：空格、注释），都会导致缓存不会命中。

![](http://i2.muimg.com/588926/c9b60373f4f227b2.png)
## 索引的分类

### 数据结构的角度
 * #### B+ 树索引 （InnoDB）  B+树介绍--> http://git.darcytech.com/base/tech/wikis/mysql-btree
 * #### hash 索引 (Memory)
 * #### FUllTEXT 索引（MyISAM, InnoDB)
 * #### R-tree 索引（MyISAM)

### 逻辑的角度

 * #### 主键索引
	* ##### 主键索引是一种特殊的唯一索引，不能为NULL。每个InnoDB引擎的表都会有一个主键索引，即使没有显示声明
 * #### 普通索引（单列索引）
 	* ##### 对表中一个字段进行索引
 * #### 复合索引（多列索引）
 	* ##### 复合索引指在多个字段上创建的索引，查询时要遵守 ==最左前缀== 原则
 * #### 唯一索引
 	* ##### 可以是单列索引也可以是多列索引，可以为NULL。 但是索引的列必须是唯一的，可以用来在数据库层面确保数据的唯一性

### 物理存储的角度
 * #### 聚簇索引（clustered index)
 	* ##### 索引项的数据顺序与表中记录的顺序一致，索引的叶子节点即包含了相关的行的数据。 每张表只能有一个聚簇索引，聚簇索引不能随便指定。无序的聚簇索引在插入更新时会造成额外的页分裂（page split), 会造成严重的性能影响。InnoDB自增主键索引便是聚簇索引，每个InnoDB都有且仅有一个聚簇索引，因为真实数据的物理顺序只能有一种

 * #### 非聚簇索引（non-clustered index, 二级索引，辅助索引）
 	* ##### 表数据和索引是分开存储的，每个二级索引当中的叶子节点都包含相关行的主键值（聚簇索引），而不是相关行的磁盘物理地址，所以每个二级索引都是复合索引。 对二级索引进行查询要额外多一次IO（第一次通过二级索引找到主键，然后通过主键找到行数据）。  这么做的好处便是减少了行移动或数据页分裂时二级索引的维护工作，~~但会让二级索引占用更多的空间，尤其是在主键值变得越来越大的时候~~
 * #### 聚簇索引和非聚簇索引的对比图如下：

![clustered-index-vs-nonclustered-index](http://i4.buimg.com/567571/fd4af6a492d60a18.png)

* * *

## 索引区分度

#### 索引的区分度越好，选择性越高，效率最好。

#### 区分度计算公式：

 * ####  ``` 选择性 = 索引中不同值的数目/表中总记录数 = select count(distinct column)/count(*) from t ```
 * #### ``` 选择性范围: (0,1]， 为1时选择性最好 ```

## 前缀索引(在长字段上建立索引)

* ##### 前缀索引是用列的前缀代替整个列作为索引key，当前缀长度合适时，可以做到既使得前缀索引的选择性接近全列索引，同时因为索引key变短而减少了索引文件的大小和维护开销

``` sql
CREATE TABLE `city_demo` (`city` VARCHAR(50) NOT NULL) COLLATE='utf8_general_ci'ENGINE=InnoDB;

SELECT
	COUNT(DISTINCT city) AS distinct_city_count,
	COUNT(1) AS total_city_count,
	COUNT(DISTINCT city) / COUNT(1) AS selectivity
FROM
	sakila.city_demo;

+---------------------+------------------+------------+
| distinct_city_count | total_city_count | selectivity |
+---------------------+------------------+------------+
| 599                 | 19200            | 0.0312      |
+---------------------+------------------+------------+

SELECT
	COUNT(DISTINCT LEFT(city, 3)) / COUNT(*) AS sel3,
	COUNT(DISTINCT LEFT(city, 4)) / COUNT(*) AS sel4,
	COUNT(DISTINCT LEFT(city, 5)) / COUNT(*) AS sel5,
	COUNT(DISTINCT LEFT(city, 6)) / COUNT(*) AS sel6,
	COUNT(DISTINCT LEFT(city, 7)) / COUNT(*) AS sel7
FROM
	sakila.city_demo;
+------------+-----------+------------+-----------+------------+
| sel3       | sel4      | sel5       | sel6      |   sel7     |
+------------+-----------+------------+-----------+------------+
| 0.0239     | 0.0293    | 0.0305     | 0.0309    |   0.0310   |
+------------+-----------+------------+-----------+------------+
```

## 覆盖索引(covering index，三星索引)

* ##### 如果索引包含满足查询的所有列，就称为覆盖索引
* ##### 覆盖索引查询不需要回表操作，减少了很多的随机io操作
* ##### 对于InnoDB表来说，覆盖索引显得格外有用，因为InnoDB使用聚集索引组织数据，如果二级索引中包含查询所需的数据，就不再需要在聚集索引中查找了
* ##### 覆盖索引查询时除了索引本身的包含的列，还可以使用其默认的聚集索引列。 因为二级索引中的叶子节点中就包含了聚簇索引的主键值
* ##### explain 中extra 一栏显示 using index

```sql
show index from account_adjust;
+----------------+------------+-------------------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table          | Non_unique | Key_name                | Seq_in_index | Column_name  | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+----------------+------------+-------------------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| account_adjust |          0 | PRIMARY                 |            1 | id           | A         |        2160 |     NULL | NULL   |      | BTREE      |         |               |
| account_adjust |          0 | uniq_aa_acid_ouid       |            1 | account_id   | A         |          90 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          0 | uniq_aa_acid_ouid       |            2 | outer_id     | A         |         720 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          1 | idx_aa_acode            |            1 | account_code | A         |          98 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          1 | idx_aa_created          |            1 | created      | A         |        2160 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          1 | idx_ac_aid_atype_amount |            1 | account_id   | A         |          90 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          1 | idx_ac_aid_atype_amount |            2 | adjust_type  | A         |         196 |     NULL | NULL   | YES  | BTREE      |         |               |
| account_adjust |          1 | idx_ac_aid_atype_amount |            3 | amount       | A         |        1080 |     NULL | NULL   | YES  | BTREE      |         |               |
+----------------+------------+-------------------------+--------------+--------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
8 rows in set (0.00 sec)


EXPLAIN SELECT aa.amount FROM account_adjust aa WHERE aa.account_id = 60 AND aa.adjust_type = 'BUSINESS_ADJUST';
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
| id | select_type | table | type | possible_keys                             | key                     | key_len | ref         | rows | Extra                    |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
|  1 | SIMPLE      | aa    | ref  | uniq_aa_acid_ouid,idx_ac_aid_atype_amount | idx_ac_aid_atype_amount | 773     | const,const |  295 | Using where; Using index |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
1 row in set (0.01 sec)


EXPLAIN SELECT aa.amount, aa.id FROM account_adjust aa WHERE aa.account_id = 60 AND aa.adjust_type = 'BUSINESS_ADJUST';
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
| id | select_type | table | type | possible_keys                             | key                     | key_len | ref         | rows | Extra                    |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
|  1 | SIMPLE      | aa    | ref  | uniq_aa_acid_ouid,idx_ac_aid_atype_amount | idx_ac_aid_atype_amount | 773     | const,const |  295 | Using where; Using index |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+--------------------------+
1 row in set (0.01 sec)

EXPLAIN SELECT * FROM account_adjust aa WHERE aa.account_id = 60 AND aa.adjust_type = 'BUSINESS_ADJUST';
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+-----------------------+
| id | select_type | table | type | possible_keys                             | key                     | key_len | ref         | rows | Extra                 |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+-----------------------+
|  1 | SIMPLE      | aa    | ref  | uniq_aa_acid_ouid,idx_ac_aid_atype_amount | idx_ac_aid_atype_amount | 773     | const,const |  295 | Using index condition |
+----+-------------+-------+------+-------------------------------------------+-------------------------+---------+-------------+------+-----------------------+
1 row in set (0.11 sec)


```


## 回表
* ##### 在二级索引中找到相关数据后，还需要根据主键到聚簇索引中查找数据的过程就叫做回表

## 最左原则
 #### 使用复合多列索引时要符合最左匹配原则
 * ##### 如果有一个2列的索引(col1,col2),则已经对(col1)、(col1,col2)上建立了索引
 * ##### 如果有一个3列索引(col1,col2,col3)，则已经对(col1)、(col1,col2)、(col1,col2,col3)上建立了索引
 * ##### b+树的数据项是复合的数据结构，b+树是按照从左到右的顺序来建立搜索树

``` sql
show index from action_record;
+---------------+------------+-----------------------------------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table         | Non_unique | Key_name                                | Seq_in_index | Column_name    | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+---------------+------------+-----------------------------------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| action_record |          0 | PRIMARY                                 |            1 | id             | A         |     1388978 |     NULL | NULL   |      | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_buyer_atime_success          |            1 | user_id        | A         |          58 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_buyer_atime_success          |            2 | buyer_nick     | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_buyer_atime_success          |            3 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_buyer_atime_success          |            4 | success        | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tid_atime                    |            1 | user_id        | A         |          58 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tid_atime                    |            2 | trade_id       | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tid_atime                    |            3 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tarid_atime                  |            1 | user_id        | A         |          58 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tarid_atime                  |            2 | target_id      | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_tarid_atime                  |            3 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_bphone_atime                 |            1 | user_id        | A         |          58 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_bphone_atime                 |            2 | buyer_phone    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_bphone_atime                 |            3 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_atime_mt_atype_succ_uid          |            1 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_atime_mt_atype_succ_uid          |            2 | marketing_type | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_atime_mt_atype_succ_uid          |            3 | action_type    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_atime_mt_atype_succ_uid          |            4 | success        | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_atime_mt_atype_succ_uid          |            5 | user_id        | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_ssi_atime                        |            1 | sms_send_id    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_ssi_atime                        |            2 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            1 | user_id        | A         |          58 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            2 | campaign_id    | A         |        3237 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            3 | marketing_type | A         |        3268 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            4 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            5 | action_type    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_uid_camid_mt_atime_atype_success |            6 | success        | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_camid_mt_atime_atype             |            1 | campaign_id    | A         |        2869 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_camid_mt_atime_atype             |            2 | marketing_type | A         |        2899 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_camid_mt_atime_atype             |            3 | action_type    | A         |        3396 |     NULL | NULL   | YES  | BTREE      |         |               |
| action_record |          1 | idx_ar_camid_mt_atime_atype             |            4 | action_time    | A         |     1388978 |     NULL | NULL   | YES  | BTREE      |         |               |
+---------------+------------+-----------------------------------------+--------------+----------------+-----------+-------------+----------+--------+------+------------+---------+---------------+

EXPLAIN  SELECT * FROM action_record ar WHERE ar.campaign_id = 2829001;
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-------+
| id | select_type | table | type | possible_keys               | key                         | key_len | ref   | rows  | Extra |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-------+
|  1 | SIMPLE      | ar    | ref  | idx_ar_camid_mt_atime_atype | idx_ar_camid_mt_atime_atype | 9       | const | 57324 | NULL  |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-------+

EXPLAIN  SELECT * FROM action_record ar WHERE ar.campaign_id = 2829001 AND ar.marketing_type = 'DUNNING';
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------+-------+-----------------------+
| id | select_type | table | type | possible_keys               | key                         | key_len | ref         | rows  | Extra                 |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------+-------+-----------------------+
|  1 | SIMPLE      | ar    | ref  | idx_ar_camid_mt_atime_atype | idx_ar_camid_mt_atime_atype | 162     | const,const | 56942 | Using index condition |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------+-------+-----------------------+
1 row in set (0.00 sec)

SELECT * FROM action_record ar WHERE ar.campaign_id = 2829001 AND ar.marketing_type = 'DUNNING' AND ar.action_type = 'SMS';
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------------+-------+-----------------------+
| id | select_type | table | type | possible_keys               | key                         | key_len | ref               | rows  | Extra                 |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------------+-------+-----------------------+
|  1 | SIMPLE      | ar    | ref  | idx_ar_camid_mt_atime_atype | idx_ar_camid_mt_atime_atype | 315     | const,const,const | 56942 | Using index condition |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------------------+-------+-----------------------+

EXPLAIN  SELECT * FROM action_record ar WHERE ar.campaign_id = 2829001 AND  ar.action_type = 'SMS';
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-----------------------+
| id | select_type | table | type | possible_keys               | key                         | key_len | ref   | rows  | Extra                 |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-----------------------+
|  1 | SIMPLE      | ar    | ref  | idx_ar_camid_mt_atime_atype | idx_ar_camid_mt_atime_atype | 9       | const | 57324 | Using index condition |
+----+-------------+-------+------+-----------------------------+-----------------------------+---------+-------+-------+-----------------------+
```




## 排序

#### MySQL 有两种方式生成排序结果
#### 利用索引顺序扫描
 * ##### 利用索引顺序扫描排序是非常快的，可以同时进行查找和排序操作，当索引的顺序与ORDER BY中的列顺序相同且所有的列是同一方向(全部升序或者全部降序)时，可以使用索引来排序，如果查询是连接多个表，仅当ORDER BY中的所有列都是第一个表的列时才会使用索引，其它情况都会使用filesort，同时还是生成临时表，影响查询性能

####  利用filesort
 - #####  filesort也叫外排序   ~~、堆排序~~。 当MySQL不能使用索引进行排序时，就会利用自己的排序算法(快速排序算法)在内存(sort buffer)中对数据进行排序，如果内存装载不下，它会将磁盘上的数据进行分块，再对各个数据块进行排序，然后将各个块合并成有序的结果集，filesort效率不高，如果产生了磁盘IO，那么效率就进一步降低，所以平时要避免filesort，更不能容许有磁盘IO的排序
 - ##### 两遍扫描算法（two  passes):需两次访问数据，尤其在第二次访问数据时会导致大量的随机IO。 同时这种方法占用的内存较小。
 - ##### 一次扫描算法（single passes):减少了IO次数，效率较高，同时占用的内存会增多。 所以查询语句当中仅列出需要的字段，而不是`select * `将会对内存占用有所减少


## 临时表
#### 临时表分为“内存临时表”和“磁盘临时表”
##### 一般情况下，MySQL会先创建内存临时表，但内存临时表超过配置指定的值后，MySQL会将内存临时表导出到磁盘临时表；
``` sql
 show variables like '%table_size%'
+---------------------+----------+
| Variable_name       | Value    |
+---------------------+----------+
| max_heap_table_size | 33554432 |
| tmp_table_size      | 33554432 |
+---------------------+----------+
```
##### 创建内部临时表的场景：
 *  ##### group by 和 order by中的列不相同
 *  ##### order by的列不是引用from 表列表中 的第一表
 *  ##### group by的列不是引用from 表列表中 的第一表
 *  ##### 使用了sql_small_result选项
 *  ##### 含有distinct 的 order by语句
 *  ##### 含有 GROUP_CONCAT() 或者 COUNT(DISTINCT)的语句

##### 直接创建磁盘临时表的场景：
 *  ##### 表包含TEXT或者BLOB列
 *  ##### 使用UNION或者UNION ALL时，SELECT子句中包含大于512字节的列



## Index Merge -->https://dev.mysql.com/doc/refman/5.7/en/index-merge-optimization.html
 #### Index Merge——索引归并，即针对一张表，同时使用多个索引进行查询，然后将各个索引查出来的结果进行进一步的操作，可以是求交 ——Intersect，也可以是求和——Union，针对union还有一种补充算法——Sort-Union

####  Index Merge Union
``` sql
show index from order_info;
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table      | Non_unique | Key_name                | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| order_info |          0 | PRIMARY                 |            1 | oid         | A         |     4806121 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_tid              |            1 | tid         | A         |     4806121 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_buyer        |            1 | user_id     | A         |          52 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_buyer        |            2 | buyer_nick  | A         |     4806121 |     NULL | NULL   | YES  | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            1 | user_id     | A         |          52 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            2 | end_time    | A         |     4806121 |     NULL | NULL   | YES  | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            3 | status      | A         |     4806121 |     NULL | NULL   | YES  | BTREE      |         |               |
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+

EXPLAIN SELECT * FROM order_info oi WHERE oi.oid = 442404425970300 OR oi.tid = 442404425960300;
+----+-------------+-------+-------------+--------------------+--------------------+---------+------+------+----------------------------------------------+
| id | select_type | table | type        | possible_keys      | key                | key_len | ref  | rows | Extra                                        |
+----+-------------+-------+-------------+--------------------+--------------------+---------+------+------+----------------------------------------------+
|  1 | SIMPLE      | oi    | index_merge | PRIMARY,idx_oi_tid | PRIMARY,idx_oi_tid | 8,8     | NULL |    2 | Using union(PRIMARY,idx_oi_tid); Using where |
+----+-------------+-------+-------------+--------------------+--------------------+---------+------+------+----------------------------------------------+
```

## ICP 特性-->https://dev.mysql.com/doc/refman/5.6/en/index-condition-pushdown-optimization.html
 #### ICP是MySQL用索引去表里取数据的一种优化
 * ##### 关闭ICP时,index 仅仅是data access 的一种访问方式，存储引擎通过索引回表获取的数据会传递到MySQL Server 层进行where条件过滤
 * ##### 打开ICP时,如果部分where条件能使用索引中的字段,MySQL Server 会把这部分下推到引擎层,可以利用index过滤的where条件在存储引擎层进行数据过滤,而非将所有通过index access的结果传递到MySQL server层进行where过滤
 * ##### 优化效果:ICP能减少引擎层访问基表的次数和MySQL Server 访问存储引擎的次数,减少io次数，提高查询语句性能

``` sql
 CREATE TABLE `employees` (
  `emp_no` int(11) NOT NULL,
  `birth_date` date NOT NULL,
  `first_name` varchar(14) NOT NULL,
  `last_name` varchar(16) NOT NULL,
  `gender` enum('M','F') NOT NULL,
  `hire_date` date NOT NULL,
  PRIMARY KEY (`emp_no`),
  KEY `idx_emp_fn_ln` (`first_name`,`last_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

******************************ICP ON***************************************
mysql> set optimizer_switch='index_condition_pushdown=on';
Query OK, 0 rows affected (0.00 sec)

mysql> set profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> select * from employees where first_name='Sumant' and last_name like '%ac';
+--------+------------+------------+-----------+--------+------------+
| emp_no | birth_date | first_name | last_name | gender | hire_date  |
+--------+------------+------------+-----------+--------+------------+
|  10009 | 1952-04-19 | Sumant     | Peac      | F      | 1985-02-18 |
+--------+------------+------------+-----------+--------+------------+

1 row in set (0.00 sec)

mysql> set profiling = 0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> show profiles;

+----------+------------+----------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                      |
+----------+------------+----------------------------------------------------------------------------+
|        1 | 0.00161925 | select * from employees where first_name='Sumant' and last_name like '%ac' |
+----------+------------+----------------------------------------------------------------------------+

1 row in set, 1 warning (0.00 sec)

mysql> show profile for query 1;
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.000097 |
| checking permissions | 0.000009 |
| Opening tables       | 0.000854 |
| init                 | 0.000045 |
| System lock          | 0.000011 |
| optimizing           | 0.000016 |
| statistics           | 0.000127 |
| preparing            | 0.000016 |
| executing            | 0.000003 |
| Sending data         | 0.000320 |      <<=====
| end                  | 0.000008 |
| query end            | 0.000007 |
| closing tables       | 0.000010 |
| freeing items        | 0.000076 |
| cleaning up          | 0.000023 |
+----------------------+----------+

15 rows in set, 1 warning (0.00 sec)

mysql> explain select * from employees where first_name='Sumant' and last_name like '%ac';
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-----------------------+
| id | select_type | table     | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra                 |
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-----------------------+
|  1 | SIMPLE      | employees | NULL       | ref  | idx_emp_fn_ln | idx_emp_fn_ln | 44      | const |  249 |    11.11 | Using index condition |   <<=====
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-----------------------+
1 row in set, 1 warning (0.00 sec)

******************************ICP OFF**************************************

mysql> set optimizer_switch='index_condition_pushdown=off';
Query OK, 0 rows affected (0.00 sec)

mysql> set profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> select * from employees where first_name='Sumant' and last_name like '%ac' ;
+--------+------------+------------+-----------+--------+------------+
| emp_no | birth_date | first_name | last_name | gender | hire_date  |
+--------+------------+------------+-----------+--------+------------+
|  10009 | 1952-04-19 | Sumant     | Peac      | F      | 1985-02-18 |
+--------+------------+------------+-----------+--------+------------+
1 row in set (0.01 sec)

mysql> set profiling = 0;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> show profiles;
+----------+------------+----------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                      |
+----------+------------+----------------------------------------------------------------------------+
|        1 | 0.00161925 | select * from employees where first_name='Sumant' and last_name like '%ac' |
|        2 | 0.00992525 | select * from employees where first_name='Sumant' and last_name like '%ac' |
+----------+------------+----------------------------------------------------------------------------+
2 rows in set, 1 warning (0.00 sec)

mysql> show profile for query 2;
+----------------------+----------+
| Status               | Duration |
+----------------------+----------+
| starting             | 0.000094 |
| checking permissions | 0.000008 |
| Opening tables       | 0.000025 |
| init                 | 0.000039 |
| System lock          | 0.000011 |
| optimizing           | 0.000014 |
| statistics           | 0.000133 |
| preparing            | 0.000019 |
| executing            | 0.000004 |
| Sending data         | 0.009437 |     <<=====
| end                  | 0.000017 |
| query end            | 0.000010 |
| closing tables       | 0.000011 |
| freeing items        | 0.000088 |
| cleaning up          | 0.000017 |
+----------------------+----------+
15 rows in set, 1 warning (0.00 sec)

mysql> explain select * from employees where first_name='Sumant' and last_name like '%ac';
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
| id | select_type | table     | partitions | type | possible_keys | key           | key_len | ref   | rows | filtered | Extra       |
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
|  1 | SIMPLE      | employees | NULL       | ref  | idx_emp_fn_ln | idx_emp_fn_ln | 44      | const |  249 |    11.11 | Using where |   <<=====
+----+-------------+-----------+------------+------+---------------+---------------+---------+-------+------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
```
#### 关闭ICP
 ![](http://i2.muimg.com/588926/683e99c97ac56be9.png)

##### 在ICP关闭的情况下，索引仅仅作为data access使用。

------
#### 开启ICP

![](http://i4.buimg.com/588926/a6997171b7f295dc.png)
#####  （上面这张图中，在server 端应该还是有 using where， 因为where 条件的字段仍然有可能不在索引之内）
##### 在ICP优化开启时，在存储引擎端首先用索引过滤可以过滤的where条件，然后再用索引做data access，被index condition过滤掉的数据不必读取，也不会返回server端

## 索引的创建和使用
* ##### 要根据创建的实际情况创建索引，不要创建多余和重复的索引。
* ##### 对于长的字符串字段，要进行部分索引。 varchar(255) 字段比如只索引前10个字符,对长字段进行全部索引时，~~MySQL会报出索引长度超过767 bytes 限制报错~~
* ##### 根据排序order by ,group by , distinct 字段添加索引
* ##### 覆盖索引的效率最好，因为查询所需的字段都在索引里面，不需要回表，减少随机IO。
* ##### 查询使用索引是要遵循 ==最左原则==，否则将无法利用索引复合索引

## 无法利用索引的情况
* ##### 索引列进行数据运算或者函数运算     ``` where date(created_time) < '2017-05-05' ```
* ##### 未含复合索引的前缀字段,即没有遵循==最左前缀==原则  ```index_table_abc(a,b,c) , where b = 'yy' and c = 'xx';```
* ##### 前缀通配符"_" "%", ``` like %abc```
* ##### NOT,<>,!= 也不能用到索引
* ##### 字段类型不匹配。 要防止字段类型的隐式转换

```sql
show index from order_info;
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table      | Non_unique | Key_name                | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| order_info |          0 | PRIMARY                 |            1 | oid         | A         |     4806124 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_tid              |            1 | tid         | A         |     4806124 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_buyer        |            1 | user_id     | A         |          52 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_buyer        |            2 | buyer_nick  | A         |     4806124 |     NULL | NULL   | YES  | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            1 | user_id     | A         |          52 |     NULL | NULL   |      | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            2 | end_time    | A         |     4806124 |     NULL | NULL   | YES  | BTREE      |         |               |
| order_info |          1 | idx_oi_uid_etime_status |            3 | status      | A         |     4806124 |     NULL | NULL   | YES  | BTREE      |         |               |
+------------+------------+-------------------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
1 row in set (0.00 sec)

EXPLAIN SELECT * FROM order_info oi WHERE oi.tid = 445419429200724;
+----+-------------+-------+------+---------------+------------+---------+-------+------+-------+
| id | select_type | table | type | possible_keys | key        | key_len | ref   | rows | Extra |
+----+-------------+-------+------+---------------+------------+---------+-------+------+-------+
|  1 | SIMPLE      | oi    | ref  | idx_oi_tid    | idx_oi_tid | 8       | const |    1 | NULL  |
+----+-------------+-------+------+---------------+------------+---------+-------+------+-------+
1 row in set (0.00 sec)

EXPLAIN SELECT * FROM order_info oi WHERE oi.tid = '445419429200724';
+----+-------------+-------+------+---------------+------------+---------+-------+------+-----------------------+
| id | select_type | table | type | possible_keys | key        | key_len | ref   | rows | Extra                 |
+----+-------------+-------+------+---------------+------------+---------+-------+------+-----------------------+
|  1 | SIMPLE      | oi    | ref  | idx_oi_tid    | idx_oi_tid | 8       | const |    1 | Using index condition |
+----+-------------+-------+------+---------------+------------+---------+-------+------+-----------------------+
1 row in set (0.00 sec)

*******************************************************************************************
show index from alipay_trade;
+--------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table        | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+--------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| alipay_trade |          0 | PRIMARY  |            1 | trade_no    | A         |           1 |     NULL | NULL   |      | BTREE      |         |               |
+--------------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
1 row in set (0.00 sec)

EXPLAIN SELECT * FROM alipay_trade at WHERE at.trade_no = 2014112641848276;
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+
|  1 | SIMPLE      | at    | ALL  | PRIMARY       | NULL | NULL    | NULL |    1 | Using where |
+----+-------------+-------+------+---------------+------+---------+------+------+-------------+

EXPLAIN SELECT * FROM alipay_trade at WHERE at.trade_no = '2014112641848276';
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
| id | select_type | table | type  | possible_keys | key     | key_len | ref   | rows | Extra |
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | at    | const | PRIMARY       | PRIMARY | 767     | const |    1 | NULL  |
+----+-------------+-------+-------+---------------+---------+---------+-------+------+-------+
```


## 索引大小
* ##### 允许为NULL 的字段，索引要多额外一个字节来做标记位
* ##### 可变字段的索引需要额外的空间来存储索引
* ##### 查看explain中的key_len可以发现有没有走到全部索引

## 索引维护
* ##### 索引维护由数据库自动完成，对用户来说是完全透明的
* ##### 插入/修改/删除每一个索引行都会变成一个内部封装的事务
* ##### 索引越多，事务越长，代价越高
* ##### 索引越多对表的插入和索引字段修改就越慢
* ##### 控制表上索引的数量，切忌胡乱添加无用索引

## InnoDB 引擎对索引的限制
* ##### 最多只能存在64个二级索引
* ##### ~~索引默认不能超过767bytes~~
* ##### 可通过开启变量 ``` innodb_large_prefix ``` 将索引的长度提升至3072 bytes，同时要保证行格式（row formate) 为  ```  DYNAMIC ``` 或者是  ```  COMPRESSED  ```
* #####  通过参数 ```innodb_page_size``` 修改```page size```时，索引限制大小也会相应的改变  16KB --> 3072 bytes, 8KB --> 1536 bytes , 4KB --> 768 bytes
* ##### 最多允许对16列进行复合索引
* ##### BLOB和TEXT类型的列只能创建前缀索引

## 索引的好处与不足
#### 优点
 * ##### 创建唯一性索引，保证数据库表中每一行数据的唯一性
 * ##### 索引可以将随机I/O变为顺序I/O
 * ##### 索引可以减少服务器需要扫描的数据量，大大加快数据的检索速度，这也是创建索引的最主要的原因
 * ##### 在使用分组和排序子句进行数据检索时，同样可以显著减少查询中分组和排序的时间

#### 缺点
 * ##### 创建索引和维护索引要耗费时间，这种时间随着数据量的增加而增加
 * ##### 索引需要占物理空间，除了数据表占数据空间之外，每一个索引还要占一定的物理空间
 * ##### 当对表中的数据进行增加、删除和修改的时候，索引也要动态的维护，降低了数据的维护速度，影响了数据库的写入性能