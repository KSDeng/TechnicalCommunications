# TechnicalCommunications



### 2022.6.28



* 关于redis的一篇文章 https://redis.com/blog/redis-vs-dragonflydb/  贡献者：@Pure White
* redis好书推荐：《Redis 设计与实现》https://github.com/7-sevens/Developer-Books/blob/master/Redis/Redis%E8%AE%BE%E8%AE%A1%E4%B8%8E%E5%AE%9E%E7%8E%B0.pdf 贡献者：@齐天大圣
* 好书分享：https://www.amazon.sg/Designing-Data-Intensive-Applications-Reliable-Maintainable/dp/1449373321/ref=asc_df_1449373321/?tag=googleshoppin-22&linkCode=df0&hvadid=389055537118&hvpos=&hvnetw=g&hvrand=16246476611259019893&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9062542&hvtargid=pla-432535594773&psc=1 贡献者：@Xavier

* 找书网站: goodreads, zlib  贡献者：@秃秃猫



### 2022.6.29



* 面试八股公众号推荐：小林coding  贡献者@欧阳鸿荣
* 组合测试文章分享: https://mp.weixin.qq.com/s/Kcsfe6jYu65BgDHpnA8ZAw 贡献者：@Wei Lai
* 《技术面试 Do & Don't》 贡献者：@WilliamZheng
* 回溯文章分享：https://www.zhihu.com/question/478362331?utm_source=wechat_session&utm_medium=social&utm_oi=1310529834506715136&utm_content=group3_supplementQuestions&utm_campaign=shareopn 贡献者：@小葱哥
* acm好书推荐：刘汝佳 贡献者：@Relapse
* acm网站推荐：https://oi-wiki.org/ 贡献者：@小葱哥



### 2022.7.24

MySQL单表查询优化总结

* 尽量使用`TINYINT`、`SMALLINT`、`MEDIUMINT`作为整数类型而非`INT`，如果非负的则加上`UNSIGNED`

`TINYINT`占1个字节，`SMALLINT`占2个字节，`MEDIUMINT`占3个字节，`INT`占4个字节，`BIGINT`占8个字节

使用适当的数据类型，可以**减小数据表，从而减少磁盘I/O**，并且在计算过程中，值的处理速度也快一些。如果数据列被索引了，那么使用较短的值带来的性能提高更加显著，不仅索引可以提高查询速度， 而且短的索引值也比长的索引值处理起来要快一些。

* 能用`CHAR`不要用`VARCHAR`

在使用可变长度的数据行的时候，由于记录长度不同，在多次执行删除和更新操作之后，数据表的**碎片要多一些**。你必须使用OPTIMIZE TABLE来定期维护其性能。固定长度的数据行没有这个问题。

如果出现数据表崩溃的情况，那么**数据行长度固定的表更容易重新构造**。使用固定长度数据行的时候，每个记录的开始位置都可以被检测到，因为这些位置都是固定 记录长度的倍数，但是使用可变长度数据行的时候就不一定了。这不是与查询处理的性能相关的问题，但是它一定能够加快数据表的修复速度。

* 尽可能把数据列标记为`NOT NULL`，如果存在`NULL`的数据列尽量通过其它有意义的值来代替

所有使用`NULL`的情况，都可以通过一个有意义的值表示，这样有利于代码的可读性和可维护性，并能从约束上增强业务数据的规范性

`NULL`值到非`NULL`值的**更新无法做到原地更新**，更容易发生索引分裂，从而影响性能

可以为`NULL`的列**需要更多的存储空间**，对于`MyISAM`引擎，需要额外的一个bit（向上取整到1个字节）；对于innodb引擎，也需要额外的空间，具体取决于行格式

`NULL`值的存在也会使得值的比较更加复杂

注：`key_len`（索引键的长度）和3个因素有关：数据类型、字符编码、是否为`NULL`

总结：至少在索引列不要允许`NULL`值的存在



* 尽量使用`TIMESTAMP`而非`DATETIME`

两者**存储方式不同**。`TIMESTAMP`存储的是对应时间到Unix元年（1970-01-01 00:00:01.000000）的秒数（客户端插入时字段转换，查询时自动转换回`YYYY-MM-DD HH:MM:SS[.fraction]`格式）；而`DATETIME`按原样存储，不做任何转换

`TIMESTAMP`只需要4个字节，`DATETIME`需要8个字节（在 MySQL 5.6.4 之前，占 8 个字节 ，之后版本，占 5 个字节。（小数秒+3 个字节））

**能够表示的时间范围不同**，

`TIMESTAMP`：`'1970-01-01 00:00:01.000000' UTC` 到 `'2038-01-09 03:14:07.999999' UTC`

`DATETIME`：`1000-01-01 00:00:00.000000` 到 `9999-12-31 23:59:59.999999`

如果存入的是`NULL`，两者的处理方式不同，

`TIMESTAMP`：会自动存储当前时间（ `now()` ）。

`DATETIME`：不会自动存储当前时间，会直接存入 `NULL` 值。



总结：`TIMESTAMP`在存储、索引、比较时相对`DATETIME`都更有优势，一般只有在表示的范围超出`TIMESTAMP`可以表示的范围时才使用`DATETIME`，其它情况尽量使用`TIMESTAMP`



* 单表不要有太多字段，常用字段和非常用字段分离（**垂直分表**）

若将非常用字段和常用字段放在一起，则整个表的数据量会很大，聚集索引的数据量更大，数据页更多，增加磁盘块I/O次数，同时也会取出不必要的数据，进而降低查询效率



贡献者：@齐天大圣
