1 修改1列

	update student s, city c
	set s.city_name = c.name
	where s.city_code = c.code;


2 修改多个列

	update a, b
	set a.title=b.title, a.name=b.name
	where a.id=b.id

3 子查询

	update student s 
	set city_name = (select name from city where code = s.city_code);

原文：[如何使用MySQL一个表中的字段更新另一个表中字段](https://www.jb51.net/article/150323.htm)
