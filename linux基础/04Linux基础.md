# Linux基础

`20:30上课！！！`

#### 1.Linux进程管理

#### 2.程序安装与管理



### 1.Linux进程管理

**1.进程的概念**

计算机实际上可以做的事情实质上非常简单，比如计算两个数的和，再比如在内存中寻找到某个地址等等。这些最基础的计算机动作被称为指令 (instruction)。所谓的程序(program)，就是这样一系列指令的所构成的集合。通过程序，我们可以让计算机完成复杂的操作。程序大多数时候被存储为可执行的文件。这样一个可执行文件就像是一个菜谱，计算机可以按照菜谱作出可口的饭菜。

[进程](http://www.cnblogs.com/vamei/archive/2012/09/20/2694466.html)是程序的一个具体实现。大家可以简单的理解为进程就是正在进行中的程序。一般来说，一个程序就是一个进程，但是很多情况下，程序为啦完成更多的事情，往往一个程序有多个进程。

**2.进程监视**

`ps`提供进程的一次性查看

选项

```
u 按用户和启动时间的顺序来显示进程
a 显示所有用户的所有进程
x 显示无终端控制的进程
f 列出进程全部相关信息，通常和其他选项联用
e 所有进程
```

演示

```shell
[budong@budong etc]$ ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.1   2900  1400 ?        Ss   Aug08   0:01 /sbin/init
root         2  0.0  0.0      0     0 ?        S    Aug08   0:00 [kthreadd]

[budong@budong etc]$ ps -ef|grep budong
#USER: 启动进程的用户
#PID: 进程的ID号
#%CPU: 进程占用的CPU百分比
#%MEM: 进程占用的物理内存百分比
#VSN: 进程使用的虚拟内存总量，单位KB
#RSS:该进程占用实际物理内存的大小，单位KB
#TTY: 该进程在哪个终端中运行
#STAT: 进程状态
#START: 启动进程的时间
#TIME: 进程消耗CPU的时间
#COMMAND: 产生此进程的命令名
#STAT常见状态：
#R 运行， S 睡眠，T 停止，s 包含子进程，+ 位于后台 Z僵尸进程  < 优先级比较高的进程
```

`top` 监视系统实时状态

选项

```
-d 设置更新的时间间隔
-n 显示更新的次数，然后退出
-u 只显示指定用户的进程信息
-s 安全模式运行，禁用一些交互命令
```

演示

```shell
[budong@budong ~]$ top -d 2
top - 09:36:12 up 17:34,  2 users,  load average: 0.00, 0.00, 0.00
Tasks:  76 total,   1 running,  75 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.0%us,  0.0%sy,  0.0%ni,100.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:   1030372k total,   326728k used,   703644k free,    33636k buffers
Swap:  2064380k total,        0k used,  2064380k free,   216784k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                                          
    1 root      20   0  2900 1400 1204 S  0.0  0.1   0:01.28 init                                                              
    2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd  
#第1行分别是：系统时间、系统启动了17小时34分、登录的用户有2个、 “load average” 代表：最近1分钟，5分钟，15分钟的系统负载值，如超过CPU个数的2倍说明高负载，需处理

#第2行Tasks表示：有76个进程在内存中，1个正在运行，75个睡眠，0个停止，0个处于僵尸状态

#第3行Cpu(s)表示：user上花费的时间，sys上花费的时间，nice优先级调整上花费的时间，idle：CPU空闲时间，iowait等待系统io时间，hi硬件中断时间，si软中断时间，steal被虚拟机偷掉的时间

#第4行Mem表示：总内存、已经使用的内存、空闲的内存、用于缓存文件系统的内存、交换内存的总容量，这内存还可以用free命令来查看

#第5行Swap表示：交换空间总大小、使用的交换内存空间、空闲的交换空间、用于缓存文件内容的交换空间
```

`top`命令的交互模式当中可以执行的命令

```
h  显示交互模式的帮助
P  以CPU使用率排序，由大到小，默认
M  按内存占有大小排序，由大到小
N  以进程ID大小排序，由大到小
q  退出top程序
```

大家可以使用`htop`来查看，界面上更加好看

`yum -y install epel-release`

`yum install htop`

Linux 的进程分为前台进程和后台进程，`前台进程`占用`终端窗口`，而`后台进程`不占用`终端窗口。要启动一个前台进程，只需要在命令行输入启动进程的命令即可，要让一个程序在后台运行，只需要在启动进程时，在命令后加上`&`符号即可。

`Ctrl + Z` 让正在前台执行的进程暂停

`jobs` 获取当前的后台作业号

`fg`将进程从后台调到前台执行
`bg` 将进程放到后台执行

`kill`删除执行中的程序

常用的信号量：

```
HUP  1  终端断线 
INT  2  中断（同 Ctrl + C）
QUIT 3  退出（同 Ctrl + \） 
TERM 15 终止
KILL 9  强制终止 
CONT 18 继续（与STOP相反， fg/bg命令） 
STOP 19 暂停（同 Ctrl + Z）
```

`kill -9 进程id `  杀死进程

一般在命令之后加“&”，就可以放到后台执行。



### 2.程序安装与管理

`RPM`包管理机制最早是有`Red Hat`公司研制，然后由开源社区维护，是`centos`上主要的软件包管理机制。

`yum`  基于`rpm`的软件包管理器，可以自动处理依赖关系

如用`yum`安装`python3`

```shell
[budong@budong ~]$ python -V
Python 2.6.6
[budong@budong ~]$ sudo yum -y install epel-release  #添加源
[budong@budong ~]$ sudo yum -y install python34  #安装python3.4
#如果想卸载安装的软件可以使用 sudo yum remove python34  来移除安装的软件
```

另外使用`yum list`查看所有能用`yum`安装的包,使用`yum list|grep python`可以查看和`python`有关的包。

使用`yum`可以很方便的安装的软件，如`MySQL`等等，但是`yum`源一般比较的旧，不是特别的新，我们可以使用[163源](http://mirrors.163.com/.help/centos.html)，这样下载的时候会快一些，软件版本也会高一点。

```shell
[root@budong /]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup   #先备份
[root@budong yum.repos.d]# yum install wget
[root@budong yum.repos.d]# cd /etc/yum.repos.d
[root@budong yum.repos.d]# wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
[root@budong yum.repos.d]# yum clean all
[root@budong yum.repos.d]# yum makecache
#按照以上步骤就可安装163源
```

`yum`源很方便，但是有时候我们需要安装高版本的软件的时候，`yum`安装就满足不了我们要求，这个时候我们可以去下载`rpm`包，然后用`rpm`包去安装。

我们用这个方法来安装`MySQL5.7`

```shell
[budong@budong tools]$ rpm -qa|grep mariadb*    #首先删除已有的mariadb，mysql数据库，

[root@budong tools]# rpm -e --nodeps mysql-libs-5.1.73-8.el6_8.i686
#使用rpm -e --nodeps mariadb-...   删除安装的数据库

[budong@budong tools]$ wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.19-1.el6.i686.rpm-bundle.tar
#即下载群文件进阶软件10，在虚拟机里面下载很慢，大家可以打开这个链接用迅雷去下,下载地址：https://dev.mysql.com/downloads/mysql/

[budong@budong tools]$ sudo yum install lrzsz
#大家从群文件里面下载之后，安装这个，就可以直接从主机拖到虚拟机里面

[budong@budong tools]$ tar -xvf 10mysql-5.7.19-1.el6.i686.rpm-bundle.tar  #解压

[budong@budong tools]$ sudo yum -y install numactl #安装一个依赖

#必须要按照下面的顺序来，不然就会安装不上
[budong@budong tools]$ sudo rpm -ivh mysql-community-common-5.7.19-1.el6.i686.rpm 
[budong@budong tools]$ sudo rpm -ivh mysql-community-libs-5.7.19-1.el6.i686.rpm 
[budong@budong tools]$ sudo rpm -ivh mysql-community-client-5.7.19-1.el6.i686.rpm
[budong@budong tools]$ sudo rpm -ivh mysql-community-server-5.7.19-1.el6.i686.rpm

#切换root身份，数据库初始化，也可以一开始就切换成root
[root@budong tools]# mysqld --initialize --user=mysql

#查看root密码  xormlHYlC1&o  就是自己生成的root密码
[root@budong tools]# cat /var/log/mysqld.log | grep 'password' 
2017-08-09T12:49:51.364886Z 1 [Note] A temporary password is generated for root@localhost: xormlHYlC1&o  

#启动MySQL服务
[root@budong tools]# service mysqld start  

#登陆
[root@budong tools]# mysql -u root -p

#修改 root 密码
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'qwe123';

#退出
mysql> \q

#修改配置文件
[root@budong tools]# vim /etc/my.cnf
[mysqld]

datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
character_set_server = utf8

symbolic-links=0

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

[client]
default-character-set=utf8
socket=/var/lib/mysql/mysql.sock

[mysql]
default-character-set = utf8

#重启服务
[root@budong tools]# service mysqld restart

#清理安装
[root@budong tools]# mysql_secure_installation
此时输入 root 密码，接下来，为了安全，MySQL 会提示你密码为级别，重置 root 密码，移除其他用户账号，禁用 root 远程登录，移除 test 数据库，重新加载 privilege 表格等，你只需输入 y 继续执行即可。如果root密码改啦的话就是输no，其他的地方一直y就可以啦。至此，整个 MySQL 安装完成。
#Press y|Y for Yes, any other key for No: y  确定后面的密码级别
#Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0 最小级别，如果是后面的级别的话，以后新建用户的时候设置密码必须要满足选定的级别
#Change the password for root ? ((Press y|Y for Yes, any other key for No) : n 是不是改root密码，我这里就不改啦
#接下来全部都是y


#设置开机启动
[root@budong tools]# chkconfig --levels 235 mysqld on

#再次登陆MySQL
[root@budong tools]# mysql -u root -p

#查看字符集
mysql> SHOW VARIABLES LIKE 'character%';

#创建管理员用户 因为root用户限制不能远程登陆
mysql> CREATE USER 'admin'@'%' IDENTIFIED BY 'rootqwe123';

#给这个用户授予所有的远程访问的权限。这个用户主要用于管理整个数据库、备份、还原等操作。
mysql> GRANT ALL  ON *.* TO 'admin'@'%';

#使更改立即生效
mysql> FLUSH PRIVILEGES;

#创建普通用户
mysql> CREATE USER 'develop'@'%' IDENTIFIED BY 'QWEqwe123';  #这里的%代表可以远程登陆

#给这个用户授予 SELECT,INSERT,UPDATE,DELETE 的远程访问的权限，这个账号一般用于提供给实施的系统访问
mysql> GRANT SELECT,INSERT,UPDATE,DELETE  ON *.* TO 'develop'@'%';

#使更改立即生效
mysql> FLUSH PRIVILEGES;

#退出
mysql> \q

#以上就是在centos6.9上用rpm安装MySQL5.7.19的方法，注意这个方法适用5.7.5之后的版本
```

`rpm`总结

```shell
rpm -qa  |grep mysql  #查看已安装的某个软件
rpm -e mysql-...  #删除某个安装包
rpm -ivh mysql...  #安装某个安装包

#选项
-a 显示安装的所有软件列表
-e 从系统中移除指定的软件包
-h 安装软件时输出hash记号：#
-i 安装软件时显示软件包的相关信息
-l 显示软件包中的列表
-v 安装软件时显示命令的执行过程
-q 使用询问模式，当遇到任何问题时，rpm指令会先询问用户
-p 查询软件包的文件
```

这个再给大家用yum安装MySQL的方法，大家参考一下，但是yum安装的版本比较低。

```shell
linux上的mysqld的yum安装，yum安装的是MySQL5.1的版本，参考一下

#查看已经安装的mysql文件
rpm -qa | grep mysql

#查看可以按的RPM包
yum list | grep ^mysql

#安装mysql开发包及mysql服务端
sudo yum install mysql-devel mysql-server

#启动mysql：
sudo service mysqld start
#会出现非常多的信息，目的是对mysql数据库进行初始化操作

#查看mysql服务是否开机自启动
chkconfig --list | grep mysqld

#如果没有启动，可以通过下面语句来启动：
sudo chkconfig mysqld on

#通过命令给root账号设置密码为root
#(注意：这个root账号是mysql的root账号，非Linux的root账号)
mysqladmin -u root password 'root'

#通过 mysql -u root -p 命令来登录我们的mysql数据库了

#进入mysql,查看编码集
SHOW VARIABLES LIKE '%char%';

#查看校对集
SHOW VARIABLES LIKE '%colla%';

#改配置文件：
#查看服务的状态
service mysqld status
#关闭服务
sudo service mysqld stop

#进入文件
sudo vi /etc/my.cnf

# 往里面加进进去的内容
# 服务端
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci

# 客户端：
[client]
default-character-set=utf8

#重启服务
sudo service mysqld restart
```

在`Linux`上除了`yum`和`rpm`安装之外，还有源码包的安装，这里以`python3`的源码安装为例

```shell
#首先卸载之前安装好的python34
[budong@budong tools]$ sudo yum remove python34

#首先安装一些依赖，为后面的编译做准备
[budong@budong tools]$ sudo yum install -y openssl-static
[budong@budong tools]$ sudo yum install -y gcc
[budong@budong tools]$ sudo yum groupinstall "Development tools"
sudo yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel

#下载python源码包
[budong@budong tools]$ wget http://python.org/ftp/python/3.6.1/Python-3.6.1.tar.xz
#下载会有点慢，可以先在Windows上用迅雷下好，然后像上面MySQL的包一样，直接拖进来也可以

#解压文件
[budong@budong tools]$ tar -xvf Python-3.6.1.tar.xz

#进入解压后的文件夹
[budong@budong tools]$ cd Python-3.6.1

#配置 配置文件
[budong@budong Python-3.6.1]$ ./configure --prefix=/usr/local/python3

#编译
[budong@budong Python-3.6.1]$ make

#安装
[budong@budong Python-3.6.1]$ sudo make install

#添加python3的符号链接
[budong@budong Python-3.6.1]$ sudo ln -s /usr/local/python3/bin/python3 /usr/bin/python3

#添加pip3的符号链接
[budong@budong Python-3.6.1]$ sudo ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3

#看看效果
[budong@budong Python-3.6.1]$ pip3 -V
pip 9.0.1 from /usr/local/python3/lib/python3.6/site-packages (python 3.6)

#编译安装完成
```

在Linux上，不是所有的有的软件都能通过`yum`或者`rpm`等其他类似的包管理工具就能安装好，很多时候需要自己去编译安装，各种软件的编译安装所需要的依赖也不一样，但是在网上都可以找到资料，大家安装的时候可以先找找资料然后多操作几次就行。



补充一点python内容：

这里我们用python的时候默认是python2，使用python3需要每次都加个3，虽然也可以重新链接，让python指向的是python3，但是这样会影响系统的一些程序运行，这是时候我们可以建立一个python3的虚拟环境，在虚拟环境里面使用python就是python3，具体操作如下：

```shell
#Linux上的python2不自带pip，需要安装操作如下：
[budong@budong ~]$ wget https://bootstrap.pypa.io/get-pip.py
[budong@budong ~]$ sudo python get-pip.py   
#这样pyhton2的pip就安装上啦，yum安装python3时，python3 get-pip.py就会安装python3的pip

#下载python的虚拟环境安装包
[budong@budong ~]$ sudo pip install virtualenv

#创建虚拟环境
[budong@budong ~]$ virtualenv  -p /usr/bin/python3  py3env

#进入虚拟环境
[budong@budong ~]$ source py3env/bin/activate
(py3env) [budong@budong ~]$ python

#退出虚拟环境
(py3env) [budong@budong ~]$ deactivate

#把  source py3env/bin/activate  添加到  .bashrc  中，可以一登陆就进入啦python3的虚拟环境

#如果要添加python2的虚拟环境只需把上面的python3改成python2即可，或者如下
[budong@budong ~]$ virtualenv  env_py2
#因为系统默认是python2，所以可以不用添加命令的路径
```

