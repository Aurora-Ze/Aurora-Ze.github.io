---
title: 项目上传至GitHub
categories: 工具
---

# 上传项目

## 1. 先在github上新建一个仓库，记住HTTPS地址

## 2. 打开git bash，进入到项目目录中

如果用的是`idea`之类的开发工具的话，可以在那里面打开`terminal`终端（默认会来到项目路径下）

就不用自己手动`cd`了

## 3. 依次键入以下命令

第一次操作时：

```bash
git init

git add .

git commit -m "first try"

git remote add origin https://githun.com/ + ...  # 这里是仓库地址

git push -u origin master
```

## 4. 之后更新项目

```bash
// 添加所有文件
git add .

// 提交并说明
git commit -m "second try"

// push到远程仓库
git push
```

## 5.遇到问题

```bash
OpenSSL SSL_read: Connection was reset, errno 10054
```

使用`git config --global http.sslVerify "false"`命令解决

```bash
Failed to connect to github.com port 443: Timed out
```

检查代理是否关闭（要关闭）

