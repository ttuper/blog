先给出一段sql:

	select 
		contentid,title,url,pv,thumb 
	from 
		content 
	where 
		status=6 and 
		catid in (1,2,3) and 
		thumb <> '' and 
		published >= 1566662400 
	order by 
		pv desc 
	limit 1

单看这句sql其实没啥大毛病，但是这个sql却执行了6秒钟还多。

这时候，把`published >= 1566662400`放在`status=6`前面，执行时间就缩短到了1秒以内。

探究其中原因，会发现

如果只查询如下sql:

	select 
		contentid,title,url,pv,thumb 
	from 
		content 
	where 
		published >= 1566662400

查询出来的结果就是两万多条

如果查询如下sql:

	select 
		contentid,title,url,pv,thumb 
	from 
		content 
	where 
		status=6

查询出来的结果是十几万条

现在应该能看出差别了

在些sql之前，不光光要查出想要的结果，还需要分析下数据，尽可能把能缩小查询范围的条件放在前边，后边的条件再继续查询范围就小很多了。

