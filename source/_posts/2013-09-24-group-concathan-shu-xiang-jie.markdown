---
layout: post
title: "group_concat函数详解"
date: 2013-09-24 00:06
comments: true
categories: Database
tags: Database
---


问了好多人，都不知道group_concat这个函数。 
这个函数好啊，能将相同的行组合起来，省老事了。
MySQL中group_concat函数(SQLite或其它数据亦可)


完整的语法如下：

group_concat([DISTINCT] 要连接的字段 [Order BY ASC/DESC 排序字段] [Separator '分隔符'])
 
 <!-- more -->
 
####基本查询
 

Sql代码  

  1. select * from aa;  



+------+------+

| id| name |

+------+------+

|1 | 10|

|1 | 20|

|1 | 20|

|2 | 20|

|3 | 200 |

|3 | 500 |

+------+------+

6 rows in set (0.00 sec)
 
以id分组，把name字段的值打印在一行，逗号分隔(默认)
 

Sql代码  

  1. select id,group_concat(name) from aa group by id;  



+------+--------------------+

| id| group_concat(name) |

+------+--------------------+

|1 | 10,20,20|

|2 | 20 |

|3 | 200,500|

+------+--------------------+

3 rows in set (0.00 sec)
 
以id分组，把name字段的值打印在一行，分号分隔
 

Sql代码  

  1. select id,group_concat(name separator ';') from aa group by id;  



+------+----------------------------------+

| id| group_concat(name separator ';') |

+------+----------------------------------+

|1 | 10;20;20 |

|2 | 20|

|3 | 200;500 |

+------+----------------------------------+

3 rows in set (0.00 sec)
 
以id分组，把去冗余的name字段的值打印在一行，


###逗号分隔
 

Sql代码  

  1. select id,group_concat(distinct name) from aa group by id;  



+------+-----------------------------+

| id| group_concat(distinct name) |

+------+-----------------------------+

|1 | 10,20|

|2 | 20 |

|3 | 200,500 |

+------+-----------------------------+

3 rows in set (0.00 sec)
 
以id分组，把name字段的值打印在一行，逗号分隔，以name排倒序
 

Sql代码  

  1. select id,group_concat(name order by name desc) from aa group by id;  



+------+---------------------------------------+

| id| group_concat(name order by name desc) |

+------+---------------------------------------+

|1 | 20,20,10 |

|2 | 20|

|3 | 500,200|

+------+---------------------------------------+

3 rows in set (0.00 sec)