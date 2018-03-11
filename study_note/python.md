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

# 7. django
- [django菜鸟教程](http://www.runoob.com/django/django-tutorial.html)


# 资源地址
Python爱好者社区历史文章列表（每周append更新一次）: http://mp.weixin.qq.com/s/-j4u6Q4KpAfTQZnlaO0vgw