---
title: "MySQL 外键"
date: 2018-11-09 02:17:48
tags: [ mysql ]
---

>如果公共关键字在一个关系中是主关键字，那么这个公共关键字被称为另一个关系的外键。

说白了就是两张表， A 表的某个字段必须和 B 表的某个字段关联起来。

B 表那个字段如果有值 value，A 表的这个字段才能使用 value 这个值。如果 B 表该字段不存在的值，是无法作为 A 表该字段的值来使用的。

这样就可以保证在写入数据的时候，不会因为 B 表的数据未写入而 A 表的数据写入成功导致数据不一致。

也能避免当 A 表关联 B 表字段。B 表擅自删除导致 A 无法关联到 B 表。

**其父表被关联的字段，称为子表的外键。**

这里有以下几个特点：

1. 父表不存在的值，子表是无法引用。
2. 子表不存在的值，父表可以不存在。

除了基本的特性，外键还以 3 种关联类型作为区分。

- DISTRIC
- CASCADE
- Set NULL
- No Action

`DISTRIC` 是严格(默认)模式，一旦父表的 col 被关联，该  col 无法被删除、修改。

`CASCADE`是级联模式， 如果父表被修改或者被删除，子表也会跟着被修改、删除。

`Set NULL`父表如果进行操作，子表的该字段将被置空为 NULL。

`No Action`父表如果进行操作，子表不做任何动作。



## 外键添加的要求

1. MySQL 引擎必须是 innodb。
2. 父子表关联的两个键的类型必须一致。
3. 如果子表已经有数据，必须保证子表的数据都能在父表上被匹配到。
4. 外键必须拥有索引(Index)。
5. 检查是否重建了同名的索引，比如第一次建立失败，再次建立可能会因为索引重复导致失败
6. 检查是否在键上设置了`unsigned`。



## 外键添加方式

创建表格时:

```sql
CREATE TABLE `my_tab1` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(10) NOT NULL DEFAULT '',
  `class` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `class` (`class`),
  CONSTRAINT `my_tab1_ibfk_1` FOREIGN KEY (`class`) REFERENCES `my_tab2` (`abc`) ON DELETE SET NULL,
  CONSTRAINT `my_tab1_ibfk_2` FOREIGN KEY (`class`) REFERENCES `my_tab2` (`abc`) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4;
```

注意一点是`ON DELETE` 和 `ON UPDATE` 可以单独设置。如果未设置，就是默认的`district`。

如果表格已经被创建了，可以通过 ALTER 添加外键。

```sql
ALTER TABLE `my_tab1` ADD CONSTRAINT `my_tab1_ibfk_2` FOREIGN KEY (`class`) REFERENCES `my_tab2` (`abc`) ON DELETE CASCADE ON UPDATE CASCADE
```



