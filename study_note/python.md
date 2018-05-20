# 1. 基础
## 1.1 输入
```
print(input("shuru"))
```

## 1.2 数据基本类型
### 1.2.1 获取类型
```
a = ""
print(type(a))
```
## 1.3 占位符
```
c="{}test{}" #占位符
print(c.format("a","b")) #有几个占位符，传几个参数
```

## 1.4 引号
```
print("ab'c")
print('ab"c') #这是有单引号和双引号的原因，不用再转义
print("""ab
"asdf
d""") #三个引号时随便放
```

## 1.5 字符串
```
print("abcde"[1]) #字符串取位数

print(" a ".strip()) #去空格

print("ab" in "abc") #判断第一个字符串是否在第二个里面

d = "a,b,c,d".split(",") #得到的是列表
print(type(d))
```

## 1.6 列表
- 和前面的字符串一样，list(a)也有自己的一些操作方法：
- 列表的长度(列表中元素的个数): len(a)
- 向列表中追加数据：a.append("传智")
- 列表的索引(由于列表的数据是有位置的，所以我们可以根据位置取值):
- a[1]    和字符串一样，索引的位置都是从0开始的，即1表示列表中的第二个数据
- a[2:5]  从第二个开始到第五个之前的数据
- a[2:5:2]  从第二个开始到第五个之前每隔1个去一个值,最后一个2表示步长
- a[5:2:-2]  结果和前一个数据的顺序相反，因为步长是-2，即倒着取值
- a[::-1] 结果是a中的数据顺序相反，应为步长是-1
- 列表的扩展(两个列表相加): [1,2]+[3,4]  或者 a.extend([3,4])
- 确定一个值在列表中的第一次出现的位置 : [1,2,3,1].index(1)
- 在列表确定的位置插入一个值：[1,2,3].insert(0,1)
- 从列表中删除一个值(仅删除第一次出现的那个位置)：[1,2,3,1].remove(1)
- 那么问题来了，去哪里查询呢？ 一个新的工具，ipython； pip install ipython
  - ipython
```
l = [1,"abc",True,[1,"a"]] #列表里的数据用，隔开
print(l[3])
print(l[::-1])
```

## 1.7 pip
- pip：包管理工具,能够通过它去安装第三方的模块
- pip的是属那个环境的,通过`pip --version`查看

## 1.8 字典
- 字典用大括号括起来，里面的数据是键值对，成对出现的: {"xiaoming":20,"xiaohong":23,"xiaogang":32}
- a = {} 定义一个空字典
- a["xiaoming"] = 10  让a中的键xiaoming为10
- a["xiaoming"]  去a中键为xiaoming的值
- a["xiaoming"] = 20  更新a中键为小明的值为20
- b = a.pop("xia-oming")  删除a中键xiaoming，同时b为小明的值
- c = {"xiaohong":32}  a.update(c) 把c中的键值对更新到a中
- a["xiaohua"] = [1,2,3]
- a["xiaohua"] = {"age":18,"grade":3}

## 1.9 if
```
money = 3
if money > 6:
	print(">")
elif 3 < money <= 6:
	print("<<=")
else:
	print("<")
```

## 1.10 for
```
for i in [1,2,3]:
	print(i)

for i in range(1,10): # 1-9, range(10)  0-10
	print(i)
```

## 1.11 while
```
while money < 10:
	print("money{}".format(money))
	money += 1
```

## 1.12 break、continue
- break表示终止循环
- continue表示退出当前循环
```
for i in range(6):
	if i < 3:
		print("continuea{}".format(i))
		continue #略过下面的语句
		print("continueb{}".format(i))
	if i == 4:
		print("break1")
		break #整个for结束
		print("break2")
```

## 1.13 函数
- 先定义后使用
```
def my_print(a,b,c):
	return a+b, c
	print("return")
a,b = my_print("1","2","3")
print (a,b)
```

## 1.14 全局变量、局部变量
- 全局变量在函数中可以引用，但是不能修改，
- 在函数中出现变量名 = 数据意味着变量名为局部变量
- 如果需要在函数中修改全部变量，需要使用global 来声明全局变量
```
g = 100
def test1():
	g = 30
	print g
def test2():
	print g
def test3():
	global g # 声明g是全局变量
	g = 20
	print(g)
def test4():
	print(g)
test1() #30
test2() #100
test3() #20
test4() #20
```
## 1.15 模块
### 1.15.1 2种方式导入
- 方式一
```
import test1
test1.function1()
```
- 方式二
```
from test1 import function1 #方式二
function1()
```

## 1.16 类、__init__(self)、实例化类
```
class Cat:
	def __init__(self, a, b):
		print("init")

	def func1(self): #类中的方法中的self必须写，调用不用传参，如果方法中需要穿参数，在self,再加参数
		print("f1")

	def func2(self):
		print("f2")

if __name__ == "__main__":
	cat = Cat("a","b")
	cat.func1()
	cat.func2()
```

## 1.17 异常处理
```
try:
	print(3/0)
except:
	print("error")

try:
	print(3/0)
except Exception as e:
	print(e)
```


## 1.18 编码、bytes
```
a = "中国"
print(type(a))
# a.encode() #编码
# a.decode() #解码
```

# 2. pip
- pip：包管理工具,能够通过它去安装第三方的模块
- pip的是属那个环境的,通过`pip --version`查看
## 2.1 linux 安装pip
```
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate
python get-pip.py
```
pip -V #查看pip版本

# 3. json
```
import json
a = {"a":1,"b":2}
print(type(a)) #字典
b = json.dumps(a) #python数据类型转成json字符串
c = json.loads(b) #转成python数据类型
print(type(b))
print(type(c))
print(b)
print(c)
```

