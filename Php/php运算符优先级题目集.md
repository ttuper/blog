**题目1**

	$x = 2;
	echo $x == 2 ? '我' : $x == 1 ? '你' : '它'; 

答案：`你`  
解析：  
1】== 运算符比三目运算符优先级高  
`($x == 2) ? '我' : $x == 1 ? '你' : '它' => true ? '我' : $x == 1 ? '你' : '它'`
2】三目运算符具有左结合性，即优先计算左侧三目运算  
`(true ? '我' : $x == 1) ? '你' : '它' => '我' ? '你' : '它'`  
故最终结果为：你

**题目2**

	$a = 3;
	$b = 5;
	if ($a = 3 || $b = 7)  {
	    $a++;
	    $b++;
	}
	echo '$a= ', $a, '$b=', $b;

答案：`$a=1 $b=6`  
解析：  
1】逻辑或 || 运算符优先级比赋值运算符 = 优先级高  
`$a = (3 || $b = 7)`  
2】逻辑或 || 运算符短路效应，左侧为true，则为true，右侧不在执行，全false才为false  
`$a = true`  
`$b = 5`  
3】递增递减对布尔值无影响，即true递增后仍为true，递减后也仍为true，false同理  
`$a++ => true++ => true`  
`$b++ => 5++ => 6`  
4】布尔值用echo输出后，true为1，false为0  
故最终结果为：a=1 b=6  

**题目3**

	$a = 3;
	$b = 4;
	if ($a = 5 && ($b = 6)) {
	    $a += 1;
	    $b += 1;
	}
	echo '$a= ', $a, '$b=', $b;

答案：`$a=2 $b=7`  
解析：  
1】逻辑与 && 运算符优先级比赋值运算符 = 优先级高  
`$a = 5 && ($b = 6) => $a = (5 && ($b = 6)) `  
2】括号内的表达式要优先执行  
`$a = (5 && ($b = 6)) => $a = (5 && 6) => $a = true`  
3】仅递增递减对布尔值无影响，+=、-=、*=、/=等属于赋值运算符，布尔值将转换为数字再进行运算  
`$a += 1 => $a = true + 1 => $a = 1 + 1 => $a = 2`  
`$b += 1 => $b = 6 + 1 => $b = 7`  
故最终结果为：a=2 b=7  

**题目4**

	$a = null;
	$b = true;
	$a++;
	$b++;
	echo '$a= ', $a, '$b=', $b;

答案：`$a=1 $b=1`  
解析：  
1】递增递减对布尔值无影响，即true递增后仍为true，递减后也仍为true，false同理；但对null递增后，为1，递减对null无影响  
`$a++ => null++ => 1`  
`$b++ => true++ => true`  
2】布尔值用echo输出后，true为1，false为0；null如果echo输出则为空，即`$a= $b=1`  
故最终结果为：a=1 b=1







