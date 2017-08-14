# Linux常见命令2

`20:30上课！！！`

#### 1.查找命令

#### 2.管道

#### 3.输入输出重定向

#### 4.vim 编辑器

#### 5.关机



### 1.查找命令

**1.命令搜索**

`whereis` 搜索命令的位置和帮助文档的位置
`which` 搜索位置和命令的别名

```shell
[budong@budong ~]$ whereis ls
ls: /bin/ls /usr/share/man/man1/ls.1.gz
[budong@budong ~]$ which ls
alias ls='ls --color=auto'
	/bin/ls
```



**2.文件查找**

1.`find` 命令格式：`find [-path] -options` 

```
path :要查找的目录，默认是当前目录
option:
-name 按文件名的某种规则的查找
-type 按文件类型查找  f 普通文件   l 符号连接   d 目录
-size 按文件大小查找
-exec<执行指令>：假设find指令的回传值为True，就执行该指令； 
-print 假设find指令的回传值为Ture，就将文件或目录名称列出到标准输出。格式为每列一个名称，每个名称前皆有“./”字符串

通配符：
*匹配任意内容
?匹配任意一个字符
[]匹配任意一个中括号内的字符
```

```shell
[budong@budong /]$ find ./ -name '*.py'   #查找根目录下所有后缀为py的文件
[budong@budong bin]$ find -name 'se*'
./setfont
./sed
[budong@budong bin]$ find -name 'se?'
./sed

[budong@budong bin]$ find -size -6k  #查找小于6k的文件
[budong@budong bin]$ find -size +6k  #查找大于6k的文件，不写的时候就是等于

[budong@budong ~]$ find ./ -name '*.py' -exec rm -rf {} \;  #删除当前目录下所有的py文件
#{} 用于与-exec选项结合使用来匹配所有文件，然后会被替换为相应的文件名

[budong@budong ~]$ find ./ -name 'a' -type d  #查找当前目录下所有为a的文件夹

[budong@budong ~]$ find ./ -name 'haha*' -type d
./tmp/haha

[budong@budong ~]$ find ./ -name 'haha*' -type f
./haha.txt

[budong@budong ~]$ find ./ -name '*.py'
./test/a/b.py
[budong@budong ~]$ find ./ ! -name '*.py'  #查找所有当前目录下所有不是py的文件

[budong@budong ~]$ find  -name '*.py' -print
```

`locate`命令

安装：`sudo yum install mlocate`

locate 是在数据库中按文件名搜索，要查找的文件名中含有的字符串，搜索数据速度更快。搜索的数据库是： /var/lib/mlocate/mlocate.db  ，这个数据库，每天自动更新一次，在使用locate之前，可以使用updatedb命令，手动更新数据库。

初始化： sudo updatedb

```shell
[budong@budong ~]$ locate b.py
[budong@budong ~]$ locate haha.py
[budong@budong ~]$ touch haha.py
[budong@budong ~]$ ls
haha.py  test
[budong@budong ~]$ locate haha.py
[budong@budong ~]$ sudo updatedb
[budong@budong ~]$ locate haha.py
/home/budong/haha.py
```

`grep`命令 

文件过滤分割与合并,grep是一种强大的文本搜索工具，它能使用正则表达式搜索文本，并把匹配的行打印出来。

选项

```
-c 计算符合范本样式的列数。
-E 将范本样式为延伸的普通表示法来使用，意味着使用能使用扩展正则表达式。
-i 忽略字符大小写的差别。
-n 在显示符合范本样式的那一列之前，标示出该列的编号。
-s 不显示错误信息。
-v 反转查找。
-w 只显示全字符合的列。
-x 只显示全列符合的列。
-o 只输出文件中匹配到的部分。
```

```shell
[budong@budong ~]$ grep -c root /etc/passwd
2
[budong@budong ~]$ grep  root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

[budong@budong ~]$ grep  root /etc/passwd
[budong@budong ~]$ grep -n root /etc/passwd
1:root:x:0:0:root:/root:/bin/bash
11:operator:x:11:0:operator:/root:/sbin/nologin

[budong@budong ~]$ grep -i Root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

[budong@budong ~]$ grep -x root /etc/passwd
[budong@budong ~]$ grep -w root /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

[budong@budong ~]$ grep -o root /etc/passwd
```



