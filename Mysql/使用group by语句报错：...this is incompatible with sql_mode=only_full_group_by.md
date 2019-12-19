##### 问题背景
在使用leftjion左连接一张表时，而且主表和副表是一对多的关系，导致结果集中出现了重复数据，为了去除重复数据，我打算使用group by来通过分组去除重复数据，结果运行时，出现了如下错误提示：

	Expression #2 of SELECT list is not in GROUP BY clause and contains nonaggregated 
	column 'loser.tank_admin.login_ip' which is not functionally dependent on columns in 
	GROUP BY clause; this is incompatible with sql_mode=only_full_group_by

通过最后一句话我们知道，此时使用`group by`是与`sql_mode=only_full_group_by`相矛盾的。

目前可以先参考以下文章：

[sql_mode=only_full_group_by研读](https://blog.csdn.net/allen_tsang/article/details/54892046)

[MySQL 5.7默认ONLY_FULL_GROUP_BY语义介绍](http://www.ywnds.com/?p=8184)