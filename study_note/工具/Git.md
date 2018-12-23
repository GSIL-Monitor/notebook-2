# 一、Git

# 7. git
## 7.1 强制覆盖本地文件
```
git fetch --all
git reset --hard origin/master
git pull
```
## 7.2 删除github中仓库中的文件本地保留
- window中启动git命令窗口，cd到要删除文件的目录
- 执行以下命令
  ```
  git rm -r --cached test.md  #--cached不会把本地的test.md删除
  git commit -m 'delete test.md'
  git push -u origin master
  ```
## 7.3 git命令保存用户名密码
参考地址：https://www.jianshu.com/p/47a51aeeea62
```
git config credential.helper store
```

## 7.4 git忽略文件
- 参考地址：https://www.jianshu.com/p/62a412b7a6e8
- 可以在仓库根目录下用git客户端创建文件   .gitignore
  ```
  #忽略配置文件
  .gitignore

  #我的笔记
  my_document/账号.md
  ```



# 二、TortoiseGit

下载地址：https://tortoisegit.org/download/

版本列表：https://download.tortoisegit.org/tgit/