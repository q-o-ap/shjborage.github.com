---
layout: post
title: "Mysql for MacOSX 安装和基本操作"
date: 2013-06-07 10:55
comments: true
categories: Database
---


##一.安装mysql 

1.mysql下载地址http://dev.mysql.com/downloads/mysql/
我的机器是mac 10.7的;使用mysql-5.1.52-osx10.6-x86.dmg安装包， 没有更新的，这个可用。（5.5也可以这样用，基本一致 MacOSX10.8.x目前都可以用）
2.安装软件包位于硬盘映象(.dmg)文件中，必须首先双击搜索起中的图标来安装该文件。应当安装图像并显示其内容。 


注释：在继续安装前，一定要使用MySQL管理器应用程序(在Mac OS X服务器上)或通过命令行的mysqladmin shutdown关闭所有运行MySQL服务器实例。 

<!-- more -->

MySQL的Mac OS X PKG安装到/usr/local/mysql-VERSION，并且还会安装一个符号连接，/usr/local/mysql，指向新的位置。如果有/usr/local/mysql目录，首先将它改名为/usr/local/mysql.bak。安装完后，安装器执行mysql_install_db在MySQL数据库中创建授权表。 

安装布局与tar文件二进制分发版类似；所有MySQL二进制代码位于目录/usr/local/mysql/bin中。默认情况，MySQL套接字文件创建为/tmp/mysql.sock。 
如果你想在系统启动过程中自动启动MySQL，你还需要安装MySQL Startup Item。在MySQL 5.1中,它是Mac OS X安装盘映像的一部分，是一个独立的安装软件包。只需要双击MySQLStartupItem.图标并按照说明来安装。 

MySQL 5.1的Startup Item安装在/Library/StartupItems/MySQLCOM。Startup Item安装时在系统配置文件/etc/hostconfig中加入变量MySQLCOM=-YES-。如果你想要disable自动启动MySQL，只需要将该变量更改为MySQLCOM=-NO-。 
安装完后，可以在终端窗口运行下面的命令来启动MySQL。你必须具有管理员权限来执行该任务。 

如果你已经安装的Startup Item： 

shell> sudo /Library/StartupItems/MySQLCOM/MySQLCOM start 

(Enter your password, if necessary) 

(Press Control-D or enter "exit" to exit the shell) 

如果不使用Startup Item，输入下面的命令序列： 

shell> cd /usr/local/mysql 

shell> sudo ./bin/mysqld_safe 

(Enter your password, if necessary) 

(Press Control-Z) 

shell> bg 

(Press Control-D or enter "exit" to exit the shell) 


##二.创建用户 分配权限 

```
grant all privileges on *.* to 'user'@'localhost' with grant option 
grant all privileges on *.* to 'user'@'localhost' identified by ‘123456′; 
flush privileges; 
```


#####1.新建用户。 

*如果mysql不识别的话，export PATH=$PATH:/usr/local/mysql/bin*
默认密码为空，直接按回车即可。

登录MYSQL 
```
@>mysql -u root -p 
```
@>密码 
创建用户 
mysql> insert into mysql.user(Host,User,Password) values("localhost","phplamp",password("1234")); 
刷新系统权限表 
mysql>flush privileges; 
这样就创建了一个名为：phplamp  密码为：1234  的用户。 

然后登录一下。 

mysql>exit; 
@>mysql -u phplamp -p 
@>输入密码 
mysql>登录成功 

#####2.为用户授权。 

登录MYSQL（有ROOT权限）。我里我以ROOT身份登录. 
@>mysql -u root -p 
@>密码 
首先为用户创建一个数据库(phplampDB) 
mysql>create database phplampDB; 
授权phplamp用户拥有phplamp数据库的所有权限。 如果其它机器想访问的话，就把localhost改为"%"
```
>grant all privileges on phplampDB.* to phplamp@localhost identified by '1234'; 
```
刷新系统权限表 
mysql>flush privileges; 
mysql>其它操作 