# 4 requests
- [requests 模块中文档](http://docs.python-requests.org/zh_CN/latest/index.html)
## 4.1 request请求
```
import requests #不能导入，但是在window上已经安装（解决：file-》setting-》project interpreter,点击+ ，安装requests）
url = "http://www.baidu.com"
r = requests.get(url)

#方式一
r.encoding = 'utf-8'
print(r.text)

#方式二
r.content.decode('utf-8')
print(r.content)
```

## 4.2 request请求添加head
```
headers = {"User-Agent":"Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.132 Safari/537.36"}
r = requests.get(url, headers=headers)
print(r.content)
```

## 4.3 pycharm无法导入requests
cmd --》`pip install requests`
在pycharm中：file-》setting-》project interpreter,点击+ ，安装requests

## 4.4 下载

### 4.4.1 下载图片、视频到本地

```
import urllib.request

# url = "http://sc4.hao123img.com/data/5684bc23a81e0a8c74e872414e69dfc6_0"
url = "http://vd3.bdstatic.com/mda-iamci8rkne0sbasv/mda-iamci8rkne0sbasv.mp4"
data = urllib.request.urlopen(url).read()
# 保存图片到本地
# file = open('D:\study\code\python_workspace\hello_python\zz\com\download\dir_name/'+ 'n.png', 'wb')
# 保存视频到本地
file = open('D:\study\code\python_workspace\hello_python\zz\com\download\dir_name/'+ '1.mp4', 'wb')
file.write(data)
file.close()
```



# 5. 工作中的python
## 5.1 绑定本地出口IP
- 背景：在三线服务器上上传文件到云服务，用其中联通的上传
- 参考地址：[绑定本地出口IP](http://vearne.cc/archives/323)

## 5.2 url编码解码
- mardown引用本地上传到github上的图片(图片名含有中文)，地址中图片名需要编码拼接地址才能访问
- python实现
```
import urllib

print(urllib.quote("rsyslog版本过高异常.png"))

rawurl = "rsyslog%E7%89%88%E6%9C%AC%E8%BF%87%E9%AB%98%E5%BC%82%E5%B8%B8.png"
print(urllib.unquote(rawurl))
```





# 6. flask
- [flask中文文档](http://docs.jinkan.org/docs/flask/)
- 安装 `pip install flask`

## 6.1 URL与视图
### 6.1.1 创建Flask项目
- 在pycharm中file --》 new project --》 Flask

- flask详细代码
  ```
  #encoding: utf-8

  # 从flask这个框架中导入Flask这个类
  from flask import Flask

  # 初始化一个flask对象
  # Flask()
  # 需要传一个参数 __name__
  # 1. 方便flask框架去寻找资源
  # 2. 方便flask插件比如Flask0Sqlalchemy出现错误的时候， 好去寻找问题所在位置
  app = Flask(__name__)

  # @app.route是一个装饰器
  # @开头并且在函数上面，说明是装饰器
  # 这个装饰器的作用是做一个url与视图函数的映射
  # 127.0.0.1:5000/  ->  去请求hello_world 这个函数，然后将结果返回浏览器
  @app.route('/')
  def hello_world():
      return 'Hello World!'

  if __name__ == '__main__':
      # 启动一个应用服务器，来接收用户的请求
      # while True:
      #     listen()
      app.run()
  ```

### 6.1.2 设置debug模式
- 在app.run()中传入debug=True；即：`app.run(debug=True)`
- debug2大功能
  - 当程序有问题的时候，在页面可以看到错误信息
  - 只要修改了python文件，程序会自动加载，不需要手动重新启动服务

### 6.1.3 使用配置文件
- 新建一个`config.py`文件
- 在app文件中导入这个文件，并配置到app中(取代上面的`app.run(debug=True)`)
  ```
  import config
  app.config.from_object(config)
  ```

### 6.1.3 url传参
- 在flask中是参数
  ```
  @app.route('/article/<id>') # 参数格式放在<>中
  def article(id):    #视图函数中需要放和url中同名的参数
      return u'您请求的参数是：%s' % id
  ```
- 请求
  http://127.0.0.1:5000/article/1

### 6.1.4 反转url
- 在页面重定向，指定不同的参数，加载不同的数据
  ```
  from flask import Flask,url_for

  @app.route('/')
  def hello_world():
      a = 1
      b = 0
      # c = a / b
      print url_for('my_list')
      print url_for('article', id='123')
      return 'Hello World!'

  @app.route('/list/')
  def my_list():
      return 'list'

  @app.route('/article/<id>') # 参数格式放在<>中
  def article(id):    #视图函数中需要放和url中同名的参数
      return u'您请求的参数是：%s' % id
  ```
  上面请求`http://127.0.0.1:5000/` 会打印list  和  您请求的参数是123
- 在模版中也会使用反转
  ```
  <body>
  	<a herf="{{ url_for('login') }}" >
  	<a herf="{{ url_for('logout') }}" >注销</a>
  </body>
  ```

### 6.1.5 反转 和 重定向
- 如果用户没有登录可重定向到登录页面
  ```
  from flask import Flask,redirect,url_for

  def hello_world():
      return redirect('login')    #找的是@app.route里的
      
      login_url = url_for('login')
      return redirect(login_url)    #找的是方法

  @app.route('/login/')
  def login():
      return 'login'
  ```


## 6.2 Jinja2模版
### 6.2.1 模版渲染、参数传递
1. 如何渲染模版
- 模版凡在templates文件夹下
- 从flask中导入render_template
- 在视图中使用render_template
2. 模版传参
- 如果只有一个或少数参数，直接在render_template 函数添加关键字即可
- 如果多个，可以先放入字典中，然后再render_template 中使用`**字典`，把字典转换成关键参数传递进去
  ```
  from flask import Flask,url_for,redirect,render_template

  @app.route('/')
  def hello_world():

      class Person(object):
          name = u'张政'
          age = 18
      p = Person()
    
  	#方式一
      # return render_template('index.html', username=u'张政')

  	##方式二、三、四
      content = {
          'username': u'张政',
          'gender': u'男',
          'age': 18,

          'person': p,

          'websites': {
              'baidu': u'bd',
              'guge': u'ge'
          }
      }
      #默认是从templates下加载，如果index.html不在templates根下，如templates/another, 就要变成another/index.html
      return render_template('index.html', **content)
  ```
- templates下的index.html
  ```
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
      这是html
      <p>用户名：{{ username }}</p>
      <p>用户名：{{ gender }}</p>
      <p>用户名：{{ age }}</p>

      <p>名字：{{ person.name }}</p>
      <p>名字：{{ person.age }}</p>

      <p>百度：{{ websites.baidu }}</p>
      <p>谷歌：{{ websites['guge'] }}</p>
  </body>
  </html>
  ```
### 6.2.2 if判断
- 和python中相差无几，可以使用 > < <= >= == != 进行判断，也可以使用and or not （） 来进行逻辑合并操作
  ```
  @app.route('/<is_login>/')
  def is_l(is_login):
      if is_login == '1':
          user = {
              'username': u'zz'
          }
          return render_template('index.html', user=user)
      else:
          return render_template('index.html')
  ```
- index.html
  ```
  <body>
      这是html
      {% if user %}
          <a href="#">{{ user.username }}</a>
      {% else %}
          <a href="#">登录</a>
      {% endif %}
  </body>
  ```
### 6.2.3 for遍历
1. 字典遍历和python一样，可以使用`items()` `keys()` `values()` `iteriterms()` `iterkeys()` `itervalues()`

  ```
  @app.route('/')
  def hello_world():
      user = {
          'username': u'zz',
          'age': 18
      }
      for k,v in user.items():
          print(k)
          print(v)
      return render_template('index.html', user = user)
  ```
- index.html
  ```
  <body>
    {% for k,v in user.items() %}
        <p>{{ k }}: {{ v }}</p>
    {% endfor %}
  </body>
  ```
2. 列表的遍历
  ```
      websits = {
          'baidu': u'bd',
          'guge': u'gg'
      }
      books = [
          {
              'name': u'西游记',
              'author': u'吴承恩',
              'price': 100
          },
          {
              'name': u'红楼梦',
              'author': u'曹雪芹',
              'price': 200
          }
          ,
          {
              'name': u'三国演义',
              'author': u'罗贯中',
              'price': 300
          }
      ]

      for k,v in user.items():
          print(k)
          print(v)
      return render_template('index.html', websits = websits, books = books)
  ```
- index.html
  ```
  <body>
      {% for websit in websits %}
          <p>{{ websit }}</p>
      {% endfor %}

      <table>
          <thead>
             <th>书名</th>
             <th>作者</th>
             <th>价格 </th>
          </thead>
          <tbody>
              {% for book in books %}
                  <tr>
                      <td>{{ book.name }}</td>
                      <td>{{ book.author }}</td>
                      <td>{{ book.price }}</td>
                  </tr>
              {% endfor %}
          </tbody>
      </table>
  </body>
  ```

### 6.2.4 过滤器

  ```
# app.py
return render_template('index.html', avatar = "http://img1.imgtn.bdimg.com/it/u=594559231,2167829292&fm=27&gp=0.jpg")

# index.html
<img src="{{ avatar }}">
  ```

  ```
# app.py
return render_template('index.html')

# index.html
# 没有默认显示default的
<img src="{{ avatar|default('http://img.zcool.cn/community/01058a556895750000012716d39e4e.jpg@3000w_1l_2o_100sh.jpg') }}">#}
  ```

  ```
# app.py
books = [
    {
        'name': u'西游记',
        'author': u'吴承恩',
        'price': 100
    },
    {
        'name': u'红楼梦',
        'author': u'曹雪芹',
        'price': 200
    }
    ,
    {
        'name': u'三国演义',
        'author': u'罗贯中',
        'price': 300
    }
]
return render_template('index.html', books = books)

# index.html
# 获取字典个数
<p>评论数：{{ books|length }}</p>
  ```

![](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/python/flask%E8%BF%87%E6%BB%A4%E5%99%A8.png?raw=true)

### 2.2.5 继承 和 block 

1. 继承作用 和 语法

   - 作用：可以把一些公用的代码写到模版中，避免每个模版写一遍
   - 语法：

   ```
   {% extends 'base.html' %}
   ```

2. block 实现

   - 作用：可以让子模块实现一些自己的需求，父模版中中需要提前定义好
   - 注意点：字模版中的代码必须在block快中

   ```
   # base.html
   {% block main %}
   {% endblock %}

   # login.html
   {% extends 'base.html' %}
   {% block main %}
   	<h1>login页面</h1>
   {% endblock %}
   ```

### 6.2.6 URL链接 和 加载静态文件

1. 语法：`url_for('static', filename='路径')`

2. 静态文件，flask会从static文件夹中开始寻找，所以不用写static

3. 可以加载css、js、image文件

   ```
   # 加载css文件
   <link rel="stylesheet" href="{{ url_for('static', filename='css/index.css' }}">

   # 加载
   <scrip src="stylesheet" href="{{ url_for('static', filename='css/index.css' }}"></scrip>

   # 加载图片文件
   <img src="stylesheet" href="{{ url_for('static', filename='css/index.css' }}" alt="">
   ```

## 6.3 SQLAlchemy数据库
### 6.3.1 MySQL-python 中间件介绍与安装
- 如果再类unix系统上，直接进入虚拟环境安装：`sudo pip install mysql-python`
- 如果在window上python2.7
  - 那么在这`http://www.lfd.uci.edu/~gohlke/pythonlibs#mysql-python` 下载 `MySQL_python‑1.2.5‑cp27‑none‑win_amd64.whl`
  - 进入到 `MySQL_python‑1.2.5‑cp27‑none‑win_amd64.whl` 所在目录，进行安装
    `pip install MySQL_python-1.2.5-cp27-none-win_amd64.whl`
  - 下载安装`mysqlclient-1.3.12-cp27-cp27m-win_amd64.wh 和上面一样
- 针对python3.6版本，参考一下博客 ：http://blog.csdn.net/c406495762/article/details/56279888 
- 注意：用虚拟环境中的pip 来安装，这样在pycharm中就可以直接使用这些模块了


### 6.3.2 ORM与flask_sqlalchemy介绍 

- ORM：Object Relationship Mapping 模型关系映射。
- flask-sqlalchemy是一套ORM框架。
- ORM的好处：可以让我们操作数据库跟操作对象是一样的，非常方便。
  因为一个表就抽象成一个类，一条数据就抽象成该类的一个对象。 
- 安装“flask-sqlalchemy“：'sudo pip install flask-sqlalchemy'。 


### 6.3.3 flask-SQLAlchemy的使用
- 设置配置信息：在‘config.py'文件中添加以下配置。
  ```
  #encoding: utf-8

  DEBUG = True

  #dialect+driver://username:password@host:port/database
  DIALECT='mysql'
  DRIVER='mysqldb'
  USERNAME='root'
  PASSWORD='root'
  HOST='127.0.0.1'
  PORT='3306'
  DATABASE='mytest'
  SQLALCHEMY_DATABASE_URI="{}+{}://{}:{}@{}:{}/{}?charset=utf8".format(DIALECT,DRIVER,USERNAME,PASSWORD,HOST,PORT,DATABASE)

  SQLALCHEMY_TRACK_MODIFICATIONS=False
  ```

- 使用flask_sqlalchemy中的SQLAlchemy进行初始化。
  ```
  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  import config

  app = Flask(__name__)
  app.config.from_object(config)
  db = SQLAlchemy(app)

  db.create_all()
  ```
- 启动如果没有问题则说明ok


### 6.3.4 SQLAlchemy模型与表映射
- 模型必须要继承自`db.Model`,然后需要映射到表中的属性，必须写成 `db.Column`的数据类型。
  - `db.Integer`代表的是整形。
  - `db.String`代表的是`varchar`，需要指定最长的长度。
  - `db.Text`代表的是`text`

- 数据类型：
  - `primary_key`: 将这个字段设置为主键。
  - `autoincreme`：代表这个主键为自增长的。
  - `nullable`：代表的这个字段是否可以为空，默认为空，可以将这个值设置为`False`，在数据库中，这个值不能为空了。
  - 最后，需要调用`db.creat_all()`来将模型真正的创建到数据库中。

- db_demo.py
  ```
  #encoding: utf-8

  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  import config

  app = Flask(__name__)
  app.config.from_object(config)
  db = SQLAlchemy(app)

  class Article(db.Model):
      __tablename__ = 'article'
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
      title = db.Column(db.String(100), nullable=False)
      content = db.Column(db.Text, nullable=False)

  db.create_all()

  @app.route('/')
  def index():
      return 'index'

  if __name__ == '__main__':
      app.run()
  ```

- 数据库mytest中创建article表


### 6.3.5 数据增删查改 

- 增、删、改、查

  ```
  #encoding: utf-8

  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  import config

  app = Flask(__name__)
  app.config.from_object(config)
  db = SQLAlchemy(app)

  class Article(db.Model):
      #创建表
      __tablename__ = 'article'
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
      title = db.Column(db.String(100), nullable=False)
      content = db.Column(db.Text, nullable=False)

  db.create_all()

  @app.route('/')
  def index():
      # 增加
      article = Article(title='dd', content='ee')
      db.session.add(article)
      # 事务
      db.session.commit()
      
      # 查
      article1 = Article.query.filter(Article.title == 'aaa').first()
      # 使用first，如果为空，则返回none；如果使用
      # Article.query.filter(Article.title == 'aaa')[0]
      print('title:%s' % article1.title)
      print('content:%s' % article1.content)
      
      # 改
      # （1）先把你要更改的数据查找出来；
      article1 = Article.query.filter(Article.title == 'aaa').first()
      # （2）把这条数据，你需要修改的地方进行修改。
      article1.title = 'newtitle'
      # （3）做事务的提交。
      db.session.commit()
      
      # 删
      # 1.把需要删除的数据找出来
      article1 = Article.query.filter(Article.content == 'bbb').first()
      # 2.把这条数据删除掉
      db.session.delete(article1)
      # 3.做事务提交
      db.session.commit()
      return 'HelloWorld!'

  if __name__ == '__main__':
      app.run()
  ```

### 6.3.6 外键 

- 创建表、插入数据、查询

  ```
  #encoding: utf-8

  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  import config

  app = Flask(__name__)
  app.config.from_object(config)
  db = SQLAlchemy(app)

  class User(db.Model):
      __tablename__='user'
      id=db.Column(db.Integer,primary_key=True,autoincrement=True)
      username=db.Column(db.String(100),nullable=False)
      # 只会映射一次，第一次映射后再执行不会再增加字段的
      #password=db.Column(db.String(100),nullable=False)

  class Article(db.Model):
      __tablename__='article'
      id=db.Column(db.Integer,primary_key=True,autoincrement=True)
      title=db.Column(db.String(100),nullable=False)
      content =db.Column(db.Text,nullable=False)
      author_id=db.Column(db.Integer,db.ForeignKey('user.id'))
      

  db.create_all()

  @app.route('/')
  def index():
      #外键，文章依赖用户，先添加一个用户，再添加一篇文章
      user1 = User(username='zhiliao')
      db.session.add(user1)
      db.session.commit()
      # 添加文章的记录
      article = Article(title='aaa', content='bbb', author_id=1)
      db.session.add(article)
      db.session.commit()
      
      
      #查询title为aaa的用户的名字
      article = Article.query.filter(Article.title == 'aaa').first()
      author_id = article.author_id
      user = User.query.filter(User.id == author_id).first()
      print(user.username)
      
      return 'HelloWorld!'

  if __name__ == '__main__':
      app.run()
  ```

- 使用映射的方式
  - 在上面的基础上，在类Aticle中加入下面映射
    ```
    #反向引用，relationship 关系，第一个参数要和模型名一样，
    author = db.relationship('User', backref=db.backref('articles'))
    ```
    > - 给'Article'这个模型添加一个’ author‘属性, 可以访问这篇文章作者的数据，就像访问普通模型一样
    > - ‘backref’是定义反向引用，可以通过‘User.articles’访问这个模型所写的所有文章

  - 使用映射插入数据、查询数据
    ```
    @app.route('/')
    def index():

        # 使用引用的方式插入、查询数据
        article = Article(title='nnn', content='mmm')
        article.author = User.query.filter(User.id == 1).first()
        db.session.add(article)
        db.session.commit()
        
        # 查询id=1的作者的文章   
        user = User.query.filter(User.id == 1).first()
        print(user.articles[0].title)

        # 查询标题为aaa的作者
        article = Article.query.filter(Article.title == 'aaa').first()
        print 'username: %s' % article.author.username

        return 'HelloWorld!'
        
    if __name__ == '__main__':
        app.run()
    ```


### 6.3.7 多对多
- 多对多的关系，要通过一个中间表进行关联。
- 中间表，不能通过‘class’的方式实现，只能通过‘db.Table'的方式实现。
- 设置关联：`tags=db.relationship('Tag',secondary=article_tag,backref=db.backref('articles'))`
- 需要使用一个关键字参数“secondary=中间表来进行关联
```
#encoding: utf-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
import confi
app = Flask(__name__)
app.config.from_object(config)
db = SQLAlchemy(app)

# 多表关联
# 中间表, 需要使用一个关键字参数“secondary=中间表来进行关联
article_tag = db.Table('article_tag',
                       db.Column('article_id', db.Integer, db.ForeignKey('article.id'), primary_key=True),
                       db.Column('tag_id', db.Integer, db.ForeignKey('tag.id'), primary_key=True)
                       )
# 文章模型
class Article(db.Model):
    __tablename__ = 'article'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    title = db.Column(db.String(100), nullable=False)
    tags = db.relationship('Tag', secondary=article_tag, backref=db.backref('article')
# tag模型
class Tag(db.Model):
    __tablename__ = 'tag'
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    name = db.Column(db.String(100), nullable=False
db.create_all(
@app.route('/')
def index():
# create table article(
#     id int primary key autoincrement,
#     title varchar(100) not null,
# )
# create table tag(
#     id int primary key autoincrement,
#     name varchar(100) not null,
# )
# create table article_tag(
#     article_id int,
#     tag_id int,
#     primary key('article_id', 'tag_id'),
#     foreign key 'article_id' refernces 'article.id',
#     foreign key 'tag_id' refernces 'tag.id',
# 
	#插入数据
    article1=Article(title='aaa')
    article2=Article(title='bbb')
  
    tag1=Tag(name='111')
    tag2=Tag(name='222')
  
    article1.tags.append(tag1)
    article1.tags.append(tag2)
    article2.tags.append(tag1)
    article2.tags.append(tag2)
  
    db.session.add(article1)
    db.session.add(article2)
    db.session.add(tag1)
    db.session.add(tag2)
  
    db.session.commit(
	#数据查询
    artcle = Article.query.filter(Article.title == 'aaa').first()
    for tag in artcle.tags:
        print("tag is %s" % tag.name)
  
    return 'HelloWorld!
if __name__ == '__main__':
    app.run()
```

## 6.4 flask_Script

### 6.4.1 flask_Script讲解 
- Flask-Script的作用是可以通过命令行的形式来操作flask，例如 通过命令行跑一个开发版本的服务器、设置数据库、定时任务等。
- 安装：首先进入虚拟环境中，然后’ pip install flask-script‘。
- 如果直接在主’ manage.py‘中写命令，那么在终端就只需要`python manage.py command_name`就可以了。
- 如果把一些命令集中在一个文件中，那么在终端就需要输入一个父命令，比如 `python manage.py db init`

### 6.4.2 代码
- flask_script_demo.py
  ```
  #encoding: utf-8

  from flask import Flask

  app = Flask(__name__)

  @app.route('/')
  def hello_world():
      return 'hello world'

  if(__name__ == '__main__'):
      app.run()
  ```

- manage.py
  ```
  #encoding: utf-8

  from flask_script import Manager
  from flask_script_demo import app
  from db_script import DBmanager

  #需要引入一个app
  manager = Manager(app)

  @manager.command
  def runserver():
      print('server start ...')
      # print('服务器开始运行...')

  #db为数据库脚本文件的前缀。DBmanager为从数据库脚本中导入的命令行
  manager.add_command('db',DBmanager)

  if(__name__ == '__main__'):
      manager.run()
  ```

- db_script.py
  ```
  #encoding: utf-8

  from flask_script import Manager

  #已有Manager,不需要再引入app
  DBmanager = Manager()

  @DBmanager.command
  def init():
      print('db init ...')

  @DBmanager.command
  def migrate():
      print('db migrate ...')
  ```

### 6.4.3 执行命令

- 命令在manager里
  ```
  #命令 python manage.py runserver
  D:\study\code\python_workspace\environment_python2.7.14\Scripts>python.exe D:\study\code\python_workspace\hello_flask\manage.py runserver

  #结果
  fwq...
  ```

- 子命令，命令在 `db_script` 里
  ```
  # 命令 python manager.py db init
  D:\study\code\python_workspace\environment_python2.7.14\Scripts>python.exe D:\study\code\python_workspace\hello_flask\manage.py db init

  # 结果
  db init ...
  ```


## 6.5 分开model解决循环引用 

### 6.5.1 循环引用例子

- models_sep.py
  ```
  #encoding: utf-8

  from flask import Flask
  from flask_sqlalchemy import SQLAlchemy
  from models import Article

  app = Flask(__name__)
  db = SQLAlchemy(app)

  db.create_all()

  @app.route('/')
  def hello_world():
      return 'hello world'

  if(__name__ == '__main__'):
      app.run()
  ```

- models.py

  ```
  #encoding: utf-8

  from models_sep import db

  class Article(db.Model):
      __tablename__ = 'article'
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
      title = db.Column(db.String(100), nullable=False)
  ```

- 异常信息

  ImportError: cannot import name db

  > models_sep 引用了models， modles又引用了modles_sep

### 6.5.2 分开模型
> - 将db单独分离出来，当主app文件和models文件中需要引用db时，都可以从db中引用 
> - 分开models的目的：为了让代码更加方便的管理 

- exts.py
  ```
  #encoding: utf-8

  from flask_sqlalchemy import SQLAlchemy

  db = SQLAlchemy()
  ```

- models.py
  ```
  #encoding: utf-8

  from exts import db

  class Article(db.Model):
      __tablename__ = 'article'
      id = db.Column(db.Integer, primary_key=True, autoincrement=True)
      title = db.Column(db.String(100), nullable=False)
  ```

- models_sep.py
  ```
  #encoding: utf-8

  from flask import Flask
  from models import Article
  from exts import db
  import config

  app = Flask(__name__)
  app.config.from_object(config)
  db.init_app(app)

  # 方法一
  app.app_context().push()
  db.create_all()
  # 方法二
  with app.app_context():
      db.create_all()

  @app.route('/')
  def hello_world():
      return 'hello world'

  if(__name__ == '__main__'):
      app.run()
  ```

- 这样再运行上面的 `models_sep.py` 就不会再报错了


## 6.6 flask_ migrate

- 因为采用 `db.create_all` 在后期修改字段的时候，不会自动的映射到数据库中，
  必须删除表，然后重新运行 `db.create_all` 才会重新映射，这样不符合我们的需求，
  因此flask-migrate就是为了解决这个问题，她在每次修改模型后，可以将修改的东西映射到数据库中。 

- 首先进入你的虚拟环境中，然后使用 `pip install flask-migrate` 进行安装就可以了。 

- 使用`flask_migrate`必须借助`flask_script` 这个包 的`MigrateCommand` 中包含了所用与数据库相关的命令

- `flask_migrate` 相关命令：
  - 初始化一个迁移脚本的环境，只需要执行一次。 
  ```
    python manage.py db init
    ​```
  - 将模型生成迁移文件，只要模型更改了，就需要执行一遍这个命令。 
  ```
    python manage.py db migrate
    ```
  - 将迁移文件真正的映射到数据库中，每次运行了’ migrate‘命令后，就记得要运
    行这个命令。 
    ```
    python manage.py db upgrade
    ```

    ```

- 注意点：需要将你想要的映射到数据库中模型，都导入到’ manage.py‘文件中，如果没有导入进去，就不会映射到数据库中。

- 代码
  - models.py
    ```
    #encoding: utf-8

    from exts import db

    class Article(db.Model):
        __tablename__ = 'article'
        id = db.Column(db.Integer, primary_key=True, autoincrement=True)
        title = db.Column(db.String(100), nullable=False)
        tag = db.Column(db.String(100), nullable=False)
    ```
  - exts.py
    ```
    #encoding: utf-8

    from flask_sqlalchemy import SQLAlchemy

    db = SQLAlchemy()
    ```
  - manage.py
    ```
    #encoding: utf-8

    from flask_script import Manager
    from migrate_demo import app
    from flask_migrate import Migrate,MigrateCommand # MigrateCommand真正做数据库迁移的
    from exts import db
    from models import Article #要导入模型

    #需要引入一个app
    manager = Manager(app)

    # 1. 要使用flask_migrate 必须要绑定app和db
    migrate = Migrate(app, db)
    # 2. 把MigrateCommand命令添加到Manager中
    manager.add_command('db', MigrateCommand)

    @manager.command
    def runserver():
        print('server start ...')
        # print('服务器开始运行...')
    ```


    if(__name__ == '__main__'):
        manager.run()
    ​```
  - migrate_demo.py
    ```
    #encoding: utf-8

    from flask import Flask
    from models import Article
    from exts import db
    import config

    app = Flask(__name__)
    app.config.from_object(config)
    db.init_app(app)

    @app.route('/')
    def hello_world():
        return 'hello world'

    if(__name__ == '__main__'):
        app.run()
    ```

- 命令
  - 到项目manage.py目录下，例如下面这个项目：
    `cd D:\study\code\python_workspace\hello_flask\`
  - init
    ```
    D:\study\code\python_workspace\hello_flask>D:\study\code\python_workspace\environment_python2.7.14\Scripts\python.exe manage.py db init
    ```
  - migrate
    ```
    D:\study\code\python_workspace\hello_flask>D:\study\code\python_workspace\environment_python2.7.14\Scripts\python.exe manage.py db migrate
    ```
  - upgrade
    ```
    D:\study\code\python_workspace\hello_flask>D:\study\code\python_workspace\environment_python2.7.14\Scripts\python.exe manage.py db upgrade
    ```

- 这样就如果再想增加某个模型的字段

  - 可以直接修改 `models.py` 增加字段，然后再执行`migrate` 和 `upgrade`
  - init只在初始化时执行一次就行了
  - 这样就不需要代码：`db.create_all()`


## 6.7 session 和 cookie
### 6.7.1 cookie 
- 在网站中,http请求是无状态的。也就是说即使第一次和服务器连接后井且登录成功后,第二次请求服务器依然不能知道当前请求是嘟个用户


- cookie的出现就是为了解决这个问题,第一次登录后服务器返回一些数据( cookie)给浏览器,然后浏览器保存在本地,当该用户发送第二次请求的时候,就会自动的把上次请求存储的cook1e数据自动的携带给服务器,服务器通过浏览器携带的数据就能判断当前用户是哪个了。
- cookie存储的数据量有限,不同的浏览器有不同的存储大小,但一般不超过4KB。因此使用cookie只能存储一些小量的数据

### 6.7.2 session
- session和 cookie的作用有点类似,都是为了存储用户相关的信息。
- 不同的是, cookie是存储在本地浏览器，而session存储在服务器。
- 存储在服务器的数据会更加的安全,不容易被窃取。但存储在服务器也有一定的弊端,就是会占用服务器的资源
- 但现在服务器已经发展至今,一些session信息还是绰绰有余的。

### 6.7.3 cookie和 session结合使用
web开发发展至今, cookie和 session的使用已经出现了一些非常成熟的方案。在如今的市场或者企业里,一般有两种存储方式
- 存储在服务端:通过cookie存储一个 session_id,然后具体的数据则是保存在 session中。如果用户已经登录,则服务器会在coke中保存一个 session id,下次再次请求的时候,会把该 session_id携带上来,服务器根据session_id在 session库中获取用户的 session数据。就能知道该用户到底是谁,以及之前保存的一些状态信息。这种专业术语叫做 server side session
- 将sess1on数据加密,然后存储在cok1e中。这种专业术语叫做client side session；flask采用的就是这种方式,但是也可以替换成其他形式

### 6.7.4 flask中的session工作机制
- flask中的 session机制是:把敏感数据圣过加密后放入 session`中,然后再把' session`存放到cookie`中,下次请求的时候,再从浏览器发送过来的 cookie中读取 session',然后再从 session中读取敏感数据,并进行解密,获取最终的用户数据。
- f1ask的这种 sessi0n`机制,可以节省服务器的开销,因为把所有的信息都存储到了客户端(浏览器)
- 安全是相对的,把sess⊥on`放到 cookie中,经过加密,也是比较安全的,这点大家放心使用就可以了

### 6.7.5 fask中使用 cookie和 session
- cookies:在F1ask中操作 cookie,是通过 response对象来操作,可以在 response返回之前,通过 response set cookie来设置,这个方法有以下几个参数需要注意
  - key:设置的 cookie的key
  - value:key对应的 value
  - max-age:改 cookie的过期时间,如果不设置。则浏宽器关闭后就会自动过期。
  - expires:过期时间,应该是一个 datetime类型
  - domain:该 cookie在哪个域名中有效。一般设置子短名,比如cms, earp1e,con
  - path:该c0oke在哪个路径下有效
- sessicn
  - F1ask中的session 是通过 `from flask import session` 然后添加值key和vave进去即可。并且,F1ask中的session机制是将 session信息加密,然后存储在cookie中



### 6.7.6 操作session
- session的操作方式
- 使用` session'需要从 flask中导入' session',以后所有和 session 相关的操作都是通过这个变量来的。
- 使用session 需要设置 `SECRET_KEY` ,用来作为加密用的。并且这个 SECRET_KEY 如果每次服务器启动后都变化的话,那么之前的 session`就不能再通过当前这个 SECRET_KEY 进行解密了
  - 操作 sesson的时候,跟操作字典是一样的。
  - 添加 `session['username'] = 'zhiliao'`
  - 删除; `session.pop('username')` 或者 del session[" username]
  - 清除所有 session : `session.clear()`
  - 获取' session: `session.get('username')`
- 设置 session的过期时间
  - 如果没有指定 session的过期时间,那么默认是浏览器关闭后就自动结束
  - 如果设置了session 的 permanent属性为True,那么过期时间是31天。
  - 可以通过给'app, config'设置 `PERMANENT_SESSION_LIFETIME` 来更改过期时间,这个值的数据类型是 `datetime.timedelta` 类型

### 6.7.7 代码
- session_demo.py
  ```
  #encoding: utf-8

  from flask import Flask, session
  import os
  from datetime import timedelta

  app = Flask(__name__)
  app.config['SECRET_KEY'] = os.urandom(24)
  app.config['PERMANENT_SESSION_LIFETIME'] = timedelta(days=7) # sessiion过期时间

  # 操作session和操作字典一样

  @app.route('/')
  def hello_world():
      #如果没有指定session时间，默认是浏览器关闭自动结束
      session['username'] = 'zhiliao'
      # 如果permanent 设置成true，那么过期时间是31天(没有设置 PERMANENT_SESSION_LIFETIME 的情况下)
      # 如果设置 PERMANENT_SESSION_LIFETIME 则会到这个配置里去找对应的 session 过期时间
      session.permanent = True
      return 'hello world'

  @app.route('/get/')
  def get():
      # session['username']
      # session.get('username')
      print(session.get('username'))
      return 'success'

  @app.route('/delete/')
  def delete():
      print(session.get('username'))
      session.pop('username')
      print(session.get('username'))
      return 'success'

  @app.route('/clear/')
  def clear():
      print(session.get('username'))
      session.clear()
      print(session.get('username'))
      return 'success'

  if __name__ == '__main__':
      app.run()
  ```

## 6.8 get和post
- 请求和参数获取
  ![](https://github.com/zhangzhengstrive/notebook/blob/master/study_note_access/python/flask_get_post.png?raw=true)
- 代码
  - login.html
    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <form action="{{ url_for('login') }}" method="post">
            <tbody>
                <tr>
                    <td>用户名</td>
                    <td><input type="text" placeholder="请输入用户名" name="username"></td>
                </tr>
                <tr>
                    <td>密码</td>
                    <td><input type="text" placeholder="请输入密码" name="password"></td>
                </tr>
                <tr>
                    <td></td>
                    <td><input type="submit" value="登录"></td>
                </tr>
            </tbody>
        </form>
    </body>
    </html>
    ```
  - get_post_demo.py
    ```
    #encoding: utf-8

    from flask import Flask,render_template,request

    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        return 'hello world'

    @app.route('/search')
    def search():
        # get方式获取参数
        q = request.args.get('q')
        return 'user param is : %s' % q

    @app.route('/login/', methods=['GET', 'POST'])
    def login():
        if request.method == 'GET':
            return render_template('login.html')
        else:
            # post方式获取参数
            username = request.form.get('username')
            password = request.form.get('password')
            print('username', username)
            print('password', password)
            return 'post request'
    ```

## 6.9 全局变量g属性
### 6.9.1 global
- g对象是专门用来保存用户的数据的
- g对象在一次那个请求中的所有的代码的地方，都是可以使用的；第二次请求时对象地址就变了
### 6.9.2 global测试
- utils.py
  ```
  #encoding: utf-8

  from flask import g

  def login_log():
      print(g.username)

  def loginip_log():
      print(g.ip)
  ```

- g_demo.py
  ```
  #encoding: utf-8

  from flask import Flask,render_template,request,g
  from utils import *

  app = Flask(__name__)

  @app.route('/login/', methods=['GET', 'POST'])
  def login():
      if request.method == 'GET':
          return render_template('login.html')
      else:
          username = request.form.get('username')
          password = request.form.get('password')
          if username == 'zs':
              g.username = 'zs'
              g.ip = 'x.x.x.x'
              login_log()
              loginip_log()
              return 'success'
          else:
              return 'false'
  ```

- 这样就不用这样定义函数：`def login_log(username):` ，调用时再传参: `login_log(username)`

## 6.10 钩子函数
### 6.10.1 before_request
- 在视图函数执行之前执行的， 可以把钩子代码放到视图函数之前执行
- 代码
  - hook_demo.py
    ```
    #encoding: utf-8

    from flask import Flask,render_template,session

    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        print 'hello world'
        return 'success'

    # before_request : 在视图函数执行之前执行的， 可以把钩子代码放到视图函数之前执行
    @app.before_request
    def my_before_request():
        print('my_before_request')
    ```
### 6.10.2 context_processor
- 上下文处理器应该返回一个字典，字典中的`key`会被模版当成变量渲染

- 返回的字典在所有页面都可以使用

- 被这个装饰器修饰的函数，必须要返回一个字典，即使为空也要返回

- 代码
  - index.html
    ```
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        这是html
        {{ username }}
    </body>
    </html>
    ```
  - hook_demo.py
    ```
    #encoding: utf-8

    from flask import Flask,render_template,session

    app = Flask(__name__)

    @app.route('/')
    def hello_world():
        print 'hello world'
        
        # 使用前页面需要变量渲染，每个需要传参
        # return render_template('index.html', username = 'zs')
        
        # 使用 context_processor 后，不再需要传，变量放在 context_processor 返回的字典中
        return render_template('index.html')

    @app.context_processor
    def my_context_processor():
        print('my_context_processor')
        if session.get('username') == 'zs':
            return {'username': 'zs'}
        else:
            return render_template("login.html")
    ```
## 6.11 问答项目
1. https://v3.bootcss.com/getting-started/

- 起步 --》 使用 BootCDN 提供的免费 CDN 加速服务（同时支持 http 和 https 协议）

    ```
    <!-- 最新版本的 Bootstrap 核心 CSS 文件 -->
    <link rel="stylesheet" href="https://cdn.bootcss.com/bootstrap/3.3.7/css/bootstrap.min.css" integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u" crossorigin="anonymous">

    <!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
    <script src="https://cdn.bootcss.com/bootstrap/3.3.7/js/bootstrap.min.js" integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
    ```

2. bootstrap静态资源库
    百度 `bootstrap静态资源库` 到 http://www.bootcdn.cn/ 
    jquery --》 下载这个 https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js
    在上面2个bootstrap中加入 `<script src="https://cdn.bootcss.com/jquery/3.2.1/jquery.min.js"></script>`
3. https://v3.bootcss.com/components/#navbar 中的组件 --》 导航条 --》 导航条的可访问性（复制里面的代码）
4. 添加图片
5. 修改css
6. 登录，bootstrap css样式，表单 --》 基本实例：https://v3.bootcss.com/css/#forms-example
7. 注册
8. user模型
9. 注册
10. 登录
11. 注销
12. 首页布局
13. 问答
14. 评论
15. 查询
16. g对象优化、密码优化


## 问题

- 问题：：No module named MYSQLdb
  处理：https://www.cnblogs.com/guohaojintian/p/6030818.html
- 问题：TypeError: 'BaseQuery' object is not callable
  处理：
  是 `user = User.query.filter(User.id == 1).firsst()`  
  不是 `user = User.query().filter(User.id == 1).firsst()`
- 问题：ImportError: cannot import name db
  确实是引入了，那就应该是循环引入导致
- 问题，html子页面乱码
  处理：https://blog.csdn.net/mindmb/article/details/7898528
  ```
  import sys
  reload(sys)
  sys.setdefaultencoding('utf8')
  ```

# 7. django

- [django菜鸟教程](http://www.runoob.com/django/django-tutorial.html)




# 8. python连接mysql
## 8.1 pymysql
  ```
  #!/usr/bin/python
  # -*- coding: UTF-8 -*-

  import pymysql

  conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='root', db='mysql', charset='utf8')
  cursor = conn.cursor()
  rowNums = cursor.execute('SELECT * FROM user')

  print('查询的行数为' + str(rowNums))

  selectResultList = cursor.fetchall()
  for i in range(len(selectResultList)):
      print(selectResultList[i])
  conn.commit()
  cursor.close()
  conn.close()
  ```



# 9 爬虫
## 9.1 安装
### 9.1.1 参考地址
- 参考租房网爬虫视屏：https://study.163.com/course/courseMain.htm?courseId=1003666043
### 9.1.2 Mac环境安装
- xcode-select --install
- ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install)"
- brew search python
- brew install python3
- pip3 installl scrapy
  检查scrapy版本：`scrapy version`

### 9.1.3 Linux环境安装
- sudo apt-get install python3-pip python3-dev
- sudo pip3 install --upgrade pip
- sudo pip3 install scrapy
  检查scrapy版本：`scrapy version`

### 9.1.4 window环境安装
- 先安装python3.5，官网下载
- 安装Pywin32，网址：[https://sourceforge.net/projects/pywin32/files/pywin32/](https://sourceforge.net/projects/pywin32/files/pywin32/) ;如果安装后后面还是出现没有pywin32接口，可以在[https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml](https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml)下载pywin32，`pip install` 安装
- pip3离线安装lxml，网址：[https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml](https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml)
- pip3离线安装twisted，网址：[https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml](https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml)
- 最后 `pip3 install scrapy` ，检查下scrapy的版本号：`scrapy version`
- python2.7 同理，找对应的文件下载安装 `Twisted-17.9.0-cp36-cp36m-win_amd64.whl` 

### 9.1.5 conda创建的虚拟环境安装scrapy
- 启动虚拟环境，安装scrapy
  ```
  activate python36
  pip -V
  pip install scrapy
  ```
- 当出现安装失败：缺少window上的vc相关的库时，先直接安装下 `Twisted-17.9.0-cp36-cp36m-win_amd64.whl` ，scrapy要用到它，它又依赖window的vc相关库
- 解决
  方法一：安装vc相关的库
  方法二：直接下载 `Twisted-17.9.0-cp36-cp36m-win_amd64.whl` 然后 
  ```
  pip install D:\bigdata\source\python\scrapy\Twisted-17.9.0-cp36-cp36m-win_amd64.whl
  pip install scrapy
  ```


## 9.2 scrapy

### 9.2.1 scrapy命令

```
(python27) C:\Users\zhang>scrapy
Scrapy 1.5.0 - no active project

Usage:
  scrapy <command> [options] [args]

Available commands:
  bench         Run quick benchmark test
  fetch         Fetch a URL using the Scrapy downloader
  genspider     Generate new spider using pre-defined templates
  runspider     Run a self-contained spider (without creating a project)
  settings      Get settings values
  shell         Interactive scraping console
  startproject  Create new project
  version       Print Scrapy version
  view          Open URL in browser, as seen by Scrapy

  [ more ]      More commands available when run from project directory

Use "scrapy <command> -h" to see more info about a command
```
- 命令参考地址：
  https://www.cnblogs.com/lemonbit/p/7068091.html
- bench: 测试性能
- fetch：给个url地址，下载
- genspider：创建一个爬虫
- runspider：启动一个爬虫
- shell：scrapy shell 环境
  - 类似于ipython
  - 启动后：
    - print response.body
    - print response.xpath('')
- startproject：创建scrapy项目
- version：版本
- view：浏览器中查看
#### 9.2.1.1 使用命令创建项目
- 创建项目：`scrapy startproject zufangtest`

  ```
  (python36) C:\Users\zhang>scrapy startproject zufangtest
  New Scrapy project 'zufangtest', using template directory 'c:\\users\\zhang\\anaconda3\\envs\\python36\\lib\\site-packages\\scrapy\\templates\\project', created in:
      C:\Users\zhang\zufangtest

  You can start your first spider with:
      cd zufangtest
      scrapy genspider example example.com

  (python36) C:\Users\zhang>cd C:\Users\zhang\zufangtest
  (python36) C:\Users\zhang\zufangtest>scrapy list
  ```




## 9.2.2 scrapy shell
- 赶集租房地址：http://bj.ganji.com/fang1/
- 使用scrapy shell：
- `scrapy shell http://bj.ganji.com/fang1/`
  ```
  [s]   fetch(req)                  Fetch a scrapy.Request and update local objects
  [s]   shelp()           Shell help (print this help)
  [s]   view(response)    View response in a browser
  >>>
  ```

- 查看抓取下来的页面 `view(response)`

- scrapy shell 中爬取赶集网租房信息 

  ```
  # 获取指定的（单个）title, path查找可以利用火狐浏览器的firbug 和 firpath来获取
  response.xpath(".//div[@id='puid-2944994487']/dl/dd[1]/a/text()").extract()

  # 获取价格（单个）
  response.xpath(".//div[@id='puid-2944994487']/dl/dd[5]/div[1]/span[1]/text()").extract()

  #获取所有的title和价格
  response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[5]/div[1]/span[1]/text()").extract()
  response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[1]/a/text()").extract()
  ```

## 9.3 爬取信息
### 9.3.1 爬取赶集住房网住房信息
- 代码下载链接：[百度网盘地址](https://pan.baidu.com/s/1qYPi2iK)

- 创建scrapy项目 `scrapy startproject zufang`，命令下面会列出项目地址

- pycharm打开这个项目，

- 在spliders下创建python文件 zufang.py
  ```
  #encoding: utf-8
  import scrapy

  class GanjiSpider(scrapy.Spider):
      name = "zufang" #爬虫的名字，scrapy list获取到的名字
      start_urls = ['http://bj.ganji.com/fang1/chaoyang/']

      def parse(self, response):
          print(response)
          title_list = response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[5]/div[1]/span[1]/text()").extract()
          money_list = response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[1]/a/text()").extract()
          for i,j in zip(title_list, money_list):
              print(i + "-------------" + j)
  ```

- 在pycharm 的 cmd里命令：`scrapy list` 列出当前有多少个爬虫项目（例如：zufang）

  - 或者在window的cmd命令端，进入项目的位置，输入：`scrapy list`
  - 项目的名称是代码中的name值

- 运行 `scrapy crawl zufang`

- 问题：
  ....
  2018-03-23 21:35:18 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (302) to <GET http://www.ganji.com/404.htm> from <GET http://callback.ganji.com/robots.txt>
  2018-03-23 21:35:18 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (meta refresh) to <GET http://www.ganji.com/> from <GET http://www.ganji.com/404.htm>
  2018-03-23 21:35:18 [scrapy.downloadermiddlewares.redirect] DEBUG: Redirecting (302) to <GET http://bj.ganji.com/> from <GET http://www.ganji.com/>
  2018-03-23 21:35:18 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://bj.ganji.com/> (referer: None)
  2018-03-23 21:35:18 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://callback.ganji.com/firewall/valid/1898790186.do?namespace=ganji_zufang_list_pc&url=http%3A%2F%2Fbj.ganji.com%2Ffang1%2Fchaoyang%2F> (referer: None)
  <200 http://callback.ganji.com/firewall/valid/1898790186.do?namespace=ganji_zufang_list_pc&url=http%3A%2F%2Fbj.ganji.com%2Ffang1%2Fchaoyang%2F>
  2018-03-23 21:35:18 [scrapy.core.engine] INFO: Closing spider (finished)
  ```
  处理：
  打开最后的url [https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/) 然后按照提示校验下，访问过去频繁导致
  ```

### 9.3.2 开始把数据写到sqlite
- 在 `pycharm terminal` 里使用ipython命令`ipython`，创建sqlite库和表
  ```
  import sqlite3
  zufangtest = sqlite3.connect('zufangtest.sqlite')
  create_table = 'create table zufangtest (title varchar(512), money varchar(128))'
  zufangtest.execute(create_table)
  quit
  ```
  - 创建后就可以在项目中看到 `zufangtest.sqlite` 文件

- 打开pycharm 右边的 `database` 把文件拖进来，刷新，打开，看到创建的表 `zufangtest` 则成功

- 修改代码
  - settings.py 放开下面的注释
    ```
     ITEM_PIPELINES = {
        'zufangtest.pipelines.ZufangtestPipeline': 300,
     }
    ```
  - pipelines.py 管道文件
    ```
    import sqlite3

    class ZufangtestPipeline(object):
        def open_spider(self,spider):
            self.con = sqlite3.connect("zufangtest.sqlite")
            self.cu = self.con.cursor()

        def process_item(self, item, spider):
            print(spider.name, 'pipelines')
            # print("------------" + item['title'])
            insert_sql = "insert into zufangtest (title, money) values('{}', '{}')".format(item['title'], item['money'])
            # print("sql--------->" + insert_sql)
            self.cu.execute(insert_sql)
            self.con.commit()
            return item

        def spider_close(self, spider):
            self.con.close()
    ```
  - items.py
    ```
    import scrapy

    class ZufangtestItem(scrapy.Item):
        # define the fields for your item here like:
        # name = scrapy.Field()
        title = scrapy.Field()
        money = scrapy.Field()
    ```
    - zufang.py
    ```
    import scrapy
    from zufangtest.items import ZufangtestItem
    import sys
    reload(sys)
    sys.setdefaultencoding("utf-8")

    class GanjiSpider(scrapy.Spider):

        # 爬虫的名字，scrapy list获取到的名字
        name = "zufangtest"
        start_urls = ['http://bj.ganji.com/fang1/chaoyang/']

        def parse(self, response):
            print(response)
            zf = ZufangtestItem()
            title_list = response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[1]/a/text()").extract()
            money_list = response.xpath(".//div[@class='f-list-item ershoufang-list']/dl/dd[5]/div[1]/span[1]/text()").extract()
            for i,j in zip(title_list, money_list):
                # print(i + "-------------" + j)
                zf['title'] = i
                zf['money'] = j
                yield zf
    ```

- pycharm terminal 中执行 `scrapy crawl zufangtest` 开始爬取数据

- 查看表zufangtest中的数据

- 问题：Exception in thread "main" java.lang.ClassNotFoundException: org.sqlite.JDBC

  - 下载驱动http://mvnrepository.com/artifact/sqlitejdbc/sqlitejdbc/0.5.6
  - pycharm右侧的database，点击`data source and drive`（数据库和扳手的图标）--》 点击test connection， 缺少驱动应该是faile
  - 点击 `sqlite(xerial)` 或是左侧的`import data source`下面的创建的数据库 --> 点击driver file里的 +， 添加下载的驱动jar
  - 添加jar后，点击左侧的`import data source`下面的创建的数据库 --》 再点击 `test connection`

## 9.4 xpath
### 9.4.1 火狐浏览器xpath使用
- 火狐浏览器新版本的不再支持firbug 和 xpath
- 可以在浏览器中`f12`打开调试，点位要选取的内容，然后右键copy，然后选择copy xpath，可以检索出xpath的路径，但不支持在debug 模式下使用xpath查询
### 9.4.2 谷歌浏览器xpath
- 在谷歌浏览器中的支持上面的xpath检索路径（在debug模式下copy xpath）

- 也支持根据xpath路径检索对应的内容，获取到xpath后，`ctrl + f` 复制xpath路径，就可以检索到对应的内容

### 9.4.3 QQ浏览器
- 和谷歌浏览器类似，使用的是谷歌浏览器的内核

### 9.4.4 手写xpath
- 自动获取的xpath的路径是根据某个id或什么生成的，手写的可以有自己的思路，建议自己手写
- xpath教程
  - w3school 中的xpath
  - www.spbeen.com 中的xpath教程，这是完全基于爬虫写的（小布老师博客-网易python爬虫）




# 10. Anaconda

## 10.1 介绍
- python 管理python工具，python第三方软件管理，并不是python官方的

- Anaconda 是一个用于科学计算的 Python 发行版，支持 Linux, Mac, Windows, 包含了众多流行的科学计算、数据分析的 Python 包。

- Anaconda 官网的镜像源是在国外，比较慢，可以百度 [Anaconda 清华镜像源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

- Anaconda、conda、pip、virtualenv的区别： [https://www.jianshu.com/p/62f155eb6ac5](https://www.jianshu.com/p/62f155eb6ac5)

## 10.2 安装、使用
### 10.2.1 安装
- 下载： Anaconda 安装包可以到 https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/ 下载。

- 安装时注意把path加入

- 安装后`cmd` 进入命令行，`conda` 如果报错，说明环境变量没配好
```
usage: conda [-h] [-V] command ..
conda is a tool for managing and deploying applications, environments and packages
Options
positional arguments:
  command
    clean        Remove unused packages and caches.
    config       Modify configuration values in .condarc. This is modeled
                 after the git config command. Writes to the user .condarc
                 file (C:\Users\zhang\.condarc) by default.
    create       Create a new conda environment from a list of specified
                 packages.
    help         Displays a list of available conda commands and their help
                 strings.
    info         Display information about current conda install.
    install      Installs a list of packages into a specified conda
                 environment.
    list         List linked packages in a conda environment.
    package      Low-level conda package utility. (EXPERIMENTAL)
    remove       Remove a list of packages from a specified conda environment.
    uninstall    Alias for conda remove. See conda remove --help.
    search       Search for packages and display associated information. The
                 input is a MatchSpec, a query language for conda packages.
                 See examples below.
    update       Updates conda packages to the latest compatible version. This
                 command accepts a list of package names and updates them to
                 the latest versions that are compatible with all other
                 packages in the environment. Conda attempts to install the
                 newest versions of the requested packages. To accomplish
                 this, it may update some packages that are already installed,
                 or install additional packages. To prevent existing packages
                 from updating, use the --no-update-deps option. This may
                 force conda to install older versions of the requested
                 packages, and it does not prevent additional dependency
                 packages from being installed. If you wish to skip dependency
                 checking altogether, use the '--force' option. This may
                 result in an environment with incompatible packages, so this
                 option must be used with great caution.
    upgrade      Alias for conda update. See conda update --help
optional arguments:
  -h, --help     Show this help message and exit.
  -V, --version  Show the conda version number and exit
conda commands available from other packages:
  build
  convert
  develop
  env
  index
  inspect
  metapackage
  render
  server
  skeleton
  verif
C:\Users\zhang>
```

- 查看conda的版本
  ```
  conda --version
  ```

### 10.2.2 安装scrapy
- `conda install scrapy=1.4` , 会提示 是否更新

### 10.2.3 python 的虚拟环境
- 创建python虚拟环境
  ```
  #默认路径在 C:\Users\zhang\Anaconda3\envs	
  create -n python36 python=3.6

  #安装虚拟环境到指定路径的命令如下：
  conda create --prefix=D:\bigdata\tools-free\python\python3.6 python=3.6
  ```

- 启动虚拟环境：
  ```
  activate.bat pythono36

  #如果安装指定了目录，启动时也要指定目录
  activate D:\bigdata\tools-free\python\python3.6

  #删除
  conda remove --help
  conda remove --prefix D:\bigdata\tools-free\python\python3.6 --all
  ```

- 查看python版本

  ```
  #大V
  python -V
  ```

- 再查看下pip的版本，看下路径

  ```
  #大V
  pip -V
  ```

- 然后就可以直接使用 `pip install` 安装其它依赖

- 想要删除指定路径下的虚拟环境，使用如下的命令
  ```
  conda remove --prefix=D:\bigdata\tools\python\python36 --all
  ```
## 10.3 命令
- 参考地址：
  https://blog.csdn.net/menc15/article/details/71477949


# 资源地址
Python爱好者社区历史文章列表（每周append更新一次）: http://mp.weixin.qq.com/s/-j4u6Q4KpAfTQZnlaO0vgw