### 2.管道

将一个程序或命令的的输出作为另一个程序或命令的的输入，有两种方法：一种是通过一个临时文件将两个命令或程序结合在一起；另一种就是管道。

管道可以把一系列命令连接起来，可以将前面的命令的输出作为后面命令的输入。使用管道符‘|‘来建立一个管道行。

```shell
[budong@budong ~]$ find ./ -name '*.py'|grep test
./test/a/b.py
#先找到所有txt文件，然后再在这些文件里面找包含test的文件
```



### 3.输入输出重定向

**1.输入重定向**

输入重定向是指把命令或可执行程序的的标准输入重定向到指定的文件中。也就是输入可以不来自键盘，而是来自一个指定的文件。输入重定向主要用于改变一个命令的的输入源。

输入重定向的一般形式为“命令<文件名”

```shell
[budong@budong ~]$ cat < /etc/passwd
```

**2.输出重定向**

输出重定向是指把命令或可执行程序的标准输出或标准错误输出重新定向到指定文件中。命令中的输出不显示在屏幕上，而是写入到指定的文件中，以便完成以后的问题定位或其他的用途。

输出重定向的一般形式为“命令>文件名”

标准输出`echo`

```shell
[budong@budong ~]$ echo python
python

[budong@budong ~]$ echo python > py.txt
[budong@budong ~]$ ls
haha.py  py.txt  test
```



### 4.vim编辑器

安装vim

`sudo yum install vim`

工作模式：命令模式、输入模式、末行模式

**模式之间切换**

多次按ESC可以进入命令模式
在命令模式下，按 i或o或a进入输入模式
在命令模式下，按shift+; ，末行出现:冒号则进入末行模式
按ESC回到命令模式

**进入与退出**

vim filename 进入
当打开一个文件时处于命令模式

在末行模式下输入q退出文件
wq 保存退出
q! 不保存退出

**移动光标**

命令模式和编辑模式下都可以用上下左右键（或者h,j,k,l）

gg 到文件第一行

G 到文件最后一行

^非空格行首

0 行首(数字0)

$行尾

```
w  #移动到下一个单词开始
b  #移动到上一个单词开始
e  #移动到当前单词词尾
```



**输入文本**

在命令模式下
按 i 从光标所在位置前面开始插入

按I在当前行首插入

按 a 从光标所在位置后面开始输入

按A在当前行尾插入

按 o 在光标所在行下方新增一行并进入输入模式

按O在当前上面一行插入

进入输入模式后，在最后一行会出现--INSERT--的字样

**复制与粘贴**

在命令模式下
yy 复制整行内容到vi缓冲区
yw 复制当前光标到单词尾内容到vi缓冲区
y$ 复制当前光标到行尾的内容到vi缓冲区
y^ 复制当前光标到行首的内容到vi缓冲区

p 读取vi缓冲区的内容，并粘贴到光标当前的位置

```
6yy  #复制6行
```



**删除与修改**

命令模式下
dd 删除光标所在行
x  删除光标所在字符
u  撤销上一次操作

dw  删除一个单词

CTRL + r  撤销u

CTRL + v  块操作

v  块操作

```
#怎样注释
1.命令模式下，按住CTRL+v（小写）
2.方向选择，选择要注释的内容
3.安装shift+i
4.输入#
5.按esc
```



**保存文档**

:q  结束编辑不保存退出，如果有修改不会退出
:q! 放弃所做的更改强制退出
:w  保存更改
:wq 保存更改并退出

x  等于wq

**查找**

命令模式下：

向前搜索 `/`

向后搜索`?`

n        重复上一条/或？命令，搜索方向相同

N        重复上一条/或？命令，搜索方向相反



### 5.关机

`shutdown` 命令安全地将系统关机。

```
-t   多久之后关机
-r   重启计算机
-h   关机后关闭电源
```

**最简单的关机命令`halt`**

`halt`命令就是调用`shutdown  -h`

**重启**

`reboot`命令用于重启系统