如果想指定部分权限给一用户，可以这样来写: 
mysql>grant select,update on phplampDB.* to phplamp@localhost identified by '1234'; 
//刷新系统权限表。 
mysql>flush privileges; 


#####3.删除用户。 
@>mysql -u root -p 
@>密码 
mysql>DELETE FROM user WHERE User="phplamp" and Host="localhost"; 
mysql>flush privileges; 
//删除用户的数据库 
mysql>drop database phplampDB; 

#####4.修改指定用户密码。 
@>mysql -u root -p 
@>密码 
mysql>update mysql.user set password=password('新密码') where User="phplamp" and Host="localhost"; 
mysql>flush privileges; 


## 三、使用后修改编码

修改my.ini文件
加上
default-character-set=gb2312
设定数据库字符集
alter database da_name default character set 'charset'
#####1)设置数据库编码 /etc/my.cnf
[mysqld]
default-character-set=gbk
...
[client]
default-character-set=gbk
－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－－
#####2)按字符集导出
$mysqldump -u root -p dbname --default-character-set=gbk > a.sql;

#####3)查看SQL文件的编码
[root@localhost gethtml]# file a.sql
   a.sql: UTF-8 Unicode ...
[root@localhost gethtml]# iconv -f utf-8 -t gbk a.sql > a2.sql
[root@localhost gethtml]# file a2.sql
   a2.sql: Non-ISO extended-ASCII English text 这时已经是gbk的编码了
#####3)导入
查看数据库服务和客户端字符集
mysql> status;
Server characterset:    gbk
Db     characterset:      gbk
Client characterset:      latin1
Conn. characterset:     latin1

mysql> set names 'gbk';   //这样
mysql> status;
Server characterset:   gbk
Db     characterset:     gbk
Client characterset:     gbk
Conn. characterset:    gbk
这时才能导数据
```
mysql> source a.sql;
----------------------------------------------------------------------------------
```
单独设置某个数据库：
alter database testdb character set utf8;
查看mysql支持的编码：
show character set;

查看系统的字符集和排序方式的设定可以通过下面的两条命令：
mysql> SHOW VARIABLES LIKE ''character_set_%'';
LINUX 如下:
#####1)导出数据
[root@www.cnscn.org ~]$ mysqldump -u root -p dbname --default-character-set=gbk > base_user.sql;
#####2)查看导出的sql文件的编码
[root@www.cnscn.org ~]$ file base_user.sql
　 base_user.sql: UTF-8 Unicode text, with very long lines
#####3)转成要用的编码
[root@www.cnscn.org ~]$ iconv -f utf-8 -t gbk base_user.sql >base_user_gbk.sql
#####4)连接数据库并查看当前库的编码
[root@www.cnscn.org ~]$ mysql -uroot
　mysql> use testdb;
mysql> status;
    Server characterset:    latin1
    Db     characterset:    gbk
    Client characterset:    latin1
    Conn. characterset:    latin1
#####5)设置成需要的编码
mysql>set names 'gbk';
#####6)查看现在的编码
mysql> status;
    Server characterset:    latin1
    Db     characterset:    gbk
    Client characterset:    gbk
    Conn. characterset:    gbk
#####7)导入我们转换成gbk后的文件
```
mysql> source base_user_gbk.sql;
```

##四、导入、导出sql文件
步骤如下：

一.MYSQL的命令行模式的设置：
桌面->我的电脑->属性->环境变量->新建->

PATH=“；path\mysql\bin;”其中path为MYSQL的安装路径。

二.简单的介绍一下命令行进入MYSQL的方法：

#####1.C:\>mysql -h hostname -u username -p 
按ENTER键，等待然后输入密码。这里hostname为服务器的名称，如localhost，username为MYSQL的用户名，如root。
进入命令行后可以直接操作MYSQL了。

