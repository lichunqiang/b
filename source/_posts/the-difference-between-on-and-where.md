title: "在关联表查询的时候是在ON添加条件和在where添加添加条件异同"
date: 2015-06-15 22:07:27
tags: [MySQL]
category: [MySQL]
---

数据库在通过连接两张或多张表来返回记录时，都会生成一张中间的临时表，然后再将这张临时表返回给用户。

在使用left jion时，on和where条件的区别如下：

1. on条件是在生成临时表时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。 

2. where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。

__假设有两张表：__

表1 tab1：

```
id size 
1 10 
2 20 
3 30 
```

表2 tab2：

```
size name 
10 AAA 
20 BBB 
20 CCC 
```

两条SQL:

```
1. select * form tab1 left join tab2 on (tab1.size = tab2.size) where tab2.name=’AAA’

2. select * form tab1 left join tab2 on (tab1.size = tab2.size and tab2.name=’AAA’)
```

第一条SQL的过程：

1、中间表 on条件:

```
tab1.size = tab2.size  
tab1.id    tab1.size    tab2.size     tab2.name 
1             10          10              AAA 
2             20          20              BBB  
2             20          20              CCC 
3             30         (null)          (null) 
2

再对中间表过滤where 条件：

tab2.name=’AAA’
tab1.id       tab1.size        tab2.size     tab2.name 
1                10                10             AAA 
```

第二条SQL的过程：

```
1、中间表 on条件:   
tab1.size = tab2.size and tab2.name=’AAA’
 (条件不为真也会返回左表中的记录)  
tab1.id      tab1.size       tab2.size      tab2.name 
1             10                10             AAA 
2             20                (null)        (null) 
3             30                (null)        (null)  
```

其实以上结果的关键原因就是left join,right join,full join的特殊性，不管on上的条件是否为真都会返回left或right表中的记录，full则具有left和right的特性的并集。而inner jion没这个特殊性，则条件放在on中和where中，返回的结果集是相同的。

__参考__

* http://stackoverflow.com/questions/354070/sql-join-where-clause-vs-on-clause

* http://dba-china.com/topic/default/view/71
