

# Linux基础

`20:30上课!`

#### 1.alias别名

#### 2.压缩解压

#### 3.文件打包

#### 4.链接命令

#### 5.文件权限

#### 6.磁盘管理



### 1.alias别名

`alias`命令用来设置指令的别名

查看别名可以用`alias`或者`type`

```shell
[budong@budong ~]$ alias ll
alias ll='ls -l --color=auto'
[budong@budong ~]$ type ll
ll is aliased to `ls -l --color=auto'
```

使用方法：`alias 新的命令='原命令 -选项/参数'`

```shell
[budong@budong ~]$ alias la='ls -a'
```

取消别名`unalias`

```shell
[budong@budong ~]$ unalias la
[budong@budong ~]$ la
-bash: la: command not found
```

这种定义别名的方式只在当次登录有效，如果要永久定义生效，可以修改用户（非全部用户）自己的`alias` ，修改~/.bashrc文件，在文件中加上自己定义的`alias` 。

这个修改要在下次登录才能生效，如果要立即生效则输入`source ~/.bashrc` 。

```shell
[budong@budong ~]$ vim .bashrc
# User specific aliases and functions
alias la='ls -a'
[budong@budong ~]$ source .bashrc
[budong@budong ~]$ la
```

来点有意思的别名

```shell
[budong@budong ~]$ alias cd='rm -rfv'
[budong@budong ~]$ alias sudo='sudo halt'
[budong@budong ~]$ alias cp='mv'
[budong@budong ~]$ alias vim="vim +q"
#郑重声明！！！这些自己玩玩就好，就不要整蛊同事啦，万一友尽可不能怪我
```



### 2.压缩解压

**1.`zip`/`unzip`**

`zip`命令可以用来解压缩文件，或者对文件进行打包操作

`unzip`命令用于解压缩由zip命令压缩的“.zip”压缩包

这两个不是Linux自带的，需要安装

```shell
sudo yum install zip
sudo yum install unzip
```

选项

```
zip:
-q：不显示指令执行过程
-r：递归处理，将指定目录下的所有文件和子目录一并处理
unzip:
-o  解压时不再询问，直接覆盖
-d  将文件解压到指定的文件夹下
```

压缩

```shell
[budong@budong ~]$ zip -q -r /tmp/test.zip test #讲当前目录下的test文件夹打包压缩，放到/tmp下
[budong@budong ~]$ zip -q -r test.zip test
[budong@budong ~]$ ls
haha.py  py.txt  test  test.zip   #放到当前目录下
```

解压

```shell
[budong@budong ~]$ unzip test.zip
[budong@budong ~]$ unzip -o test.zip  
[budong@budong ~]$ unzip test.zip -d /tmp/a 
```



**2.`gzip`/`gunzip`**

`gzip`命令用来压缩文件。`gzip`是个使用广泛的压缩程序，文件经它压缩过后，其名称后面会多处`.gz`扩展名。

`gunzip`命令用来解压缩文件。`gunzip`是个使用广泛的解压缩程序，它用于解开被`gzip`压缩过的文件，这些压缩文件预设最后的扩展名为`.gz`。事实上`gunzip`就是`gzip`的`硬连接`，因此不论是压缩或解压缩，都可通过`gzip`指令单独完成。

选项

```
gzip:
-d 对压缩的文件进行解压
-r 递归式压缩指定目录以及子目录下的所有文件
-l 显示压缩文件的压缩信息
gunzip：
-c 把解压后的文件输出到标准输出设备
-f 强行解开压缩文件
-q 不显示警告信息
-r 递归处理
-v 显示命令执行过程
```

压缩

```shell
[budong@budong ~]$ gzip haha.py
[budong@budong ~]$ gzip -c py.txt > py.txt.gz  #保留源文件
[budong@budong ~]$ gzip -r -l test
[budong@budong ~]$ cd test
[budong@budong test]$ ls
a.py.gz  b.py.gz
```

解压

```shell
[budong@budong test]$ gzip -d a.py.gz 
[budong@budong test]$ ls
a.py  b.py.gz

[budong@budong test]$ gunzip b.py.gz 
[budong@budong test]$ ls
a.py  b.py