#####2.简单介绍一下MYSQL命令：
mysql->CREATE DATABASE dbname;//创建数据库
mysql->CREATE TABLE tablename;//创建表
mysql->SHOW DATABASES;//显示数据库信息，有那些可用的数据库。
mysql->USE dbname;//选择数据库
mysql->SHOW TABLES;//显示表信息，有那些可用的表
mysql->DESCRIBE tablename;//显示创建的表的信息

#####三.从数据库导出数据库文件：
1.将数据库mydb导出到e:\mysql\mydb.sql文件中：

打开开始->运行->输入cmd 进入命令行模式
c:\>mysqldump -h localhost -u root -p mydb >e:\mysql\mydb.sql

然后输入密码，等待一会导出就成功了，可以到目标文件中检查是否成功。

2.将数据库mydb中的mytable导出到e:\mysql\mytable.sql文件中：
c:\>mysqldump -h localhost -u root -p mydb mytable>e:\mysql\mytable.sql 

3.将数据库mydb的结构导出到e:\mysql\mydb_stru.sql文件中：
c:\>mysqldump -h localhost -u root -p mydb --add-drop-table >e:\mysql\mydb_stru.sql 

//-h localhost可以省略，其一般在虚拟主机上用

#####四.从外部文件导入数据到数据库中：

从e:\mysql\mydb2.sql中将文件中的SQL语句导入数据库中：

1.从命令行进入mysql，然后用命令CREATE DATABASE mydb2;创建数据库mydb2。

2.退出mysql 可以输入命令exit；或者quit；

3.在CMD中输入下列命令：
c:\>mysql -h localhost -u root -p mydb2 < e:\mysql\mydb2.sql

然后输入密码，就OK了。

#####五.下面谈一下关于导入文件大小限制问题的解决：

默认情况下：mysql 对导入文件大小有限制的，最大为2M，所以当文件很大时候，直接无法导入，下面就这个问题的解决列举如下：

1.在php.ini中修改相关参数：

影响mysql导入文件大小的参数有三个：

memory_limit=128M,upload_max_filesize=2M,post_max_size=8M

修改upload_max_filesize=200 M 这里修改满足你需要的大小，可以同时修改其他两项memory_limit=250M post_max_size=200M

这样就可以导入200M以下的.sql文件了。

上文中是把mysql放置在系统路径中，其实不妨也行。例如我的Mysql安装目录为D:\MySQL Server 5.0；

则首先用cmd打开dos窗口，然后输入D:(没有'\')回车

此时应该会出现D:\>这样的标记，然后在其后面输入D:\MySQL Server 5.0\bin回车（顺序颠倒也行的）

出现D:\MySQL Server 5.0\bin> 接着输入mysqldump -u用户名 -p 数据库名 > 数据库名.sql（也可以输入路径）；具体用法参照上文。

导入文件同样，只是改了‘>’为‘<’就行了。或者直接用source 也行：

常用source 命令

进入mysql数据库控制台

如mysql -u root -p（没必要,省略）

mysql>use 数据库

然后使用source命令，后面参数为脚本文件(如这里用到的.sql)

mysql>source d:wcnc_db.sql

##修改Root密码


```
#mysql -u root -p
Enter password:
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES/NO)
```
 
安装时没有给root设置密码而默认其为空的,或忘记密码，不能成功登录，或者登录后没有权限操作。
可以尝试以下的方法：
方法1  :
```
# mysqladmin -u root password 123456
```

设置了root的密码。

方法2
{% codeblock %}
# /etc/init.d/mysql stop
# mysqld_safe --user=mysql --skip-grant-tables --skip-networking &
# mysql -u root mysql

mysql> UPDATE user SET Password=PASSWORD('123456') where USER='root';
mysql> FLUSH PRIVILEGES;
mysql> quit
# /etc/init.d/mysql restart
# mysql -uroot -p
Enter password: <输入新设的密码newpassword>
mysql>　
{% endcodeblock %}

方法3：
MySQL安装之后，root的密码是空的。为了提高安全性有必要给root加上密码。
```
mysql –u root –p
password:
mysql>SET PASSWORD FOR 'root'@'localhost' = PASSWORD('Password');
```