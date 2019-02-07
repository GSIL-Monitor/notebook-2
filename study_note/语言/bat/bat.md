

# 1. 基本使用

## 1.1 命令
```
# 不输出、输出
@echo off、@echo on

#打印
echo "hello"

# start启动一个窗口
#如果需要新窗口运行后自动关闭，请把k换成c
start "" cmd /k "activate D:\bigdata\tools-free\python\python3.6"

#等待输入
pause
```

## 1.2 打开cmd窗口执行多条命令
```
@echo off
#如果需要新窗口运行后自动关闭，请把k换成c
start "" cmd /k "D: && cd D:\Users\zheng"
#pause
```

## 1.3 激活anaconda的python虚拟环境
```
@echo off
#如果需要新窗口运行后自动关闭，请把k换成c
start "" cmd /k "activate C:\study\tools\Anaconda3\envs\python3.7"
#pause
```

## 1.4 启动anaconda的jupyter notebook

```
@echo off
#如果需要新窗口运行后自动关闭，请把k换成c
start "" cmd /k "activate C:\study\tools\Anaconda3\envs\python2.7 && jupyter notebook D:\study\workspace\jupyter\python2.7"
#pause
```

