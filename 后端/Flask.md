# 安装与配置

## pip镜像

**pip国内镜像：**

- 阿里云 	http://mirrors.aliyun.com/pypi/simple/
- 中国科技大学 	https://pypi.mirrors.ustc.edu.cn/simple/
- 豆瓣(douban)	 http://pypi.douban.com/simple/
- 清华大学	 https://pypi.tuna.tsinghua.edu.cn/simple/
- 中国科学技术大学	 http://pypi.mirrors.ustc.edu.cn/simple/



**临时修改：**

~~~
# 使用-i参数指定pip镜像源
pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
~~~

**永久修改：**

在user目录中创建一个pip目录，如：C:\Users\xx\pip，创建 pip.ini 文件或者 pip.conf 文件

~~~
[global]
index-url=http://pypi.tuna.tsinghua.edu.cn/simple/
[install]
trusted-host=pypi.tuna.tsinghua.edu.cn 
~~~



## 安装Flask

~~~
# 根据需求选择版本
pip install flask==2.2.3
~~~





# debug,host,port 配置



**debug模式的开启：**

![image-20230224193810158](Flask.assets/image-20230224193810158.png)

![image-20230224193801949](Flask.assets/image-20230224193801949.png)





```
# debug 模式
# 开启 debug 模式后，主要修改代码并保存后，就会自动重新加载，不需要手动重启
# 开发时，能看到出错信息
```



**修改 host：**--host=ip地址

- host：当前监听的主机名，让其他电脑能访问我的flask项目

![image-20230224194514876](Flask.assets/image-20230224194514876.png)





**修改 port ：**--port=端口号

- port：监听的端口号

![image-20230224194809668](Flask.assets/image-20230224194809668.png)



# url与视图



## 跨域配置

~~~
pip install flask-cors
~~~





视图函数主要负责逻辑处理的结果展现给用户

- 必须有个函数注册为程序跟地址的处理程序、
- 必须返回一个response对象，所以我们会用到make_response的函数，使返回的数据构建成一个response的对象
- 视图函数：request、data、response

~~~python
from flask import Flask, make_response 
# Flask类只有一个必须指定的参数，即程序主模块或者包的名字，__name__是系统变量，该变量指的是本py文件的文件名
app = Flask(__name__)

# app.route装饰器，把装饰的函数注册为路由
@app.route('/')
def run():  #  视图函数
    return make_response("OK")

~~~



## 路由匹配

- 不带参路由

~~~python
@app.route('/')
def run():  # 视图函数
    res = {
        'code': 0,
        'msg': "OK",
        'data': {
            'test': '测试页面'
        }
    }
    return make_response(res)
~~~

- 带一个参数的路由匹配

~~~python
@app.route('/student/<name>')
def students(name):
    return "HI! %s" % name
~~~

- 多url匹配

~~~python
@app.route('/index')  # http://127.0.0.1:5000/index
@app.route('/<int:page>')  # http://127.0.0.1:5000/1
@app.route('/index/<int:page>')  # http://127.0.0.1:5000/index/15
def index1(page=None):
    if page is None:
        page = 1
    return "当前页数为： %s" % page

# 多路由参数<int:age>表示int类型
@app.route('/student2/<name>/<int:age>')
def students2(name, age):
    return "%s, %s" % (name, age)

~~~



## 请求

- 无参请求

~~~python
from flask import Flask, make_response

# Flask类只有一个必须指定的参数，即程序主模块或者包的名字，__name__是系统变量，该变量指的是本py文件的文件名
app = Flask(__name__)


@app.route('/', methods=['GET', 'POST'])  # 这是路由,定义是GET请求还是POST请求方式
def run():  # 视图函数
    res = {
        'code': 0,
        'msg': "OK",
        'data': {
            'test': '测试页面'
        }
    }
    return make_response(res)

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, debug=False, threaded=True)

~~~

- 带表单**formData**参数的请求：

