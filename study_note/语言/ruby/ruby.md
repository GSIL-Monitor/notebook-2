# 1. 基本使用
## 1.1 加密
```
#md5加密
require 'md5' 
puts MD5.hexdigest('admin')
 
#sha1加密
require 'digest/sha1' 
puts Digest::SHA1.hexdigest('admin')
 
require 'base64' 
code = Base64.encode64('admin') 
source = Base64.decode64(code)
```
## 1.2 时间

```
Time.now.to_i
```

# 2. 问题
## 2.1 类型强转异常
- 代码
  ```
time = Time.now.to_i
str = time + "000"
  ```
- 异常信息
  failed: error_class=TypeError error="no implicit conversion of Fixnum into String"
- 原因
不能强转string使用`.to_s`