a标签中可以使用target，如：

	<a target="_parent" href="www.baidu.com" />

这样就可以跳出当前框架，将链接的内容展示在上级框架中了  
_blank	在新窗口中打开被链接文档。  
_self	默认。在相同的框架中打开被链接文档。  
_parent	在父框架集中打开被链接文档。  
_top	        在整个窗口中打开被链接文档。  
framename	在指定的框架中打开被链接文档。  

或者知道目标iframe的name或者id，也可以这样：

	<iframe name="myIframe"></iframe>
	<a target="myIframe" href="www.baidu.com" />
