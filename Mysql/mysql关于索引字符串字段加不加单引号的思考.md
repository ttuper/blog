在发表看法之前，先仔细浏览下面这篇文章
[干货！MySQL常见的面试题＋索引原理分析！](https://mp.weixin.qq.com/s/Qq2i0cAqkbSXm4fWg2oAaQ)

文章开头有个提问，如下图所示：
![提问](https://upload-images.jianshu.io/upload_images/9899281-65e0397354569c7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
前三个我们根据最左前缀原则，可以轻松的判断出是否使用到了联合索引，在判断最后一个的时候，我想当然的认为没有用到联合索引，原因很简单，因为我觉得联合索引建立的顺序是empno，title，fromdate，索引的使用也应该和建立顺序息息相关，但我却忽略了一点，那就是mysql的自我优化能力。
在文章末尾，笔者提到：
![笔者说](https://upload-images.jianshu.io/upload_images/9899281-c83e6404db9c3b83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
好了，我们可以看到最后一段话他是这样说的，“但是mysql会对sql进行优化，优化之后会将empno=1这个条件放到第一位，从而可以使用索引”，可想而知，对于存在联合索引中的首位字段时，如果where条件中对于比较顺序和联合索引中的创建顺序不一致，mysql会自动为我们优化将首位索引字段放在第一位，从而使用上联合索引。

还有一点，我们可以看到，在提问中的几个sql语句中，它的`emp_no`字段有的用单引号引起来，有的没有引起来，用了单引号的，我们应该会想到可能是字符串类型，比方说第三题和第四题，那么引号引起来和不引起来对使用索引情况有变化吗？答案是，有。所以我们可以对这几题提出质疑。
我们来试验一下，具体会出现什么样的差异。
现在我们有一个这样的表：

	CREATE TABLE `test` (
	  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
	  `title` varchar(255) NOT NULL DEFAULT '',
	  PRIMARY KEY (`id`),
	  KEY `ddd` (`title`) USING BTREE
	) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8

可以看到该表中仅两个字段，一个id主键，一个加了普通索引的title，接下里有两个这样的sql语句：

1、`select * from test where title='1';`

2、`select * from test where title=1;`

猜测下这两条语句是否都用到了索引。
在接下来，我们为test表新增一个字段，就叫做emp_no吧，varchar类型或者别的什么类型，同样索引还是id为主键，title为普通索引键。同样还是上面两条语句，猜测是否用到了索引。
加字段前的结果：
1、explain select * from test where title='1';
![加字段前sql1结果](https://upload-images.jianshu.io/upload_images/9899281-a88940cf060e2e8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2、explain select * from test where title=1;
![加字段后sql2结果](https://upload-images.jianshu.io/upload_images/9899281-1d46fc511e38f04e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
声明：test表没什么数据的情况下做的实验。
可以看到这两者都用到了title的索引，但是也有不同的地方，就是1扫描的rows是1行，而2扫描的rows是2行。也就是说，2有多少行数据就多扫描了多少行的数据，而1则仅扫描一行，比2有这方面的提升，从这里我们就可以看出加了引号的性能会高。

加字段后的结果：
1、explain select * from test where title='1';
![加字段后sql1](https://upload-images.jianshu.io/upload_images/9899281-10bc83b85c66b03a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


2、explain select * from test where title=1;
![加字段后sql2](https://upload-images.jianshu.io/upload_images/9899281-3103990f656d78d2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这次是不是有很明显的对比。

首先一点，我们可以确认，当字段为字符串类型时，加上单引号后的查询效率肯定是比不加单引号的效率要高得多的。在mysql中单引号是代表字符不需要还编辑了，如果不是字符还需要mysql进行一个类型判断操作所以自然就慢了。
其次，当表中所有字段均为索引时（id主键其实可以忽略，因为即使没有这个主键，mysql也会隐式的为我们创建一个看不见的主键），无论加单引号还是不加单引号都可以用到索引，但是，加了单引号后效率肯定会更高，从结果中我们就可以看出其扫描的行数就不一样。

具体的待后续补充，还差点东西。。。。。。

