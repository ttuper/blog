### 问题场景：
今日使用try{} catch(Exception $e) {}来捕获一个mysqli连接时，未捕获到异常信息。因为项目中自认为一致是使用该方法来处理异常的，但此处，使用原生方法连接数据库时却发生了错误，十分惊讶，看来，光知道用是万万不行的。

### 解决进度
先上问题代码

	$host = '127.0.0.1';
	$port = '3306';
	$user = 'root1'; // 此处我故意将数据库连接用户名写错，正确的用户名是root
	$pwd = '';
	$dbName = 'dbname';
	try{
	    $Mysqli = new Mysqli($host, $user, $pwd, $dbName);
	}catch (Exception $e){        //捕获异常
	    echo  '数据库连接失败：' . $e->getMessage();    //理想情况是此处打印异常信息
	}

###### 理想中的报错输出结果：
【数据库连接失败：失败原因】
###### 实际页面输出结果是：
【Warning: mysqli::__construct(): (HY000/1045): Access denied for user 'root1'@'localhost' (using password: NO) in E:\SVN\CodeRepertory\yanyan_yii\web\tt\index.php on line 9】

怎么会这样呢？
后来我想到项目中使用的是Yii2.0，该框架集成的是PDO来连接数据库，项目中这样使用没有报错，肯定是有原因的，于是我是用原生PDO来连接数据库

	try {
	    $dbh = new PDO('mysql:$host;', $user, $pwd);
	} catch (PDOException $e) {
	    echo '数据库连接失败：' . $e->getMessage();    //打印异常信息
	}

###### 理想中的报错输出结果：
【数据库连接失败：失败原因】
###### 实际页面输出结果是：
【数据库连接失败：SQLSTATE[HY000] [1045] Access denied for user 'root1'@'localhost' (using password: NO)】

完全符合预期，在这里，我们就可以得出一个结论，使用原生mysqli来连接数据时，mysqli的构造函数，即连接时如果出错的话并没有抛出一个异常，而是直接报错，但是PDO则会抛出一个异常，具体可以查看[PDO的构造函数](http://php.net/manual/zh/pdo.construct.php)，这里对PDO类的构造函数返回值的说明很明确。

所以try catch才能捕获到该异常。

那么，PDO中所有进行操作的地方如果出错都是抛出一个异常吗？此时，我们可以打开php手册，找到PDO的那个栏目来仔细看看：[PDO错误与错误处理](http://php.net/manual/zh/pdo.error-handling.php)

PDO 提供了三种不同的错误处理模式，分别是：ERRMODE_SILENT、ERRMODE_WARNING、ERRMODE_EXCEPTION

默认模式是ERROR_SILENT，即不抛出异常也不现实错误，仅给PDO的errorInfo和errorCode设置相应的错误信息。

ERRMODE_WARNING则除了设置errorCode和errorInfo之外，还会在页面上显示出错误，例如第一段代码实际的输出结果。

ERRMODE_EXCEPTION则显而易见的除了设置errorCode和errorInfo之外，还会抛出一个异常，这时候就能捕获到了，上面两种模式通过try catch是捕获不到错误的。

为了更明显的区分，我们做了简单测试
#### 例一：默认ERRMODE_SILENT

	try {
	    $PDO = new PDO('mysql:$host;', $user, $pwd);
	} catch (PDOException $e) {
	    echo 'Connection failed: ' . $e->getMessage();
	}
	$PDO ->query("SELECT wrongcolumn FROM wrongtable");// 这里表名称是不存在的
	print_r($PDO ->errorInfo());die;

输出结果：

Array ( [0] => 3D000 [1] => 1046 [2] => No database selected )
页面中除了该输出结果并未见任何warning等错误输出

#### 例二：ERRMODE_WARNGIN
	
	try {
	    //这里我们设置错误模式为ERROR_WARNIGN
	    $PDO = new PDO('mysql:$host;', $user, $pwd, [PDO::ATTR_ERRMODE => PDO::ERRMODE_WARNING]);
	} catch (PDOException $e) {
	    echo 'Connection failed: ' . $e->getMessage();
	}
	$PDO ->query("SELECT wrongcolumn FROM wrongtable");// 这里表名称是不存在的
	print_r($PDO ->errorInfo());die;

输出结果：

Warning: PDO::query(): SQLSTATE[3D000]: Invalid catalog name: 1046 No database selected in E:\www\lianxi\Db.class.php on line 14
Array ( [0] => 3D000 [1] => 1046 [2] => No database selected )
看到了吧，除了errorInfo信息还有warnging错误

#### 例三：ERRMODE_EXCEPTION

	try {
	    //这里我们设置错误模式为ERROR_EXCEPTION
	    $PDO = new PDO('mysql:$host;', $user, $pwd, [PDO::ATTR_ERRMODE => PDO::ERRMODE_EXCEPTION]);
	} catch (PDOException $e) {
	    echo 'Connection failed: ' . $e->getMessage();
	}
	$PDO ->query("SELECT wrongcolumn FROM wrongtable");// 这里表名称是不存在的
	print_r($PDO ->errorInfo());die;

输出结果：

Fatal error: Uncaught PDOException: SQLSTATE[3D000]: Invalid catalog name: 1046 No database selected in E:\www\lianxi\Db.class.php:14 Stack trace: #0 E:\www\lianxi\Db.class.php(14): PDO->query('SELECT wrongcol...') #1 {main} thrown in E:\www\lianxi\Db.class.php on line 14

此时已经不是warnging错误了而是fatal error，说是有未捕获的PDOException异常，此时，就可以用try catch 把$PDO->query前后用try catch捕获异常并显示出来了，这里就不操作了

我们再来看此文标题中的问题

大家应该都知道PDO类是一个封装了可以操作多种数据库的类库，而Mysqli是原生的，其中并未对代码中产生的异常信息做处理，而PDO类做了，所以在使用mysqli时，要主动使用throw new Exception('错误信息')来抛出一个异常，供try catch来捕获。

PDOException则又是继承了Exception类的子类，可以理解成咱们自己写的一个自定义异常处理类，你不使用PDOException而使用Exception也是可以的，只不过不能使用PDOException类中的独有的属性和方法罢了，并无大碍，虽没什么大问题，但是此处还是应保持同一。

可以再看一个例子：

	try{
	    $Mysqli = new Mysqli($host, $user, $pwd, $dbName);
		if($Mysqli->connect_errno) {
			throw new Exception('数据库连接失败'); // 这里我们抛出异常信息
		}
	}catch (Exception $e){        //捕获异常
	    echo  $e->getMessage();    
	}

输出结果：

Warning: mysqli::__construct(): (HY000/1045): Access denied for user 'root1'@'localhost' (using password: NO) in E:\www\lianxi\Db.class.php on line 9
数据库连接失败

我们看到不仅输出了warngin还输出了捕获的异常信息，此时如果不想看到warngin可以使用error_reporting函数将错误级别设置为0，即关闭所有PHP错误报告。

不仅仅是数据库连接这方面需要手动throw抛出异常来应对，其他需要被捕获异常的地方也应主动抛出异常，变量名错误属于运行时错误，无法捕获







