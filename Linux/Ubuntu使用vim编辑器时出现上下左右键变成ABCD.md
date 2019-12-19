今天在配置安装php时，要打开配置文件做些修改，肯定是要使用到vim编辑器的，我按照之前的使用命令之类的，在用到上下左右键时居然出现了ABCD，这我就纳闷了，难道Ubuntu的vim编辑器和别的不一样？然后我查了查Ubuntu上vim编辑器的使用方法，也没有发现什么不一样的，上下左右键就是那字面上的意思，还有别的命令也是一样的。

后来发现原来是Ubuntu自带的vim编辑器版本比较老，于是我安装如下命令重新安装vim编辑器：

	sudo apt-get remove vim-common
	sudo apt-get install vim

OK，完美解决。