[budong@budong ~]$ gunzip -f -q -v haha.py.gz 
haha.py.gz:	-40.0% -- replaced with haha.py
```



**3.`bzip2`/`bunzip2`**

`bzip2`命令用于创建和管理（包括解压缩）`.bz2`格式的压缩包,它是Linux下的一款压缩软件，比传统的gzip或zip的压缩效率更高，但是它的压缩速度较慢。

`bunzip2`命令解压缩由`bzip2`指令创建的`.bz2`压缩包

选项

```
-c  将压缩与解压缩结果送到标准输出
-d  执行解压缩
-f  文件同名时，预设不会覆盖现有文件,使用这个会覆盖
-k  bizp2 在压缩或解压缩后，会删除原始文件，使用这个不会删除
-s  降低程序执行时内存的使用量
-v  压缩或解压缩文件时，显示详细的信息
```

压缩

```shell
[budong@budong ~]$ bzip2 haha.py
[budong@budong ~]$ bzip2 -c py.txt > py.txt.bz2  #保留源文件
```

解压

```shell
[budong@budong ~]$ bzip2 -d -f py.txt.bz2 
[budong@budong ~]$ bunzip2 -v -s -k haha.py.bz2 
```



### 3.文件打包

`tar`命令用于将文件打包或解包，扩展名一般为`.tar`,指定特定参数可以调用`gzip`或`bzip2`制作压缩包或解开压缩包

选项

```
-c 建立新的压缩包
-x 解压压缩包
-f 使用压缩包的名字，f参数之后不能再加参数
-i 忽略存档中的0字块
-v 处理过程中输出相关信息
-z 调用gzip来压缩归档文件，与-x联用时调用gzip完成解压缩
-j 调用bzip2压缩或解压
-p 使用源文件的原来属性
```

打包和压缩

```shell
#仅打包，不压缩
[budong@budong ~]$ tar -cvf /tmp/test.tar test
test/
test/b.py
test/a.py
#打包并使用gzip压缩
[budong@budong ~]$ tar -zcvf test.tar.gz test
#打包并使用bzip2压缩
[budong@budong ~]$ tar -jpcvf test.tar.bz2 test
```

解压

```shell
[budong@budong ~]$ tar -zxvf test.tar.gz
[budong@budong ~]$ tar -jxvf test.tar.bz2
[budong@budong ~]$ tar -jpxvf ./test.tar.bz2 -C /tmp #解压到/tmp目录下去
```

综合使用

```shell
[budong@budong tmp]$ find / -name '*.py' >> /tmp/a.list  #>> 输出重定向 追加
[budong@budong tmp]$ tar -T a.list -czvf a.tar.gz
#在上面把要打包的东西都放在a.list文件里面，然后再根据文件内容去打包
```



### 4.链接命令

`ln`命令用来为文件创件链接，链接类型分为`硬链接`和`符号链接`两种，默认的链接类型是`硬链接`。如果要创建`符号链接`必须使用`-s`选项

*注意：`符号链接`文件不是一个独立的文件，它的许多属性依赖于`源文件`，所以给`符号链接`文件设置`存取权限`是没有意义的*

`软链接`只会在目的位置生成一个文件的`链接文件`，实际不会占用磁盘空间，相当于Windows中的快捷方式。`硬链接`会在目的位置上生成一个和源文件大小相同的文件。无论`软链接`和`硬链接`，文件保持同步变化。

选项

```
-i 覆盖既有文件之前先询问用户
-s 创建符号(软)链接而不是硬链接
```

示例

```shell
#ln 源文件  目标文件(快捷方式)
[budong@budong ~]$ ln py.txt p.hard  #创建硬链接
[budong@budong ~]$ ln -s py.txt p.soft  #创建软链接
[budong@budong ~]$ ll

[budong@budong ~]$ cat py.txt
[budong@budong ~]$ cat p.hard
[budong@budong ~]$ cat p.soft
[budong@budong ~]$ echo 'this' >> py.txt
[budong@budong ~]$ cat py.txt
[budong@budong ~]$ cat p.hard    
[budong@budong ~]$ cat p.soft     #链接会随着文件内容一起变化

[budong@budong ~]$ rm py.txt
[budong@budong ~]$ cat p.hard
[budong@budong ~]$ cat p.soft   #删除源文件之后，硬链接可以继续使用，软链接不行

[budong@budong ~]$ vim py.txt
[budong@budong ~]$ ln -i py.txt p.hard  #可以重新覆盖之前的链接
ln：是否替换"p.hard"？ y
[budong@budong ~]$ vim p.hard   
[budong@budong ~]$ cat py.txt    #改变硬链接内容，文件也会随着一起变化
```



### 5.文件权限

`ls -l` 查看文件信息，别名为`ll`

```shell
[budong@budong bin]$ ll
总用量 5660
-rwxr-xr-x. 1 root root  23408 3月  23 02:46 arch
lrwxrwxrwx. 1 root root      4 8月   5 22:16 awk -> gawk
-rwxr-xr-x. 1 root root  22484 3月  23 02:46 basename
-rwxr-xr-x. 1 root root 872372 3月  23 08:11 bash

