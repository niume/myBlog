---
title: jqweui loading 如何显示
date: 2017-11-30 13:03:09
tags: 
- jqweui
- weui'
top: 90
categories: 
---
首先吐槽一下 jqweui的官方手册，有的地方确实不是太完善。
不过现在做小程序的应该用官方的weui了。

toast 在jqweui 的手册中，只有下面几个方法（http://jqweui.com/components#toast ）。

```bash
$.toast("取消操作", "cancel");
$.toast("禁止操作", "forbidden");
$.toast("纯文本", "text");
// 第二个参数可以是时间，单位毫秒
$.toast("消息", 20000);
```

但是右边的正在加载样式，手册中怎么也找不到。
百度了一遍，解决方案几乎千篇一律的是自己用jqweui的样式自己封装。
无赖只能F12 找到对应的按钮后，发现官方有现成的方法可以调用。



展示正在加载中页面
```bash
$.showLoading("开门中...");
```

关闭正在加载中页面

```bash
$.hideLoading();
```

ok，完美解决。