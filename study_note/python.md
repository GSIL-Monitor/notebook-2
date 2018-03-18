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

### 2.6.5 继承 和 block 

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

### 6.6.6 URL链接 和 加载静态文件

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

## 6.7 SQLAlchemy数据库
### 6.7.1 MySQL-python 中间件介绍与安装
- 如果再类unix系统上，直接进入虚拟环境安装：`sudo pip install mysql-python`
- 如果再window上
  - 那么在这`http://www.lfd.uci.edu/~gohlke/pythonlibs#mysql-python` 下载 `MySQL_python‑1.2.5‑cp27‑none‑win_amd64.whl`
  - 进入到 `MySQL_python‑1.2.5‑cp27‑none‑win_amd64.whl` 所在目录，进行安装
    `pip install MySQL_python-1.2.5-cp27-none-win_amd64.whl`








# 7. django

- [django菜鸟教程](http://www.runoob.com/django/django-tutorial.html)




# 8. pymysql

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











# 资源地址
Python爱好者社区历史文章列表（每周append更新一次）: http://mp.weixin.qq.com/s/-j4u6Q4KpAfTQZnlaO0vgw