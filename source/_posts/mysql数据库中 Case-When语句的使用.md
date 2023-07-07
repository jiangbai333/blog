---
title: mysql数据库中 Case-When语句的使用
date: '2016/10/14 15:17:00'
categories:
  - 数据库
  - mysql
tags:
  - 软件
  - 数据库
  - mysql
  - SQL
abbrlink: 115e38c3
---

个人认为这个功能可以减少后台逻辑处理，并且由于在数据库内部完成，保证了数据的准确性(例如：数值与数据格式的合法性)。另外，如果条件包含结果集全集那么可以不加 else 关键字，否则需要指定 else 对应的值，如果不指定，条件之外的结果集将变成 NULL

>按条件更改查询值

如果表中 field1、field2字段条件相互冲突，那么将会以field1条件为主。
<!-- more -->

```sql
select 
	case
		when field1 < '某值1' then '小'  
		when field2 > '某值2' then '大'  
        	else '一般' END as "field"
from table; 
```

>按特定值更改查询值

```sql
select 
	case field
		when '某值1' then '值1'  
		when '某值2' then '值2'  
        	else '值3' END as "字段别名"
from table; 
```

>复合查询

```sql
select 
	case 
		when field1 > 15 and field1 < 20 then '值1'
		when field1 > 20 and field2 > 10 then '值2'  
	        else '值3' END as "字段别名"
from table; 
```
