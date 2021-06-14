---
title: Hello World
top: 100
cover: https://gitee.com/aurora1004/pictures/raw/master/%E5%8A%9E%E5%85%AC%E7%94%A8%E5%93%81,%E7%99%BD%E8%89%B2%E8%83%8C%E6%99%AF,%E5%AD%A6%E6%A0%A1%E7%94%A8%E5%93%81,%E9%9D%99%E6%AD%A2%E7%9A%84,%E9%87%8D%E8%BF%94%E6%A0%A1%E5%9B%AD,%E8%AE%A2%E4%B9%A6%E6%9C%BA,%E5%8D%B7%E7%AC%94%E5%88%80,%E9%93%85%E7%AC%94,%E6%B0%B4%E7%AC%94,%E6%95%99%E8%82%B2.jpg
top_img: https://gitee.com/aurora1004/pictures/raw/master/%E5%8A%9E%E5%85%AC%E7%94%A8%E5%93%81,%E7%99%BD%E8%89%B2%E8%83%8C%E6%99%AF,%E5%AD%A6%E6%A0%A1%E7%94%A8%E5%93%81,%E9%9D%99%E6%AD%A2%E7%9A%84,%E9%87%8D%E8%BF%94%E6%A0%A1%E5%9B%AD,%E8%AE%A2%E4%B9%A6%E6%9C%BA,%E5%8D%B7%E7%AC%94%E5%88%80,%E9%93%85%E7%AC%94,%E6%B0%B4%E7%AC%94,%E6%95%99%E8%82%B2.jpg
---
# 记录博客用法

## 一、按钮

### 1.描述

> 点击按钮后，显示隐藏的内容

### 2.用法

```markdown
{% hideInline content,display,bg,color %}
```

### 3.示例

哪个英文字母最酷？ {% hideInline C(因为西装裤),查看答案,#FF7242,#fff %}

## 二、文章front-matter

### 1.描述

用来表示文章的一些信息，如标题、类别、tag和封面等

### 2.用法

|     键     |                        值（例子）                        |          描述          |
| :--------: | :------------------------------------------------------: | :--------------------: |
|   title    |                       Hello World                        |        文章标题        |
| categories |                          线程池                          |      文章类别所属      |
|    tags    |                        -tag: 源码                        |  文章标签，可包含多个  |
|   cover    | https://gitee.com/aurora1004/pictures/raw/master/wps.jpg |       文章外封面       |
|  top_img   | https://gitee.com/aurora1004/pictures/raw/master/wps.jpg |   文章内部，顶部图片   |
|    top     |                           100                            | 权重值，越大越容易置顶 |

## 三、代码配色修改

### 1.描述

通过引入外部css文件来修改代码配色

### 2.用法

1.选择一个想要的样式文件，网址：[代码配色相关css](https://github.com/highlightjs/highlight.js/tree/main/src/styles)

2.在`\butterfly\source\self`路径下新建一个`css`文件（如`code1.css`），复制第一步中选择的`css代码`

3.`code1.css`文件中添加以下代码

```css
/* 颜色可自己设置 */
:root {
  --hl-color: #d3af86;
  --hl-bg: #221a0f;
  --hltools-bg: #321a0f;
  --hltools-color: #fff;
  --hlnumber-bg: #221a0f;
  --hlnumber-color: #fff;
  --hlscrollbar-bg: #d3af86;
  --hlexpand-bg: #d3af86;
} 

/* 更改的內容 把.hljs改為 #article-container figure.highlight .hljs */
进行更改！！！！！
```

4.在butterfly主题的配置文件中引入该css文件

```yml
# ...
inject:
  head:
    - <link rel="stylesheet" href="/self/code1.css">    
```

## 四、hexo相关命令

### 1.本地启动博客

```bash
hexo s
```

### 2.上传GitHub

```bash
hexo clean
hexo g
hexo d
```

### 错误1

如果遇到如下报错

> OpenSSL SSL_read: Connection was reset, errno 10054

则在bash中输入以下代码

```bash
git config --global http.sslVerify "false"
```

然后重新来一遍即可。

### 错误2

```bash
fatal: unable to access 'https://github.com/Aurora-Ze/Aurora-Ze.github.io.git/': Failed to connect to github.com port 443: Timed out

err: Error: Spawn failed
```

**解决方法：**

1. 删除`.deploy_git`文件夹
2. 执行命令`git config --global core.autocrlf false`
3. 重新来一套部署命令

## 五、常用数学公式

### 1.上标下标

上标用`^`表示，下标用`_`表示；例如，$2^3_3$

### 2.大于等于……

大于等于用`\geq\`；例如$4 \geq\ 3$