~~~python
from flask import Flask, make_response, request


@app.route('/', methods=['GET', 'POST'])
def run():
    username = request.form.get('username', type=str)  # form.get()获取表单格式请求体
    password = request.form.get('password', type=int)  # formData格式区分

    if username == 'admin' and password == 123:
        res = {
            'code': 0,
            'msg': "OK",
            'data': {
                'test': '测试页面'
            }
        }
    else:
        res = {
            'code': 999,
        }
    return make_response(res)


~~~



- json格式参数

~~~python
from flask import Flask, make_response, request

@app.route('/', methods=['GET', 'POST'])
def run():
    getJson = request.get_json() # get_json()获取json格式请求体
    username = str(getJson.get('username'))  # json数据格式
    password = int(getJson.get('password'))  # json数据格式

    if username == 'admin' and password == 123:
        res = {
            'code': 0,
            'msg': "OK",
            'data': {
                'test': '测试页面'
            }
        }
    else:
        res = {
            'code': 999,
        }
    return make_response(res)


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, debug=False, threaded=True)

~~~

- 查询字符串的方式传参(更加灵活)

~~~python
# /book/list 此时查询到第一页
#book/list?page=2
@app.route('/book/list')
def book_list():
	page = request.args.get("page",dafaul=1,type=int)
	return f"第{page}页"
~~~

- 单参数

~~~python
@app.route('/student/<name>')
def students(name):
    return "HI! %s" % name
~~~





## 获取请求头

~~~python
# 获取headers内的Host
request.headers.get('Host')
# 获取cookies
request.headers.get('cookies')
~~~

-  request.scheme  # 获取请求方案（协议）
- request.method  # 获取本次请求的请求方式
-  request.args  # 获取使用GET请求方式提交的数据
- request.form  # 获取POST请求方式提交的数据
-  request.values  # 获取GET和POST请求方式提交的数据
-  request.cookies  # 获取COOKIES中的信息
-  request.headers  # 获取请求消息头的信息
- request.path  # 获取请求的URL地址
- request.files  # 获取用户上传的文件
-  request.headers['User-Agent']  # 获取headers中的User-Agent
-  request.url  # 获取URL请求地址



# mysql

## 配置与映射

Flask操作mysql，需要安装 python 操作mysql的驱动。

**常见驱动：**

- MySQL-python:也就是MySQLdb。是对C语言操作 MySQL数据库的一个简单封装。遵循了Python-DB-API v2。但是只支持Python2。

- mysqlclient:是 MySQL-python的另外一个分支。支持Python3并且修复了一些bug。是目前为止执行效率最高的驱动，但是安装的时候容易因为环境问题出错
- pymysql:纯 Python实现的一个驱动。因为是纯 Python编写的，因此执行效率不如 mysqlclient。也正因为是纯 Python写的，因此可以和 Python代码无缝衔接
- mysql-connector-python: MySQL官方推出的纯 Python连接MySQL的驱动，执行效率pymysql还慢

**ORM技术：**

借助  flask-sqlalchemy ，sqlalchemy可以提供ORM技术，可以帮助我们像操作普通Python对象一样实现数据库的增删改查。flask-sqlalchemy是对sqlalchemy的一个封装，使得在flask中使用sqlalchemy更加方便。flask-sqlalchemy需要单独安装，因为flask-sqlalchemy依赖aqlalchemy，所以只要安装了flask-sqlalchemy，sqlalchemy就会自动安装。

~~~python
# 以安装 pymysqle 为例子
pip install pymysqle
# 安装flask-sqlalchemy
pip install flask-sqlalchemy
~~~

**实现：**

~~~python
# 导入数据库连接
from sqlalchemy import create_engine, Column, Integer, String, DateTime, Table, \
    MetaData, or_, func, and_
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker, session, scoped_session

~~~

配置文件settings.py中设置好连接数据库的信息，然后使用SQLAlchemy(app) 创建一个db对象，SQLAlchemy会自动读取app.config中连接数据库的信息。

