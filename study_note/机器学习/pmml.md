# 一、java转pickle模型为pmml

## 1. pickle模型转成pmml

- 参考地址：

  https://github.com/jpmml/jpmml-sklearn

  http://www.cnblogs.com/princessd8251/articles/9649547.html


- 环境需求:
  > Prerequisites
  The Python side of operations
  Python 2.7, 3.4 or newer.
  scikit-learn 0.16.0 or newer.
  sklearn-pandas 0.0.10 or newer.
  sklearn2pmml 0.14.0 or newer.
  Validating Python installation:

- 查看环境版本：
  ```
  import sklearn, sklearn.externals.joblib, sklearn_pandas, sklearn2pmml
  print(sklearn.__version__)
  print(sklearn.externals.joblib.__version__)
  print(sklearn_pandas.__version__)
  print(sklearn2pmml.__version__)
  ```

  >The JPMML-SkLearn side of operations
  Java 1.8 or newer.

- 安装
  > Installation
  Enter the project root directory and build using Apache Maven:
  ```
  mvn clean install
  ```

- 使用
  > The JPMML-SkLearn side of operations
  Converting the pipeline pickle file pipeline.pkl.z to a PMML file pipeline.pmml:
  ```
  java -jar target/jpmml-sklearn-executable-1.5-SNAPSHOT.jar --pkl-input pipeline.pkl.z --pmml-output pipeline.pmml
  ```
  Getting help:
  ```
  java -jar target/jpmml-sklearn-executable-1.5-SNAPSHOT.jar --help
  
  D:\study\workspace\jupyter\python3.6\model>java -jar jpmml-sklearn-executable-1.5-SNAPSHOT.jar --help
  Usage: org.jpmml.sklearn.Main [options]
    Options:
      --help
        Show the list of configuration options and exit
    * --pkl-pipeline-input, --pkl-input
        Pickle input file
    * --pmml-output
        PMML output file
  ```

## 2. 

# 二、Java调用pmml模型

## 1. 参考地址

https://github.com/jpmml/jpmml-evaluator

## 2. 

# 三、spark调用pmml

jpmml-evaluator-spark （github）：https://github.com/jpmml/jpmml-evaluator-spark

参考地址：https://blog.csdn.net/fansy1990/article/details/53293024	



# 四、python导出pkl.z再转pmml模型

## 1. 参考地址：

http://www.cnblogs.com/princessd8251/articles/9649547.html