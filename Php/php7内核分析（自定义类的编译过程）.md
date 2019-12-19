> 1)如果父类没有找到则将opcode置为
ZEND_DECLARE_INHERITED_CLASS_DELAYED ，**这种情况下当前类是没有编译到CG(class_table)中去的，也就是这个时候这个类是无法使用的**，在执行的时候会再次尝试这个过程，那个时候如果找到父类了则再加入EG(class_table)；  
> 2)如果找到父类了则与无继承的类处理一样，将zend_class_entry添加到
CG(class_table)中，然后将对应的两条opcode删掉，除了这个外还有一个非常重要的操作： zend_do_inheritance() ，这里主要是进行属性、常量、成员方法的合并、拷贝。

	//情况1
	// 结合对象的编译过程，首先是根据类名在EG(class_table)中查找对应 
	// zend_class_entry、然后是创建并初始化一个对象、最后是初始化调用构造函数的
	new A();zend_execute_data
	class A extends B{}
	class B{}
	===================
	完整opcodes：
	1 ZEND_NEW => 执行到这报错，因为此时A因为找不到B尚未编译进EG(
	class_table)
	2 ZEND_DO_FCALL
	3 ZEND_FETCH_CLASS
	4 ZEND_DECLARE_INHERITED_CLASS
	5 ZEND_DECLARE_CLASS => 注册class B
	6 ZEND_RETURN
	实际执行顺序：5->1->2->3->4->6



	//情况2
	class A extends B{}
	class B{}
	new A();
	===================
	完整opcodes：
	1 ZEND_FETCH_CLASS
	2 ZEND_DECLARE_INHERITED_CLASS => 注册class A，此时已经可以找到B
	3 ZEND_DECLARE_CLASS => 注册class B
	4 ZEND_NEW
	5 ZEND_DO_FCALL
	6 ZEND_RETURN
	实际执行顺序：3->1->2->4->5->6，执行到4时A都已经注册，所以可以执行


	//情况3
	class A extends B{}
	class B extends C{}
	class C{}
	new A();
	===================
	完整opcodes：
	1 ZEND_FETCH_CLASS => 找不到B,直接报错
	2 ZEND_DECLARE_INHERITED_CLASS
	3 ZEND_FETCH_CLASS
	4 ZEND_DECLARE_INHERITED_CLASS => 注册class B，此时可以找到C，所以注册成功
	5 ZEND_DECLARE_CLASS => 注册class C
	6 ZEND_NEW
	7 ZEND_DO_FCALL
	8 ZEND_RETURN
	实际执行顺序：5->1->2->3->4->5->6->7->8，执行到1发现还是找不到父类B，报

这个没看懂，个人理解的话是这样：  
编译类A的时候发现A有父类，所以此时的opcode就为ZEND_FETCH_CLASS、ZEND_DECLARE_INHERITED_CLASS，但是在EG中找B的时候，没找到B，所以此时将opcode重置为ZEND_DECLARE_INHERITED_CLASS_DELAYED，表示向下执行的时候会再次尝试这个过程，那个时候如果找到B则再加入EG(class_table)，但此时发现B还是没找到，因为上面说了，**这种情况下当前类是没有编译到CG(class_table)中去的，也就是这个时候这个类是无法使用的**，所以第一句报错，B未找到

