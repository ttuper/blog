情景描述：
项目中难免会遇到嵌套事务的情况，当我在使用本身有开启事务的主方法中调用了一个同样存在事务的子方法时出现了如下的错误提示：

	SQLSTATE[HY000]: General error: 2014 Cannot execute queries while other unbuffered 
	queries are active. Consider using PDOStatement::fetchAll(). Alternatively, 
	if your code is only ever going to run against mysql, you may enable query buffering 
	by setting the PDO::MYSQL_ATTR_USE_BUFFERED_QUERY attribute.
	The SQL being executed was: SAVEPOINT LEVEL1

单从字面意思来理解就是有别的查询是活动的，别的查询还没结束，你就来执行下一个查询，如果你的数据库是MySQL的，那可以设置PDO::MYSQL_ATTR_USE_BUFFERED_QUERY 这个属性为true来启用查询缓存......吧啦吧啦......  
最后它给出了出现问题的sql语句，即SAVEPOINT LEVEL1的时候。

我通过这句话给的解决方案，在db配置文件中，设置PDO::MYSQL_ATTR_USE_BUFFERED_QUERY属性为true，发现没用用场。

再然后，我查到设置PDO::ATTR_EMULATE_PREPARES为false可以解决该问题，于是我尝试了下，的确可以，想想就很开心，终于解决了。

但是，如果把这个属性设置为false，那么，接下来从数据查询出来的 int 整型 类型的数据就会变成 string 字符串 类型，这个，就尴尬了。难道没有两全的办法吗？  
默认情况下，PDO是会将int转换成string的

	PDO::ATTR_STRINGIFY_FETCHES 提取的时候将数值转换为字符串，默认为true。 
	PDO::ATTR_EMULATE_PREPARES 启用或禁用预处理语句的模拟，默认为true。

为了保证整型还是整型，请设置上述两项属性为false。

到此时，就想，既然我还是需要整型还是整型的，但是，将PDO::ATTR_EMULATE_PREPARES改成true又能解决今天出现的问题，那么咱们就在需要该值为true的时候改成true，然后操作完毕后将其改回false。这样感觉上应该能OK，说干就干：

	// Yii2.0设置PDO属性的语法
	Yii::$app->db->pdo->setAttribute(\PDO::ATTR_EMULATE_PREPARES, 1); 
	// 存在savepoint level1可能的代码逻辑
	 if($FilmFollow->updateFollow($filmId, $epNum) === false) {
	   $this->addError('', '0:追剧失败');
	   return false;
	 }
	// 改回false
	Yii::$app->db->pdo->setAttribute(\PDO::ATTR_EMULATE_PREPARES, 0);

试验后发现，完美解决，整型还是整型，错误也不再发生，开森(/≧▽≦)/



