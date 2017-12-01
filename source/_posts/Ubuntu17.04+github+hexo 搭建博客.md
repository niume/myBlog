---
title: Ubuntu17.04+github+hexo 搭建博客
date: 2017-11-11 15:22:58
tags: 
- github
- hexo
top: 90
categories: Linux
---

### 第一步:
 首先安装nodejs,这个是hexo运行的基础环境,安装很简便:
```bash
$ sudo apt-get install nodejs
```
### 第二步
 安装npm,这个也很简单,这是是node.js的包管理器,跟apt,pip等差不多的东东, 直接
```bash
$ sudo apt-get install npm

```
 
### 第三步
接下来安装hexo或者hexo-cli,后者安装出现问题,前者没有问题,暂时还不知道原因.所以我安装的是hexo,如果你使用sudo npm install hexo -g 或者 sudo npm install hexo-cli -g,是不会成功的,最起码我是这样的。
正确的方法如下:
```bash
$ sudo npm install --unsafe-perm --verbose -g hexo 

```

### 第四步

修改以下hexo所在路径的文件
```bash
$ sudo vi /usr/local/lib/node_modules/hexo/bin/hexo
```

把#!/usr/bin/env node修改为!/usr/bin/env nodejs即可

```
#!/usr/bin/env nodejs
'use strict';
require('hexo-cli')();
```

然后输入hexo 看是否安装成功

### 第五步
创建项目：
    

```
$ mkdir blog
$ cd blog
$ hexo init
$ hexo g # 生成
$ hexo s # 启动服务

```

然后输入127.0.0.1：4000 就可以预览蓝

### 第六步

上传到github

首先在github 建造一个 youname(自己的github名字).github.io

在项目文件下安装插件

```
npm install hexo-deployer-git --save
```

在__config.yml中加入

```
deploy:
     type: git
     repository: git@github.com:youname/youmane.github.io.git
     branch: master
```

然后进入blog 文件

```
$ hexo g
$ hexo d

```
就可以上传到github了。然后youname.github.io 就可以访问到blog蓝
















