---
title: php 生僻知识点
date: 2015-04-05 15:22:28
tags:
categories: PHP
top: 10
---

### 类名前加'\' 与不加的区别
#### Example #1 在命名空间中访问全局类
```bash
<?php
namespace A\B\C;
class Exception extends \Exception {}

$a = new Exception('hi'); // $a 是类 A\B\C\Exception 的一个对象
$b = new \Exception('hi'); // $b 是类 Exception 的一个对象

$c = new ArrayObject; // 致命错误, 找不到 A\B\C\ArrayObject 类
?>
```

#### Example #2 命名空间中后备的全局函数/常量

```bash

<?php
namespace A\B\C;

const E_ERROR = 45;
function strlen($str)
{
    return \strlen($str) - 1;
}

echo E_ERROR, "\n"; // 输出 "45"
echo INI_ALL, "\n"; // 输出 "7" - 使用全局常量 INI_ALL

echo strlen('hi'), "\n"; // 输出 "1"
if (is_array('hi')) { // 输出 "is not array"
    echo "is array\n";
} else {
    echo "is not array\n";
}
?>

```
#### 英文字母和中文汉字在不同字符集编码下的字节数
英文字母：

字节数 : 1;编码：GB2312

字节数 : 1;编码：GBK

字节数 : 1;编码：GB18030

字节数 : 1;编码：ISO-8859-1

字节数 : 1;编码：UTF-8

字节数 : 4;编码：UTF-16

字节数 : 2;编码：UTF-16BE

字节数 : 2;编码：UTF-16LE

 

中文汉字：

字节数 : 2;编码：GB2312

字节数 : 2;编码：GBK

字节数 : 2;编码：GB18030

字节数 : 1;编码：ISO-8859-1

字节数 : 3;编码：UTF-8

字节数 : 4;编码：UTF-16

字节数 : 2;编码：UTF-16BE

字节数 : 2;编码：UTF-16LE