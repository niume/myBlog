---
title: 【MySQL学习笔记】 MySQL 建立索引的思路和实例
date: 2018-04-28 23:54:39
tags:
categories: MySQL
top: 80
---

### 一、索引的分类
#### 1、普通索引

#### 2、唯一索引


#### 3、全文索引

  使用FULLTEXT参数可以设置为全文索引。全文索引只能创建在char，varchar或者 text 类型的字段上。

#### 4、单列索引



#### 5、多列索引
    
多列索引是在多个字段上建立一个索引，也叫联合索引。该索引指向创建时对应的多个字段，可以通过这几个字段进行查询。但是，只有查询条件中使用了这些字段中的第一个字段时，索引才会被使用。例如，在表中对id，name,sex字段上建立索引，那么，只有查询条件使用了id字段是，该索引才会被使用。

#### 6、空间索引


### 二、创建索引的原则

为了使索引的使用效率更高，在创建索引时，必须考虑在那些字段上创建索引和创建什么类型的索引。下面来说一下索引的设计原则。

#### 1、选择唯一性索引

  唯一索引的值是唯一的，可以更快速的通过索引来确定某条记录。例如学生学号是具有唯一性的，往该字段建立唯一索引可以快速的确定某个学生的信息。如果使用姓名的话，可能存在同名的现象，从而降低查询速度。
  
#### 2、为经常需要排序、分组和联合操作的字段建立索引

  经常需要 order by、group by、DIST 和 UNION 等操作的字段，排序操作会浪费很多时间。如果围棋建立索引，可以有效的避免排序操作。

#### 3、为常作为查询条件的字段建立索引
  
  如果某个字段经常用来当做查询条件，那么该字段的查询速度会影响整个表的查询速度。因此，为这样的字段建立索引，可以提高整个表的查询速度。
  
#### 4、限制索引的数目
  
  索引的数目不是越多越好。每个索引都需要占磁盘空间，索引越多，需要的磁盘空间就越大。修改表时，对索引的重构和更新很麻烦。越多的索引，会使用更新表变得很浪费时间。

#### 5、尽量使用数据量少的索引
 
  如果索引的值很长，那么查询的速度会受到影响。例如，对一个char(100)类型的字段进行全文检索所需要的时间肯定要对比char(10)的类型的字段需要的时间要多。

#### 6、尽量使用前缀来索引

  如果索引的字段的值很长，最好使用值的前缀来索引。例如，text 和 Blog类型的字段，进行全文检索会很浪费时间。如果只检索字段的前面的若干个字符，这样可以提高检索的速度。
  
#### 7、删除不再使用的或者少使用的索引

  表中的数据被大量更新，或者数据的使用方式改变后，原有的一些索引可能不再需要，可以将其删除，从而减少索引对更新操作的影响。
  


### 三、创建表时建立索引

#### 1、创建普通索引

```bash
create table table_name(
    id int(11),
    name varchar(20),
    sex boolean,
    INDEX(id)
);
```

查看表结构
```bash
show create table table_name; 
```

可以使 EXPLAIN 语句查看索引是否被使用 
```bash
explain select * from table_name where id = 1\G
```

#### 2、创建唯一索引
```bash
create table index2(
    id int unique,
    name varchar(20),
    unique INDEX index_2(id asc)
);

```

#### 3、创建全文索引

全文索引只能在char，varchar或者text 类型的字段上。而且，只有MyISAM 储存引擎支持全文索引。

```bash
create table idnex3(
    id int,
    info varchar(20),
    FULLTEXT INDEX index3_info(info)
)ENGINE=MyISAM;

```
#### 4、创建单列索引

```bash
create table index4(
    id int,
    subject varchar(255),
    index index4_st(subject(10))
);

```
这里需要注意的，subject 的长度为255，但是index4_st索引只有10。这样做的目的还是为了提高查询速度。对于字符型的数据，可以不用查询全部信息，只查询其前面的若干字符信息。

#### 5、创建多列索引

```bash
create table index5(
    id int,
    name varchar(20),
    sex char(4),
    index index5_ns(name.sex)
);

```

这是我们可以看到，name 和sex字段上已经创建了index_ns索引。

下面我们来用explain 来看看索引情况；

```bash
mysql> explain select * from index5 where name = '22'\G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: index5
   partitions: NULL
         type: ref
possible_keys: index5_ns
          key: index5_ns
      key_len: 63
          ref: const
         rows: 1
     filtered: 100.00
        Extra: NULL
1 row in set, 1 warning (0.00 sec)

ERROR: 
No query specified

```

再看一条

```bash
mysql> explain select * from index5 where sex = '22' \G;
*************************** 1. row ***************************
           id: 1
  select_type: SIMPLE
        table: index5
   partitions: NULL
         type: ALL
possible_keys: NULL
          key: NULL
      key_len: NULL
          ref: NULL
         rows: 1
     filtered: 100.00
        Extra: Using where
1 row in set, 1 warning (0.00 sec)

ERROR: 
No query specified
```

我们发现： 在多列索引中，只有查询条件中使用了这些字段中的第一个字段时，才会被索引。



### 四、在已经存在的表中创建索引

#### 1、创建普通索引
在example0() 表中的id 创建名为index7_id 的索引。

```bash
create index index7_id on example0(id);
```
#### 2、创建唯一索引
```bash
create UNIQUE index index8_name on index8(name);
```

#### 3、创建全文索引
```bash
create FULLTEXT index index9_info on index9(info);

```

#### 4、创建单列索引
```bash

create INDEX index_name ON index4(name(10));
```

#### 5、创建多列索引

```angular2html
create INDEX index_name ON table_name(name,sex);
```

### 五、用alter table 语句来创建索引

#### 1、创建普通索引

在name字段上创建名为indx_name 的索引
```bash
alter table table_name ADD INDEX index_name(name(20));
```
#### 2、创建唯一性索引

```bash
alter table table_name ADD UNIQUE INDEX index_name(id);
```
#### 3、创建全文索引

```bash
alter table table_name ADD FULLTEXT INDEX index_name(info);

```
#### 4、创建单列索引
```bash
alter table table_name ADD INDEX index_name(name(4));
```
#### 5、创建多列索引
```bash
alter tabel table_name ADD INDEX index_name(name.sex);
```

### 六、删除索引

```bash
DROP INDEX index_name ON table_name;
```








