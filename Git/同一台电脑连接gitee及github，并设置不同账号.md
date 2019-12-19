## 同一台电脑连接gitee及github，并设置不同账号

### 创建ssh key

	# 进入用户目录下的 .ssh 文件夹下
	# 没有这个文件夹也无所谓，直接运行下一句命令也可以
	cd ~/.ssh
	
	# 生成 key，将邮件地址替换为你 Gitee 或者 Github 使用的邮件地址
	ssh-keygen -t rsa -C "xxx@xxx.com"

会出现如下提示

	Generating public/private rsa key pair.
	Enter file in which to save the key (/c/Users/your_user_name/.ssh/id_rsa): 

这一步如果默认回车，就会生成名为`id_rsa`的文件，这里可以输入不同名字来识别文件，例如，Gitee的ssh key就是`id_rsa_gitee`，Github的ssh key就是`id_ras_github`。这里我输入`id_rsa_gitee`，然后回车

接下来就会看到：

	Enter passphrase (empty for no passphrase):

直接回车，然后会看到：

	Enter same passphrase again:

继续回车就行了。生成完毕：

	Your identification has been saved in id_rsa_gitee.
	Your public key has been saved in id_rsa_gitee.pub.
	The key fingerprint is:
	SHA256:F0K/ojCbFzgMPru11m4g/9uV03oHK+U0rKBLwOOye2c xxx@xxx.com
	The key's randomart image is:
	+---[RSA 2048]----+
	|        .        |
	|       . .       |
	|  .     . o      |
	| . + .   . o     |
	|  o X . S o.     |
	|  .+.O o.o o*    |
	|  oo=o+. .+=.+   |
	|   =++E. .oo+ .  |
	|  ++.*=o. .o .   |
	+----[SHA256]-----+

`id_rsa_github`文件也如以上步骤执行一遍即可。

### 在 Gitee 和 Github 添加 public key

找到用户目录下的 .ssh 文件夹，查看并复制创建好的`id_rsa_gitee.pub`或`id_rsa_github.pub` 的内容。

	cd ~/.ssh
	# 查看 id_rsa_gitee.pub 文件内容
	cat id_rsa_gitee.pub

会显示这样一串东西，复制它：

	ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDZbvgUEj3XAXH4HkW27ibdXgV6VHdrA9/WdSDHtiiC55mjPvxj3OtPxIbpeJmhWyHiJWR6
	uUuK+gkb//O51uWCPhHqxKR7+45tZ9jHqXW+hEKPp+odQgc+3hiHAjTkn3JGeIJlQp2UdJCDHBrp+kcgVeg91+y7cU3ufaUQ/hpD
	rCgn6uvwjwJgnDhV9DYi+gFUFe7LUwa1o4nfwg43ycuOOuT7c6VO2dj/0pLRUVTPQYu/C3kaaPVedir7mKIu/dM6Ec44bhYTp1Dq
	qp8BO42Cfo+n+dempqYTe2wcPvuDjSj884IATc/KvBfc86Yd2Uj7NI7li90Y3i6adoxUIWQh xxx@xxx.com

打开 Gitee 和 Github 的网站找到设置，再找到 SSH Keys，添加复制的 public key。

![Github上ssh key添加](Git-img/1.png)


![Gitee上ssh key添加](Git-img/2.png)

### 创建配置文件

	# gitee
	Host gitee.com
	HostName gitee.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa_gitee
	# User这个值不写的话默认就是git
	# User xxx 

	# github
	Host github.com
	HostName github.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa_github
	# User这个值不写的话默认就是git
	# User xxx


#### 原理分析

> 1.ssh 客户端是通过类似 **git@github.com:githubUserName/repName.git ** 的地址来识别使用本地的哪个私钥的，地址中的 User 是`@`前面的`git`， Host 是`@`后面的`github.com`。
> 
> 2.如果所有账号的 User 和 Host 都为 git 和 github.com，那么就只能使用一个私钥。所以要对User 和 Host 进行配置，让每个账号使用自己的 Host，每个 Host 的域名做 CNAME 解析到对应的地址上去，如上面配置中的`Host github.com`其还是解析到`github.com`，也可以是`Host first.github.com`解析到`github.com`。
> 
> 3.配置了别名之后，新的地址就是**git@github.com:githubUserName/repName.git**（在添加远程仓库时使用）。
> 
> 这样 ssh 在连接时就可以区别不同的账号了。

### 测试连接是否正常

在命令行中输入：

	ssh -T git@github.com

结果：

	Hi yourname! You've successfully authenticated, but GitHub does not provide shell access.

继续在命令行输入：

	ssh -T git@gitee.com

若返回如下内容，则 Gitee 连接正常。

	Welcome to Gitee.com, yourname!

### gitee和github配置不同用户名

如果之前使用过如下命令，则gitee和github上提交代码时，都会显示的是同一个用户名：

	git config --global user.name "xxx"
	git config --global user.email "xxx"

以上是配置全局的用户名和邮箱。

如果想不同项目配置不同的用户名的话，先进入自己项目根目录，再 `cd .git`

设置本项目的用户名和邮箱

	git config user.name "yourname"
	git config user.email "youremail"

如果要重设，则可以使用

	git config --global --unset user.name
	git config --global --unset user.email

查看当前用户名

	git config user.name
	git config user.email



