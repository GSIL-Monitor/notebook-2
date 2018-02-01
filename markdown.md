# **notepad++使用**

## **索引**  
[1. window上使用markdown pad](#1. window上使用markdown pad)    
&emsp; [1.1 下载markdownpad](#1.1 下载markdownpad)  
&emsp; [1.2 注册](#1.2 注册)  
&emsp; [1.3 window10 下MarkdownPad2 预览无法显示问题](#1.3 window10 下MarkdownPad2 预览无法显示问题)  
[2. 基本使用](#2. 基本使用)  
&emsp;[2.1. 换行](#2.1. 换行)  
&emsp;[2.2. 页面内跳转，锚点](#2.2. 页面内跳转，锚点)  
&emsp;[2.3. 链接](#2.3. 链接)  
&emsp;[2.4. 注脚](#2.4. 注脚)  
&emsp;[2.5. tab](#2.5. tab)  
&emsp;[2.6. 图片](#2.6. 图片)  
&emsp;[2.7. 文件](#2.7. 文件)  


## 1. window上使用markdown pad  [返回索引](#索引)
### 1.1 下载markdownpad  
- [http://markdownpad.com/](http://markdownpad.com/)
### 1.2 注册
- 邮箱  
  Soar360@live.com
- 授权密匙GBPduHjWfJU1mZqcPM3BikjYKF6xKhlKIys3i1MU2eJHqWGImDHzWdD6xhMNLGVpbP2M5SN6bnxn2kSE8qHqNY5QaaRxmO3YSMHxlv2EYpjdwLcPwfeTG7kUdnhKE0vVy4RidP6Y2wZ0q74f47fzsZo45JE2hfQBFi2O9Jldjp1mW8HUpTtLA2a5/sQytXJUQl/QKO0jUQY4pa5CCx20sV1ClOTZtAGngSOJtIOFXK599sBr5aIEFyH0K7H4BoNMiiDMnxt1rD8Vb/ikJdhGMMQr0R4B+L3nWU97eaVPTRKfWGDE8/eAgKzpGwrQQoDh+nzX1xoVQ8NAuH+s4UcSeQ== 
### 1.3. window10 下MarkdownPad2 预览无法显示是怎么回事？
- 参考[https://www.zhihu.com/question/34393386](https://www.zhihu.com/question/34393386 "参考")
- 刚刚在我的 Win10 上测试了一次，确实会遇到这个问题，官方的说法是从 Win 8 开始就有这个问题了，解决办法就是安装 Awesomium 1.6.6 SDK.，如果还是不行就再安装 Microsoft's DirectX End-User Runtimes (June 2010)，经我实际测试，只需要安装前者就可以了 






## 2. 基本使用  [返回索引](#索引)  
### 2.1. 换行  
- 使用2个空格再回车 
- 使用html风格：`</br>`
### 2.2. 页面内跳转，锚点  
- 代码  
  `[跳转测试](#jump)`  
  `<span id="jump">跳转到这里\<span>`
### 2.3. 链接
- 方式一，代码：  
  `[链接测试](http://www.baidu.com)`
- 方式二，代码  
  `第一个[1]个链接，这是第二个[2]个链接,这是第三个[3]个链接`  
  `[1]:http://www.baidu.com`  
  `[2]:http://blog.csdn.net/witnessai1/article/details/52551362`  
  `[3]:https://www.baidu.com/baidu?word=markdown+%E9%93%BE%E6%8E%A5&ie=utf-8&tn=98012088_5_dg&ch=5`  
### 2.4. 注脚
代码：  
`这是一个链接到谷歌的[^脚注]。`  
`[^脚注]: http://www.google.com`

### 2.5. tab 
&emsp;由于markdown语法主要考虑的是英文，所以对于中文的首行缩进并不太友好，两种方法都可以完美解决这个问题。  

- 方式一：把输入法由半角改为全角。 两次空格之后就能够有两个汉字的缩进  
- 方式二：直接使用代码  
  方大的空白`&ensp;` 或 `&#8194;`  
  全方大的空白`&emsp;`或`&#8195;`  
  不断行的空白格`&nbsp;`或`&#160;`  

### 2.6. 图片 
- 方式一：html代码，使用img标签  
  `<img src="./xxx.png" width = "300" height = "200" alt="图片名称" align=center />`

- 方式二：支持大小改变  
  `![](./pic/pic1_50.png =100x100)`  
  `![](file:///)`

### 2.7 文件 
- 和url的写法相同  
  `[](file:///)`




[typora](https://www.typora.io/#windows)  
[miu](https://www.appinn.com/miu-markdown-editor/)
window下的markdown：https://www.jianshu.com/p/471c2c1d562d