~~~python
class Configs:
    ENV='development'
    DEBUG=True
    # 设置连接数据库路径
    SQLALCHEMY_DATABASE_URI='mysql+pymysql://{USERNAME}:{PASSWORD}@{HOSTNAME}:{PORT}/{DATABASE}?charset=utf8mb4'
    # 每次请求结束后自动提交数据库中的改动
    SQLALCHEMY_COMMIT_ON_TEARDOWN=True
    # 禁用SQLAlchemy对追踪对象的修改并且发送信号
    SQLALCHEMY_TRACK_MODIFICATIONS = False
    # 操作数据库时显示原始SQL语句
    SQLALCHEMY_ECHO=True
~~~



将查询结果转换为json格式：

~~~python
from sqlalchemy.ext.declarative import DeclarativeMeta
import json
class AlchemyEncoder(json.JSONEncoder):
    def default(self, obj):
        if isinstance(obj.__class__, DeclarativeMeta):
            # an SQLAlchemy class
            fields = {}
            for field in [x for x in dir(obj) if not x.startswith('_') and x != 'metadata']:
                data = obj.__getattribute__(field)
                try:
                    json.dumps(data)     # this will fail on non-encodable values, like other classes
                    fields[field] = data
                except TypeError:
                    fields[field] = None
            # a json-encodable dict
            return fields
        return json.JSONEncoder.default(self, obj)


def class_to_dict(obj):
    is_list = obj.__class__ == [].__class__
    is_set = obj.__class__ == set().__class__
    if is_list or is_set:
        obj_arr = []
        for o in obj:
            dict = {}
            a = o.__dict__
            if "_sa_instance_state" in a:
                del a['_sa_instance_state']
            dict.update(a)
            obj_arr.append(dict)
        return obj_arr
    else:
        dict = {}
        a = obj.__dict__
        if "_sa_instance_state" in a:
            del a['_sa_instance_state']
        dict.update(a)
        return dict
~~~





编写数据模型和映射对象：

已经建好表，自动加载映射：

~~~python
from sqlalchemy.ext.automap import automap_base

from sqlalchemy.orm import Session

url = 'mysql+pymysql://medical:7TdHGp4Ei8iLWnFK@1.116.162.146:3306/medical?charset=utf8mb4'
# url = Configs.SQLALCHEMY_DATABASE_URI

# engine = create_engine(url, echo=True,
#                     pool_size=5,
#                    pool_recycle=7200)


# ③：基于ORM的反射
# Base = automap_base()
# Base.prepare(engine, reflect=True)

# print(Base.classes.keys())  # 获取所有的对象名

# 获取表对象
# Img = Base.classes.img
# User = Base.classes.user
# Patient = Base.classes.patient
# Department = Base.classes.department
# Record = Base.classes.record
#
# session_img = Session(engine)
# session_user = Session(engine)
# session_patient = Session(engine)
# session_department = Session(engine)
# session_Record = Session(engine)
~~~

~~~python
from db_mapper.db_model import cursor,session_user,User
from view.Util import class_to_dict,RandomNum
#
#
# # 根据账号查找用户
def getUserByPhone(phone):
    res = session_user.query(User).filter(User.phone == phone).first()
    if res is not None:
        res = class_to_dict(res)
    return res




# # 新建用户
def createUser(phone,username,password):
    user = User()
    user.phone = phone
    user.username = username
    user.password = password
    user.work_no = RandomNum()
    try:
        session_user.add(user)
        session_user.commit()
    except Exception as e:
        session_user.rollback()
        print(e)


~~~



建表映射：

~~~python
from flask import Flask
import settings
from flask_sqlalchemy import SQLAlchemy
from datetime import datetime
app = Flask(__name__)
 
app.config.from_object(settings.Configs)  # 加载flask项目配置
 