#第1列表示文件类型
#第2列表示文件权限
#第3列为硬链接个数
#第4列表示文件所有者，就是文件属于那个用户
#第5列表示文件所属的组
#第6列表示文件大小
#第7列表示文件的修改时间
#第8列表示文件名或目录名
```

`-rwxr-xr-x`这10个字符的确定了文件类型和用户对文件的权限

第1个字符代表文件类型：- 表示普通文件

后面9位每3位为一组 （rwx），读（r），写（w），执行（x）

第1组是`u`(user)所有者的权限：`rwx `代表文件的所有者`root`用户有读、写和执行的权限。 

第2组是`g`(group)所属组的权限：`r-x` 代表与文件所有者在同一组的用户有读和执行的权限

第3组是`o`(other)其他人的权限：`r-x` 代表其他的用户有读和执行权限。

在Linux中，文件有3种属性：`可读`、`可写`和`可执行`。每个文件都有自己的`属主`，每个用户有自己的`用户组`，这样文件权限就有`属主权限`、`同组用户权限`和`不同组用户权限`。

**1.文件的权限**

`读权限`：对应标志为`r`，表示具有读取文件或目录的权限，对应的使用者可以查看文件内容

`写权限`：对应标志为`w`，对应使用者可以变更此文件，如：删除，移动等。写权限依赖于该文件父目录的权限设置，既父目录没有写权限，就算文件有写权限也不行。

`执行权限`：对应标志为`x`，一些可执行文件必须有可执行权限才可以运行，如：py文件。对于目录而言，可执行权限表示其他用户可以进入此目录，如目录没有可执行权限，则其他用户不能进入此目录。

Linux中通过`符号`表示权限之外，也可以通过`数字`来表示权限
`r` 对应数字 `4`， `w` 对应数字 `2`，`x `对应数字 `1 ` 。 那么rwx 就是数字`7`,`0`表示没有任何权限

```shell
[budong@budong bin]$ ll vi
-rwxr-xr-x. 1 root root 845932 12月 22 2016 vi

[budong@budong bin]$ rm vi
rm：是否删除有写保护的普通文件 "vi"？y
rm: 无法删除"vi": 权限不够
#无法删除没有权限的文件
```

**2.改变文件的权限**

`chmod` 可以改变文件或目录的权限

```shell
[budong@budong ~]$ ll py.txt 
-rw-rw-r--. 1 budong budong 0 8月   9 08:17 py.txt

[budong@budong ~]$ chmod u+x  py.txt  #属主既budong增加执行权限
[budong@budong ~]$ chmod g+x  py.txt  #同组增加执行权限
[budong@budong ~]$ chmod o-r  py.txt  #其他用户去掉可读权限 
[budong@budong ~]$ ll py.txt 
-rwxrwx---. 1 budong budong 0 8月   9 08:17 py.txt
#使用数字改变权限
[budong@budong ~]$ chmod 664 py.txt 
[budong@budong ~]$ ll py.txt 
-rw-rw-r--. 1 budong budong 0 8月   9 08:17 py.txt
```

`chown`  将文件变更成新的属主或属组

```shell
[budong@budong tmp]$ ll vi
-rwxr-xr-x. 1 budong budong 845932 8月   9 08:03 vi  #属主和组都是不动

[root@budong tmp]# chown -R root /tmp/vi  #登陆root用户，改成属主为root用户
[root@budong tmp]# ll vi
-rwxr-xr-x. 1 root budong 845932 8月   9 08:03 vi  #属主已改，组没改
```

`chgrp`  改变文件或目录所属的用户组

```shell
[root@budong tmp]#  ll vi
-rwxr-xr-x. 1 root budong 845932 8月   9 08:03 vi #属组为budong
[root@budong tmp]# chgrp root /tmp/vi 
[root@budong tmp]# ll vi
-rwxr-xr-x. 1 root root 845932 8月   9 08:03 vi   #属组已改成root
```



### 6.磁盘管理

`df`命令查看硬盘空间的使用情况

```shell
[budong@budong /]$ df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_budong-lv_root
                       18G  736M   16G   5% /
tmpfs                 504M     0  504M   0% /dev/shm
/dev/sda1             477M   26M  426M   6% /boot
#-h 可读的方式显示当前磁盘空间
```

`du`查看磁盘或某个目录占用的磁盘空间

```shell
[budong@budong bin]$ du -chs
5.6M	.
5.6M	总用量
#c 最后再加上总计  h 打印可识别的格式，如：1KB，500MB，1GB s只显示各档案大小的总和
[budong@budong bin]$ du -ah  #显示全部目录和其次目录下的每个档案所占的磁盘空间
```

