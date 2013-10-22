---
layout: post
title: "SQLite使用 iOS平台"
date: 2013-09-03 13:35
comments: true
categories: Database iOS
---

###不太常用，但很关键命令

####1.整理数据库：vacuum

VACUUM命令是SQLite的一个扩展功能，模仿PostgreSQL中的相同命令而来。若调用VACUUM带一个表名或索引名， 则将整理该表或索引。在SQLite 1.0中，VACUUM命令调用gdbm_reorganize()整理后端数据库文件。

SQLITE 2.0.0中去掉了GDBM后端，VACUUM无效。在2.8.1版中，VACUUM被重新实现。现在索引名或表名被忽略。

<!-- more -->

当数据库中的一个对象(表，索引或触发器)被撤销，会留下空白的空间。它使数据库比需要的大小更大，但能加快插入速度。实时的插入和删除会使得数据库文件结构混乱，减慢对数据库内容访问的速度。 VACUUM命令复制主数据库文件到临时数据库并从临时数据库重新载入主数据库，以整理数据库文件。这将除去空白页，使表数据彼此相邻排列，并整理数据库文件结构。不能对附加数据库文件进行以上操作。

若当前有活动事务，该命令无法起作用。对于in-memory数据库，该命令无效。

SQLite3.1中，可以通过使用auto-vacuum模式取代VACUUM命令，使用auto_vacuum pragma开启该模式。
扩展阅读：http://www.sqlite.org/lang_vacuum.html


####2.导出数据库：.dump

.output file.sql

.dump

.output stdout



####3.导入数据库：.read 和.import

.read file.sql

.import [file][table]


###SQLite不支持的常用操作

*	1 高级联结：SQlite支持“左外部联结（LEFT OUTER JOIN）”，不支持“右外部联结（RIGHT OUTER JOIN）”和“全外部联结（FULL OUTER JOIN）”。
*	2 修改表操作：SQlite支持“重命名表（RENAME TABLE）”，使用“ALTER TABLE给表添加新列”。不支持使用“ALTER TABLE”进行“删除列（DROP COLUMN）”，“修改列（ALTER COLUMN）”，“添加约束（ADD CONSTRAINT）”等。
*	3 触发器支持不完整：SQlite支持“行级触发器（FOR EACH ROW）”，不支持“语句级触发器（FOR EACH STATEMENT）”。
*	4 视图只能读不能写：SQlite不能在一个视图上使用“删除（DELETE）”，“插入（INSERT）”和“更新（UPDATE）”语句。但是可以创建一个触发器，当对表进行“DELETE”，“INSERT”或者“UPDATE”操作时激活该触发器，在该触发器的body部分做所需的操作。
*	5 不支持GRANT和REVOKE语句：因为SQlite只是去读写普通的磁盘文件，所以唯一可以应用到SQlite数据库的访问权限（控制），只有底层操作系统的普通的文件访问权限。所以那些client/server模式的数据库上通常具有的GRANT和REVOKE命令，SQlite并不支持，因为它们对于“嵌入式的数据库引擎（embedded database engine）”毫无意义。

####官方SQL语法
http://www.sqlite.org/lang_vacuum.html


