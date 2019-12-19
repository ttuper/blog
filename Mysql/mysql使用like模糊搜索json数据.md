工作中存储到mysql的数据是json类型的，在列表使用like模糊搜索时搜索不到，使用下面的方法可解决：

1.  `$name = str_replace('"','',json_encode($name));`
2.  `$name = str_replace("\\",'_',$name);`

原因是：

1、第一步是去除json_encode转义的后字符串带的双引号

2、把转义后的unicode编码数据中的“\”替换成“_”就可直接搜索出来了，要不就得跟数据库的数据完全匹配才可以

转自：https://blog.csdn.net/xiao_bai6/article/details/52312932
