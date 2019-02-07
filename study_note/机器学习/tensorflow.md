# 一、安装
## 1. 查看版本
```
python

import tensorflow as tf
# 版本
tf.__version__
# 查询tensorflow安装路径为:
tf.__path
```

## 2. 安装
```
pip install --upgrade --ignore-installed tensorflow
# 进入python虚拟环境后执行下面的
pip install --upgrade --ignore-installed tensorflow==1.11.0
```

## 3. pip安装tersorflow异常：
- 异常信息
  ```
  Installing collected packages: wheel, numpy, six, keras-preprocessing, grpcio, h5py, keras-applications, absl-py, setuptools, protobuf, markdown, werkzeug, tensorboard, gast, termcolor, astor, tensorflow
  Could not install packages due to an EnvironmentError: [WinError 5] 拒绝访问。: 'C:\\study\\tools\\Anaconda3\\envs\\python3.6\\Lib\\site-packages\\numpy\\core\\multiarray.cp36-win_amd64.pyd'
  Consider using the `--user` option or check the permissions
  ```
- 参考地址：

  https://blog.csdn.net/a781751136/article/details/80231406
  
- 处理：

  ```
  pip install --user --upgrade --ignore-installed tensorflow==1.11.0
  ```

