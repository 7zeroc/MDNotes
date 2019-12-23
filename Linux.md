# Linux（Ubuntu下实操）

## Ubuntu root超级管理员密码
	* 如果Ubuntu安装时没有设置root密码，安装好后在终端设置root密码：
	  输入$ sudo password 回车，连续输入两次密码，设置成功
	  测试：su root 检验是否能切换

## 开机重启常用命令
1. shutdown
	- shutdown -h now 表示立即关机。
	- shut -h 1 表示1分钟后关机。
	- shutdown -r now 立即重启。
2. halt 直接使用没有其他参数，效果等价于关机。

3. reboo 重启系统。

4. sync 把内存数据同步到磁盘，关机或者重启都应该先执行一下sync指令，把内存数据写入磁盘，避免数据丢失。

## 用户登录和注销
1. 用户注销 $ logout

2. $su root 切换用户

## 用户管理
### Linux用户设计理念
   * 用户，组，家目录
   * 每一个用户至少属于一个组或者多个组
   * 家目录 /home/
		xiaoming 的家目录
		xiaohong 的家目录
	用户登录后会默认地去到自己的家目录下
### 基本介绍
   * Linux系统是一个多用户多任务的操作系统，任何一个要使用系统资源的用户，都必须首先向系统管理员申请一个账号，然后以这个账号的身份进入系统。
   * Linux的用户至少属于一个组

### 添加用户(基于root超级管理员)
   - 基本用户
	$useradd [options] 用户名
  	1. 当创建用户成功后，会自动的在/home/目录下创建一个和用户名相同的家目录
	2. 也可以通过useradd -d 指定目录
		比如创建一个用户Ly然后指定到/home/lazy 目录下
			$ userdd -d /home/lazy Ly
	3. 创建用户后，给用户指定密码 $ password Ly 回车输入密码
	
	4. 删除用户 $ userdel 用户名，用户已删除，但是家目录还在
		$ userdel -r 用户名，删除用户同事删除用户的家目录

	5. 查询用户 $ id 用户名

	6. 切换用户 $ su 用户名，$ exit 返回原来的用户 

## 实用命令
### 搜索查找类
* find 指令
	> find指令将从指定目录向下递归地遍历各个子目录，将满足条件的文件或者目录显示在终端。
* 基本语法 $ find [搜索范围] [选项]
	
* 常见三个用法
	> 1. 比如查找 workspace 目录下是否有vim.md 文件,指定范围按名字查找
	     $ find workspace -name vim.md
	> 2. 按拥有者查找，比如查/opt下是否有用户ly的文件
	     $ find /opt -user ly
	> 3. 查找整个Linux系统大于20m的文件
	     $ find / -size +20M
	> 4. 查找 /根目录下所有的.txt文件 $ find \ -name *.txt

* locate指令: locate指令可以快速定位文件路径。locate指令利用事先建立的系统中所有文件名称及路径的locate数据库可以实现快速定位文件。locate指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的精准度，管理员必须定期更新locate时刻。
	> 基本语法 $locate 搜索的文件，补充说明：由于locate指令基于数据库查询，所以第一次运行前，必须updatedb指令创建locate数据库。
	> 使用locate指令快速定位vim.md文件所在目录
		1. $updatedb 2. $locate vim.md
* grep指令和管道符号|
	> 说明：grep过滤查找，管道符“|”，表示将前一个指令的处理结果传递给后面的命令处理。
	
	> 基本语法： grep [选项] 查找内容源文件，常用两个选项 -n 显示匹配行及行号，-i 忽略字母大小写
	
	> 例如：在vim.md中查找"gg"所在行，并显示行号：$ cat vim.md | grep -n gg
	> 例如：查找vim.md中查找所有的g忽略大小写并显示行号：cat vim.md | grep -ni g


## 压缩与解压类
* gzip/gunzip 指令：gzip用户压缩文件，gunzip用于解压，都是针对.gz文件
	> 压缩vim2.md -> vim2.md.gz 与解压 vim2.md.gz
	$ gzip vim2.md、$ gunzip vim2.md.gz
* zip/unzip 指令
	> zip 用于压缩，unzip用于解压，这个在项目打包发布中很有用
	> 基本语法:
		1. zip [选项] XXX.zip 将要压缩文件或者目录; 常用选项 -r :递归压缩，即压缩目录
		2. unzip 解压; -d 指定解压后文件存放目录
	> 比如将MDnotes目录压缩notes.zip $ zip -r motes.zip MDnotes/
	> 比如将notes.zip 解压到 /tmp/ 目录下 $ unzip -d /tmp/ notes.zip
	
* tar 指令 
	> tar 指令是打包指令，最后打包的文件是 .tar.gz 文件
	> 基本语法
	tar [选项] xxx.tar.gz 打包的内容（功能描述：打包目录，压缩文件格式.tar.gz）
	```
	   /* 选项说明 */
	 	-c 产生 .tar打包文件
		-v 详细详细信息
		-f 指定压缩文件名
		-z 打包同时压缩
		-x解压.tar文件
		
	   /* 因此cx不能同时使用 */
		压缩 -zcvf
		解压 -zxvf
	  /* 案例 */
		将 a1.txt 和 a2.txt 压缩成 a.tar.gz
			$ tar -zcvf a.tar.gz a1.txt a2.txt
		将a目录下所有文件都压缩到/tmp/下
			$ tar -zcvf all.tar.gz /tmp/
		将a.tar.gz解压到当前文件夹
			$ tar -zxvf a.tar.gz
		将all.tar.gz解压到home目录
			$ tar -zxvf all.tar.gz /home/  
	```





## 权限管理（文件与目录的权限）


	