db=SQLAlchemy(app)    # 创建映射对象,并绑定在app中
 
# 创建模型类
class User(db.Model):
    #设置表名
    __tablename__ = 'user'
    # 创建数据库表字段
    # db.Column(类型，约束)
    id=db.Column(db.Integer,primary_key=True,autoincrement=True)
    username=db.Column(db.String(15),nullable=False)
    password=db.Column(db.String(15),nullable=False)
    phone=db.Column(db.String(11),unique=True)
    rdatetime=db.Column(db.DateTime,default=datetime.now)
 
if __name__ == '__main__':
    app.run()
~~~

`db.create.all()`

- db.create_all()方法将寻找所有db.Model的数据模型类，根据数据模型类创建数据表



## connection

传统的connection的方式连接和操作数据库：使用原生sql

~~~sql
from sqlalchemy import create_engine
 
# 数据库连接字符串
DB_CONNECT_STRING = 'sqlite:///:memory:'
 
# 创建数据库引擎,echo为True,会打印所有的sql语句
engine = create_engine(DB_CONNECT_STRING, echo=True)
 
# 创建一个connection，这里的使用方式与python自带的sqlite的使用方式类似
with engine.connect() as con:
    # 执行sql语句，如果是增删改，则直接生效，不需要commit
    rs = con.execute('SELECT 5')
    data = rs.fetchone()[0]
    print "Data: %s" % data
~~~



**connection事务**

~~~sql
from sqlalchemy import create_engine
 
# 数据库连接字符串
DB_CONNECT_STRING = 'sqlite:Users/zhengxiankai/Desktop/Document/db.sqlite'
engine = create_engine(DB_CONNECT_STRING, echo=True)
 
with engine.connect() as connection:
    trans = connection.begin()
    try:
        r1 = connection.execute("select * from User")
        r2 = connection.execute("insert into User(name, age) values(?, ?)", 'bomo', 24)
        trans.commit()
    except:
        trans.rollback()
        raise
~~~



## 插入数据

**插入单条数据：**

~~~python
user1=User(username='zhagsan',password='1245',phone='13700100000')  #创建实例对象
db.session.add(user1)  #插入数据
db.session.commit()   #提交事务
~~~

- 首先创建一个模型类的实例对象，再使用db.session.add()将实例对象中的参数插入在数据库中，最后使用db.session.comit()来保存我们对数据库的修改。

**插入多条数据：**

~~~python
user1=User(username='huan',password='123',phone='13700000000')  #创建模型类实例对象
user2=User(username='ying',password='456',phone='13700010000')
user3=User(username='xue',password='789',phone='13700020000')
db.session.add_all([user1, user2, user3])        #插入数据
db.session.commit()            #提交事务
~~~

- 插入多条数据与插入单条数据差不多，插入多条数据也就是创建多个模型类的实例对象，再将实例对象放在列表中，再使用db.session.add_all()方法插入在数据库中，最后使用db.session.comit()来保存我们对数据库的修改。



## 查询数据

**查询全部数据：**

~~~python
User.query.all() 
~~~

**精确查询：**

~~~
User.query.filter_by(username=='huan').first()  #查询名为huan的数据
~~~

- 通过User.query.filter_by()方法进行查询，然后通过first()方法来返回第一个查询结果。这里我们选择的是查询名为huan的数据。

  当数据中没有我们要查询的数据，则会返回None。

**模糊查询：**

~~~python
User.query.filter(or_(User.username.like('z%'), User.username.contains('i'))).all()   
---------->      select * from user where username like 'z%' or username like '%i%';

User.query.filter(or_(User.username.like('z%'), User.username.contains('i'))).all()   
---------->      select * from user where username like 'z%' or username like '%i%';


User.query.filter(User.phone.endswith('3000')).first()  #获取手机尾号为3000的数据
User.query.filter(User.name!='huan').all()       #获取用户名不是huan的所有数据
 
