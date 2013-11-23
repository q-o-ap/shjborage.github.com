---
layout: post
title: "Python MySQLdb安装以及问题汇总"
date: 2013-10-30 23:17
comments: true
categories: python Database
---

自已备注一些, 斜体

转自：http://blog.chinaunix.net/uid-8487640-id-3183185.html

MySQLdb是Python连接MySQL的模块，下面介绍一下源码方式安装MySQLdb：

首先要下载下载：请到官方网站http://sourceforge.net/projects/mysql-python/ 或者 [点击链接下载](http://downloads.sourceforge.net/project/mysql-python/mysql-python-test/1.2.3c1/MySQL-python-1.2.3c1.tar.gz?use_mirror=nchc)

<!-- more -->

解压：tar zxvf MySQL-python*

进入文件目录，运行以下命令：
```
python setup.py install 
```

安装完成，到你的python安装目录下的site-packages目录里检查以下文件是否存在，如果存在即代表安装成功了

Linux：MySQL_python-1.2.3c1-py2.6-linux-i686.egg
Mac OS X：MySQL_python-1.2.3c1-py2.6-macosx-10.4-x86_64.egg

####注：如果碰到mysql_config not found的问题，有两种方法解决：*可能两个方法要合一起*

1）ln -s /usr/local/mysql/bin/mysql_config /usr/local/bin/mysql_config
将mysql_confi从你的安装目录链接到/usr/local/bin目录下，这样就可以在任意目录下访问了（也可以放到/usr/bin）

2）编辑源码文件夹的site.cfg文件，去掉#mysql_config = /usr/local/bin/mysql_config前的注释＃，修改后面的路径为你的mysql_config真正的目录就可以了。（如果不知道mysql_config在哪里，运行命令：whereis mysql_config）

####注:如果碰到import error: libmysqlclient.so.18: cannot open shared object file: No such file or directory

解决方法: 

locate or find libmysqlclient.so.18
link path/libmysqlclient.so.18 /usr/lib/libmysqlclient.so.18
vi /etc/ld.so.conf    //加入libmysqlclient.so.18 所在的目录
插入: /usr/lib/

保存退出后执行/sbin/ldconfig生效

####*后部安装：需要进行一个软连接链接不然会提示报错。*

问题如下：

```
>>> from django.db import connection
>>> cursor = connection.cursor()
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/Library/Python/2.7/site-packages/django/db/__init__.py", line 39, in __getattr__
    return getattr(connections[DEFAULT_DB_ALIAS], item)
  File "/Library/Python/2.7/site-packages/django/db/utils.py", line 193, in __getitem__
    backend = load_backend(db['ENGINE'])
  File "/Library/Python/2.7/site-packages/django/db/utils.py", line 108, in load_backend
    return import_module('%s.base' % backend_name)
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/importlib/__init__.py", line 37, in import_module
    __import__(name)
  File "/Library/Python/2.7/site-packages/django/db/backends/mysql/base.py", line 17, in <module>
    raise ImproperlyConfigured("Error loading MySQLdb module: %s" % e)
ImproperlyConfigured: Error loading MySQLdb module: dlopen(/Library/Python/2.7/site-packages/MySQL_python-1.2.4b4-py2.7-macosx-10.9-intel.egg/_mysql.so, 2): Library not loaded: libmysqlclient.18.dylib
  Referenced from: /Library/Python/2.7/site-packages/MySQL_python-1.2.4b4-py2.7-macosx-10.9-intel.egg/_mysql.so
  Reason: image not found
>>> 
```

解决方案：

```
sudo ln -s /usr/local/mysql/lib/libmysqlclient.18.dylib /usr/lib/libmysqlclient.18.dylib
```
