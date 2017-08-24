# SQLAlchemy基本认知

`20:30上课！！！`

##### 1.基本认知

##### 2.工具准备

##### 3.连接数据库

##### 4.声明映像

##### 5.创建和映射类

##### 6.数据操作



### 1.基本认知

[ORM](https://baike.baidu.com/item/ORM/3583252?fr=aladdin)全称 `Object Relational Mapping`对象关系映射。

[SQLAlchemy ](http://www.sqlalchemy.org/)是一个数据库的`ORM`框架，`python`操作数据库的工具`ORM `将数据库中的表与面向对象语言中的类建立了一种对应关系。简单来说，使用`SQLAlchemy`可以不用考虑使用的是什么数据库，只要是用`SQLAlchemy`提供的方式写好语句，`SQLAlchemy`会自动根据你连接的数据库把你写的语句转化成对应的数据库`SQL`语句。



### 2.工具准备

1.`mysql`数据库

2.`pymysql`用于连接`MySQL`服务器的一个库

3.`sqlalchemy`

```shell
$ pip install pymysql
$ pip install sqlalchemy
```



### 3.连接数据库

从sqlalchemy中导入create_engin，创建引擎建立与数据库的连接。

`from sqlalchemy import create_engine`

准备连接数据库的数据：
HOSTNAME = '127.0.0.1'        # ip地址
PORT = '3306'                          # 端口号
DATABASE = 'mydb'                # 数据库名
USERNAME = 'admin'                 # 用户名
PASSWORD = 'rootqwe123'                 # 用户登录密码

DB_URI的格式:
数据库类型+数据库驱动名称://用户名:密码@机器地址:端口号/数据库名?字符编码
DB_URI=`mysql+pymysql://<username>:<password>@<host>/<dbname>?charset=utf8` 

engine = create_engine(DB_URI)

我们可以尝试着测试一下是否连接上:

`print(dir(engine))`,当有打印出方法时，表示连接成功。

```python
#connect.py
from sqlalchemy import create_engine

HOSTNAME = '127.0.0.1'
PORT = '3306'
DATABASE = 'mydb'
USERNAME = 'admin'
PASSWORD = 'Root110qwe'

Db_Uri = 'mysql+pymysql://{}:{}@{}/{}?charset=utf8'.format(USERNAME,PASSWORD,HOSTNAME,DATABASE)

engine = create_engine(Db_Uri)

if __name__=='__main__':
    print(dir(engine))
```



### 4.声明映像

对象关系型映射，数据库中的表与python中的类相对应，创建的类必须继承自sqlalchemy中的基类。

使用Declarative方法定义的映射类依据一个基类，这个基类是维系类和数据表关系的目录。
应用通常只需要有一个base的实例。我们通过declarative_base()功能创建一个基类。

```python
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base(engine)
```



###5.创建和映射类

再次强调，我们用类来表示数据库里面的表！！！

这些表的类都继承于我们的Base基类。在类里面我们定义一些属性，这个属性通过映射，就对应表里面的字段，每个属性定义的时候需要指定属性是属于那
定义好一些属性，与user表中的字段进行映射并且这个属性要属于某个类型

Column用来创建表中的字段的一个方法

```python
#sqlalchemy常用的数据类型
Integer #整形，映射到数据库中的int类型。
String  #字符类型，映射到数据库中的varchar类型，使用时，需要提供一个字符长度。
```

```python
#---创建数据表对应的类---
from sqlalchemy import Column,Integer,String

class User(Base):
    __tablename__ = 'user'
    id = Column(Integer,primary_key=True,autoincrement=True,doc='id')
    name = Column(String(20),nullable=False,doc='用户姓名')
    number = Column(Integer,nullable=False,doc='用户号')

    def __repr__(self):
        return "<User(id='%s',name='%s',number='%s')"%(self.id,self.name,self.number)

#---将创建好的user类，映射到数据库的user表中---
Base.metadata.create_all()
```



### 6.数据操作

之前的操作都是准备工作，提供一个类似与交互模式的环境，让我们能够进行增删改查的操作。

在我们对表数据进行增删改查之前，先需要建立会话，建立会话之后才能进行操作，就类似于文件要打开之后才能对文件内容操作。

**创建会话**

定义个`session`会话对象,使用 `sessionmaker`初始化一个类对象

```python
from sqlalchemy.orm import sessionmaker
Session = sessionmaker(engine)
session = Session()
```

**增**

我们通过sqlalchemy往表里面插入数据，需要先有这表的映射，也就是上面我们创建的类，然后我们通过会话来进行需要的操作。

创建`user`对象添加到会话对象中

添加单个对象：
`session.add(user)`
添加多个对象：
`session.add_all([user1,user2,....])`
将会话对象进行提交:
`session.commit()`
如果你不想将修改提交则使用回滚:`session.rollback()`

```python
def add_user():
    #添加单个对象
    #person = User(name='budong',number=11)
    #session.add(person)

    #添加多个对象
    session.add_all([User(name='tuple',number=2),\
            User(name='which',number=3)])
    #提交才会生效，和命令行有区别
    session.commit()
    
add_user()
```



**查**

`session`会话中的`query`对象可以查找数据。

查找`User`这张表中的所有数据:`session.query(User).all()`
查找`User`这张表中的第一条数据:`session.query(User).first()`
通过`username=?`来进行过滤查找:`session.query(User).filter_by(username='budong')`
通过`get`方法，用主键查找对象:`session.query(User).get(primary_key)`

注意：
`filter` 引用列名时，使用`类名.属性名`的方式，比较使用两个等号`==`
`filter_by `引用列名时，使用`属性名`，比较使用一个等号`=`赋值这种形式

这两个只是用法不一样而已，常用`filter` 

```python
def search_user():
    #查询所有
    rows = session.query(User).all()
    print(rows)

    #查询第一条
    rows = session.query(User).first()
    print(rows)

    #查询number=11的数据
    rows = session.query(User).filter_by(number=11).all()
    print(rows)

    #查询name='budong'的数据
    rows = session.query(User).filter(User.name=='budong').all()
    print(rows)
```



**改**

修改数据是在`filter`里面过滤需要修改的数据，再使用`update`即可。

```python
def update_user():
    rows = session.query(User).filter(User.name=='budong').update({User.number:1})
    session.commit()
```



**删**

我们可以先查出数据，然后再删除，使用`session.delete`方法进行删除.

```python
def delete_user():
    rows = session.query(User).filter(User.name=='which')[0]
    print(rows)
    session.delete(rows)
    session.commit()
```



**总结**

SQLAlchemy是一个ORM，便于我们的代码从一个数据库迁移到另一个数据库，提高代码的复用率。

用SQLAlchemy连接数据库需要大家掌握怎样连接数据库，然后怎样创建会话，创建会话之后怎样才可以进行增删改查的操作，这就是这节课要掌握的内容。



补充vim自动缩进

```shell
#~/下执行
$ sudo yum -y install git
$ git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
$ touch ~/.vimrc
####写到 ~/.vimrc
set nocompatible              " required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
"
" " alternatively, pass a path where Vundle should install plugins
" "call vundle#begin('~/some/path/here')
"
" " let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'
"
" " Add all your plugins here (note older versions of Vundle used Bundle
" instead of Plugin)
"
"
" " All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required

"代码折叠
" Enable folding
set foldmethod=indent
set foldlevel=99
" Enable folding with the spacebar
nnoremap <space> za
Plugin 'tmhedberg/SimpylFold'
let g:SimpylFold_docstring_preview=1

"au BufNewFile,BufRead *.py
set tabstop=4
set softtabstop=4
set shiftwidth=4
set textwidth=79
set expandtab
set autoindent
set fileformat=unix


####最后在vim里面执行   :PluginInstall
```