#导入and方法，实现逻辑与查询
from sqlalchemy import and_
User.query.filter(and_(User.name!='huan',User.phone.endswith('3000'))).all()  #获取用户名不是huan以及手机尾号为3000
 
#导入or方法，实现逻辑或查询
from sqlalchemy import or_
User.query.filter(or_(User.name!='huan',User.email.endswith('3000'))).all()   #获取用户名不是huan或手机尾号为3000
 
#导入not，实现取反查询
from sqlalchemy import not_
User.query.filter(not_(User.name=='huan')).all()  #获取用户名不是huan的所有数据

模型类.属性.startswith()    #以什么开头的数据
模型类.属性.endswith()    #以什么结尾的数据
模型类.属性.contains()    #数据包含什么什么

# 当我们查询的字段为整型或日期类型时，还可以使用以下代码：(当然，也可以直接使用> 、< 、=符号来表示)
模型类.属性.__lt__(18)                #小于18
模型类.属性.__gt__(18)        #大于18
模型类.属性.__ge__(18)        #大于等于18
模型类.属性.__le__(18)        #小于等于18
模型类.属性.between(18，30)       #18到30之间
~~~

- 通过User.query.filter()方法进行查询
- 通过User.phone.endswith()方法来选定查询以什么结尾的数据内容
- first()方法返回第一个查询结果
- all()方法获取查询的全部数据返回的是一个列表



**主键查询：**

~~~python
# 通过数据中的主键来查询数据
User.query.get(1)

# 选择返回数据的条数
User.query.limit(2).all()     #获取所有数据中前两条数据
User.query.offset(2).limit(2).all()    #跳过所有数据中前两条数据再获取跳过后的前两条数据
~~~

- 通过主键来查询数据是使用.get()方法其参数为键值，如果主键不存在没有返回内容
- 使用User.query.limit()方法来返回数据的条数，其传递的参数就是返回数据的条数



**join连接查询：**

~~~python
db.session.query(主表模型字段1, 主表模型字段2, 从表模型字段1, xx.. ).join(从表模型类, 主表模型类.主键 == 从表模型类.外键)

# 使用join语句优化关联查询
# 查询姓名为"张三"的用户id和地址信息
adrs = Address.query.join(User, Address.user_id == User.id).filter(User.name == '张三').all()  # 列表中包含地址模型对象
~~~



**排序：**

~~~python
#模型类.query.order_by(参数)
User.query.order_by(User.id).all    #对所有的id进行排序
~~~

- query默认情况下是按id升序进行排序的，当遇到复杂情况时就需要时用order_by
- 当我们想要倒序排列数据时，只要在前面加



## 修改数据

先创建一个变量来接收要修改的数据，通过该变量来修改对应的属性值，最后通过db.session.commit()方法提交事务

~~~python
user = User.query.first() #选定要修改的数据
user.username = 'xiu'  #要修改的参数值
db.session.commit()   #提交事务

# 将数据全部修改
user = User.query.all()  #选择所有要修改的数据
for i in user:      #循环遍历
    i.name = 'dong'     #要修改的参数值
    db.session.commit()    #提交事务
~~~

- 通过User.query.all()来获取所有数据并存放在user变量中，注意all()返回的时一个列表，所以要用for循环来遍历，再根据自身需要来修改数据，最后通过db.session.commit()方法提交事务。



## 删除数据

**逻辑删除：**

~~~python
变量名 = 模型类.query.get(xx)        # 通过主键获取数据对象
# 在表中定义一个名为isdelete的字段值为布尔类型，当对数据对象进行删除时，将其值改为True，在进行查询显示是增加对isdelete字段的筛选
变量名.isdelete = True   
db.session.commit()        # 提交修改
~~~

**物理删除：**

~~~python
变量名 = 模型类.query.get(id)        # 通过主键获取数据对象
db.session.delete(变量名)        # 类似于缓存删除数据
db.session.commit()        # 提交删除
~~~



































































































