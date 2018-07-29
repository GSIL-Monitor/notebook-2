# 1. 下载
[官方个版本下载地址](https://notepad-plus-plus.org/download/all-versions.html)

# 2 插件
## 2.1 参考地址
> [Notepad++及实用插件安装](http://blog.csdn.net/z45689/article/details/57493338)  
> [Notepad++实用插件分享](http://blog.csdn.net/wang02011/article/details/7743522)

## 2.2 好用的些插件
> Compare  
> Light Explorer  
> NppFTP  
> Explorer  
> RegEx Helper  
> JSON Viewer   

## 2.3 pugin manager

[pugin manager下载参考地址](https://github.com/notepad-plus-plus/notepad-plus-plus/issues/32647)

[pugin manager github下载地址](https://github.com/bruderstein/nppPluginManager/releases)

## 2.4 安装离线插件

* 设置 --》 导入 --》插件/主题
  * 选择要装的dll文件

# 3. 右键菜单
## 3.1 参考地址
- [无法将notepad++添加到打开方式列表中的解决办法](https://blog.csdn.net/jl1134069094/article/details/50749075)
- [为绿色版NOTEPAD++添加、删除右键菜单](https://yutuo.net/archives/58c118c4d5528597.html)

## 3.2 删除右键的notepad++ 菜单
### 3.2.1 bat脚本删除
- 脚本 `dele.bat`
  ```
  @echo off
  cd /d "%~dp0"
  reg delete "HKEY_CLASSES_ROOT\*\Shell\NotePad++" /f
  ```
- 使用管理员运行


# 4. window+R中使用notepad++
