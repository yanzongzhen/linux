# SQLAlchemy查询

`20:30上课！！！`

##### 1.查询结果

##### 2.过滤条件

##### 3.Column常用参数

##### 4.sqlalchemy常用数据类型



### 1.查询结果

我们使用`session`中的`query`可以查出数据，但是我们对返回的结果还不太熟悉，我们有必要了解一下返回的结果，这样才能方便我们取数。

```python
#返回一个对象，就好像函数没有加括号一样   
    rs = session.query(User).filter(User.name=='budong')
    print('***',rs,type(rs))

#这里加上0和加上first()是一样的，返回一个User对象，属性值就是数据
    rs = session.query(User).filter(User.name=='budong')[0]
    print('aaa',rs,type(rs))
    rs = session.query(User).filter(User.name=='budong').first()
    print('aaabbb',rs,type(rs))

#和上面的返回的对象一样，但是值放在列表里面，对于对象的值，我们可以使用getattr方法来获取    
    rs = session.query(User).filter(User.name=='budong').all()
    print('bbb',rs,type(rs))
    print('++++++',getattr(rs[0],'name'))

#和最开始类似    
    rs = session.query(User.number).filter(User.name=='budong')
    print('ccc',rs,type(rs))

#这里的[0]也就是first()，但是结果不是一个对象，是一个元组，元组的值就是查询出的数据 
    rs = session.query(User.number).filter(User.name=='budong')[0]
    print('ddd',rs,type(rs))

#把上面的结果放到列表里面，同时是查询所有符合条件的结果    
    rs = session.query(User.number).filter(User.name=='budong').all()
    print('eee',rs,type(rs))
```



### 2.过滤条件

`filter`和`filter_by`都可以过滤，结果都一样，只是用法有点不一样：

`filter `引用列名时，使用“类名.属性名”的方式，比较使用两个等号`==`

`filter_by` 引用列名时，使用“属性名”，比较使用一个等号`=`

`filter`就相当与我们`MySQL`里面的`where`，很多`where`里面的判断条件也是可以用的，具体写法参考于下：

```python
#等于
rows = session.query(User.number).filter(User.name=='budong').all()

#不等于
rows = session.query(User.number).filter(User.name!='budong').all()
#filter_by里面不能用!= 还有> < 等等，所有filter用得更多,filter_by只能用=

#模糊匹配like
rows = session.query(User.number).filter(User.name.like('budong%')).all()

#成员属于 in_
rows = session.query(User.number).filter(User.name.in_(['budong','tuple'])).all()

#成员不属于 notin_
rows = session.query(User.number).filter(User.name.notin_(['budong'])).all()

#为空
rows = session.query(User.number).filter(User.name==None).all()
#或者
rows = session.query(User.number).filter(User.name.is_(None)).all()

#不为空
rows = session.query(User.number).filter(User.name!=None).all()
#或者
rows = session.query(User.number).filter(User.name.isnot(None)).all()

#多个条件
rows = session.query(User.number).filter(User.name.isnot(None),User.number!=0).all()

#选择条件or_
from sqlalchemy import or_
rows = session.query(User.number).filter(or_(User.name=='budong',User.number==2)).all()
```



###3.Column常用参数

`Column`声明类属性时使用，也即数据库表里面的字段，常用的参数如下：

1. primary_key：主键，True和False。
2. autoincrement：是否自动增长，True和False。
3. unique：是否唯一。
4. nullable：是否可空，默认是True。
5. default：默认值。



###4.SQLAlchemy常用数据类型

sqlalchemy中也有很多的数据类型，这些数据类型和我们数据库里面的各种数据类型相对应。常用的如下：

1. Integer：整型，映射到数据库中的int类型。
2. String：字符类型，映射到数据库中的varchar类型，使用时，需要提供一个字符长度。
3. Text：文本类型，映射到数据库中的text类型。
4. Boolean：布尔类型，映射到数据库中的tinyint类型，在使用的时候，传递True/False进去。
5. Date：日期类型，没有时间。映射到数据库中是date类型，在使用的时候，传递datetime.date()进去。
6. DateTime：日期时间类型。映射到数据库中的是datetime类型，在使用的时候，传递datetime.datetime()进去。
7. Float：浮点类型。

```python
from user import session,Base
from datetime import datetime,date
from sqlalchemy import Column,Integer,String,Boolean,Date,DateTime

class Column_test(Base):
    __tablename__ = 'column_test'
    id = Column(Integer,primary_key=True,autoincrement=True)
    name = Column(String(20),nullable=False,unique=True)
    gender = Column(Boolean)
    start_time = Column(DateTime,default=datetime.now())
    birth_time = Column(Date)

Base.metadata.create_all()

haha = Column_test(name='haha',gender=True,birth_time=date(2017,8,22))
session.add(haha)
session.commit()
```



### 总结

在`SQLAlchemy`中的查询和我们写原生的`SQL`是类似的，我们理解了在`MySQL`中如何书写，在`SQLAlchemy`中思维是一样的，只不过表达方式有些区别，大家学会使用就行。