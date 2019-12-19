**简单理解PHP延迟静态绑定**

static::中的static其实是运行时所在类的别名，并不是定义类时所在的那个类名。这个东西可以实现在父类中能够调用子类的方法和属性。
使用(static)关键字来表示这个别名，和静态方法，静态类没有半毛钱的关系，static::不仅支持静态类，还支持对象（动态类）。

**预备概念**

**转发调用**
所谓的“转发调用”（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call()。

**非转发调用**
那么非转发调用其实就是明确指定类名的静态调用（foo::bar()）和非静态调用($foo->bar())

**后期静态绑定原理**
后期静态绑定工作原理是存储了在上一个“非转发调用”（non-forwarding call）的类名。

**例子1，简单使用static::**

	class A {
	    public static function who() {
	        echo __CLASS__;
	    }
	    public static function test() {
	        static::who(); // 后期静态绑定从这里开始
	    }
	}
	class B extends A {
	    public static function who() {
	        echo __CLASS__;
	    }
	}
	B::test();

以上例程会输出：
B

**例子2，区分转发调用和非转发调用**

	class A {
	    public static function foo() {
	        static::who();
	    }
	
	    public static function who() {
	        echo __CLASS__."\n";
	    }
	}
	
	class B extends A {
	    public static function test() {
	        A::foo();
	        parent::foo();
	        self::foo();
	    }
	
	    public static function who() {
	        echo __CLASS__."\n";
	    }
	}
	class C extends B {
	    public static function who() {
	        echo __CLASS__."\n";
	    }
	}
	
	C::test();

以上例程会输出：

A

C

C

**例子3，使用场景举例**

	class Model 
	{ 
	    public static function find() 
	    { 
	        echo static::$name; 
	    } 
	} 
	
	class Product extends Model 
	{ 
	    protected static $name = 'Product'; 
	} 
	
	Product::find(); 

输出：
Product

分析：Product::find()方法运行的是Product继承自Model的find方法，这个方法中的static对应于此时正在执行的类名，即Product，所以相当于运行Product:: $name，输出了Product。

转载自：[理解 PHP 延迟静态绑定 (late static bindings) ](https://laravel-china.org/topics/3844/understanding-php-delayed-static-binding-late-static-bindings)