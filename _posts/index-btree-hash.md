title: B-Tree索引与Hash索引的比较
date: 2015-08-21 21:00:00
categories: Develop
tags: [MySql,Index,B-Tree,Hash,翻译,数据结构]
---

>翻译自http://dev.mysql.com/doc/refman/5.6/en/index-btree-hash.html

理解B-Tree和Hash的数据结构能够帮助我们预测不同存储引擎下的查询性能差异。存储引擎在索引中使用这些数据结构，尤其是`MEMORY` 同时提供了B-Tree和Hash索引让你选择。
<!-- more --> 
###B-Tree索引特性

----------

B-Tree索引可以在表达式中使用=, >, >=, <, <=用作列比较或者 BETWEEN 运算符。还能使用LIKE比较，如果参数是一个不以通配符开头的常量。举个例子，下面的SELECT语句使用了索引：

    SELECT * FROM tbl_name WHERE key_col LIKE 'Patrick%';
    SELECT * FROM tbl_name WHERE key_col LIKE 'Pat%_ck%';

第一条语句中，只有'Patrick' <= key_col < 'Patricl' 的行会被考虑。第二条语句中，只有'Pat' <= key_col < 'Pau' 的行会被考虑。

下面的`SELECT`语句没有使用索引：
    
    SELECT * FROM tbl_name WHERE key_col LIKE '%Patrick%';
    SELECT * FROM tbl_name WHERE key_col LIKE other_col;

第一条语句是因为它以通配符开头，第二条语句是因为没有使用常量。

如果你使用... LIKE '%string%'而且`string`超过三个字符，那么MYSQL会使用`Turbo Boyer-Moore algorithm`算法来初始化查询表达式，然后用这个表达式来让查询更迅速。

查询中有col_name IS NULL可以使用col_name索引。

任何一个没有覆盖所有WHERE中AND级别条件的索引是不会被使用的。也就是说，要使用一个索引，这个索引中的第一列需要在每个AND组中出现。

下面的WHERE条件会使用索引：


	... WHERE index_part1=1 AND index_part2=2 AND other_column=3
	
	    /* index = 1 OR index = 2 */
	... WHERE index=1 OR A=10 AND index=2
	
	    /* optimized like "index_part1='hello'" */
	... WHERE index_part1='hello' AND index_part3=5
	
	    /* Can use index on index1 but not on index2 or index3 */
	... WHERE index1=1 AND index2=2 OR index1=3 AND index3=3;

下面的WHERE条件不会使用索引：

	    /* index_part1 is not used */
	... WHERE index_part2=1 AND index_part3=2
	
	    /*  Index is not used in both parts of the WHERE clause  */
	... WHERE index=1 OR A=10
	
	    /* No index spans all rows  */
	... WHERE index_part1=1 OR index_part2=10


有时候mysql不会使用索引，即使在可用的情况下。例如当mysql预估使用索引会读取大部分的行数据时。（在这种情况下，一次全表扫描可能比使用索引更快，因为它需要更少的检索）。然而，假如语句中使用LIMIT来限定返回的行数，mysql则会使用索引。因为当结果行数较少的情况下使用索引的效率会更高。

###Hash索引特性

----------

Hash类型的索引有一些区别于以上所述的特征：

 - 它们只能用于对等比较，例如=和<=>操作符（但是快很多）。它们不能被用于像<这样的范围查询条件。假如系统只需要使用像“键值对”的这样的存储结构，尽量使用hash类型索引。


 - 优化器不能用hash索引来为ORDER BY操作符加速。（这类索引不能被用于搜索下一个次序的值）


 - MySQL不能判断出两个值之间有多少条数据（这需要使用范围查询操作符来决定使用哪个索引）。假如你将一个MyISAM表或InnoDB表转为一个依靠hash索引的MEMORY表，这可能会影响一些查询。


 - 只有完整的键才能被用于搜索一行数据（使用了B-Tree索引，那么任何一个键的前缀都可以用于查找）。
