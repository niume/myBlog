---
title: php基础总结
date: 2018-04-16 23:14:07
tags:
categories: PHP
top: 10
---

## 一、常用魔术方法的触发时机？


1） __autoload() ：当程序实例化某个类，而该类没有在当前文件中被引入。此时会触发执行__autoload()。程序希望通过该方法，自动引入这个类文件。该方法有一个参数，即就是那个忘记引入的类的名称。__autoload()方法的工作原理是什么？当程序执行到实例化某个类的时候，如果在实例化前没有引入这个类文件，那么就自动执行__autoload()函数。这个函数会根据实例化的类的名称来查找这个类文件的路径，当判断这个类文件路径下确实存在这个类文件后，就执行include或者require来载入该类，然后程序继续执行，如果这个路径下不存在该文件时就提示错误。使用自动载入的魔术函数可以不必要写很多个include或者require函数。

2） __construct() ：这个是魔术构造方法。构造方法是实例化对象的时候自动执行的方法，作用就是初始化对象。该方法可以没有参数，也可以有多个参数。如果有参数，那么new这个对象的时候要记得写上相应的参数。在php5以前，没有魔术构造方法，普通构造方法是一个跟类名同名的方法来实现构造的。如果一个类中既写了魔术构造方法，又定义了普通构造方法。那么php5以上版本中，魔术方法起作用，普通构造方法不起作用。反之，在php5以前版本中，不认识魔术构造方法，只是把该方法当做普通的方法。

3） __destruct() ：这个是魔术析构方法。析构方法的作用和构造方法正好相反，是对象被销毁时被自动调用的，作用是释放内存。析构方法没有参数。

4） __call() ：当程序调用一个不存在或不可见的成员方法时，自动触发执行__call()。它有两个参数，分别是未访问到的方法名称和方法的参数。而第二个参数是数组类型。

5） __get() ：当程序调用一个未定义或不可见的成员属性时，自动触发执行__get()。它有一个参数，表示要调用的属性的名称。

6） __set()：当程序试图写入一个不存在或不可见的成员属性时，PHP就会自动执行__set()。它包含两个参数，分别表示属性名称和属性值。

7） __tostring() ：当程序使用echo或print输出对象时，会自动调用该方法。目的是希望通过该方法将对象转化为字符串，再输出。__tostring() 无参数，但是该方法必须有返回值。

8） __clone() ：当程序clone一个对象的时候，能触发__clone()方法，程序希望通过这个魔术方法实现：不仅仅单纯地克隆对象，还需要克隆出来的对象拥有原来对象的所有属性和方法。


## 二、$this，self::, parent::,static::  使用场景


$this代表的是当前对象

self代表的是当前的类

parent代表的是当前类的父类

使用场合：
$this只能使用在当前类中，通过$this->可以调用当前类中的属性和方法；
self只能在当前类中使用，通过作用域操作符::访问当前类中的类常量、当前类中的静态属性、当前类中的方法；
parent只能使用在有父类的当前类中，通过作用域操作符::访问父类中的类常量、父类中的静态属性、父类中的方法

后期静态绑定

自 PHP 5.3.0 起，PHP 增加了一个叫做后期静态绑定的功能，用于在继承范围内引用静态调用的类。

准确说，后期静态绑定工作原理是存储了在上一个“非转发调用”（non-forwarding call）的类名。当进行静态方法调用时，该类名即为明确指定的那个（通常在 :: 运算符左侧部分）；当进行非静态方法调用时，即为该对象所属的类。所谓的“转发调用”（forwarding call）指的是通过以下几种方式进行的静态调用：self::，parent::，static:: 以及 forward_static_call()。可用 get_called_class() 函数来得到被调用的方法所在的类名，static:: 则指出了其范围。

该功能从语言内部角度考虑被命名为“后期静态绑定”。“后期绑定”的意思是说，static:: 不再被解析为定义当前方法所在的类，而是在实际运行时计算的。也可以称之为“静态绑定”，因为它可以用于（但不限于）静态方法的调用。
self:: 的限制

使用 self:: 或者 __CLASS__ 对当前类的静态引用，取决于定义当前方法所在的类：

Example #1 self:: 用法

代码实例 


```bash

<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        self::who();
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
?>
```


以上例程会输出：

A

后期静态绑定的用法

后期静态绑定本想通过引入一个新的关键字表示运行时最初调用的类来绕过限制。简单地说，这个关键字能够让你在上述例子中调用 test() 时引用的类是 B 而不是 A。最终决定不引入新的关键字，而是使用已经预留的 static 关键字。

Example #2 static:: 简单用法

```bash
<?php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test() {
        static::who(); // 后期静态绑定从这里开始
    }
}

class B extends A {
    public static function who() {
        echo __CLASS__;
    }
}

B::test();
?>
```
以上例程会输出：

B
Note:

在非静态环境下，所调用的类即为该对象实例所属的类。由于 $this-> 会在同一作用范围内尝试调用私有方法，而 static:: 则可能给出不同结果。另一个区别是 static:: 只能用于静态属性。

Example #3 非静态环境下使用 static::
```bash
<?php
class A {
    private function foo() {
        echo "success!\n";
    }
    public function test() {
        $this->foo();
        static::foo();
    }
}

class B extends A {
   /* foo() will be copied to B, hence its scope will still be A and
    * the call be successful */
}

class C extends A {
    private function foo() {
        /* original method is replaced; the scope of the new one is C */
    }
}

$b = new B();
$b->test();
$c = new C();
$c->test();   //fails
?>
```
以上例程会输出：

success!
success!
success!


Fatal error:  Call to private method C::foo() from context 'A' in /tmp/test.php on line 9

Note:

后期静态绑定的解析会一直到取得一个完全解析了的静态调用为止。另一方面，如果静态调用使用 parent:: 或者 self:: 将转发调用信息。


Example #4 转发和非转发调用
```bash
    <?php
    class A {
        public static function foo() {
            static::who();
        }

        public static function who() {
            echo __CLASS__."\n";
        }
    }

    class B extends A {
        public static function test() {
            A::foo();
            parent::foo();
            self::foo();
        }

        public static function who() {
            echo __CLASS__."\n";
        }
    }
    class C extends B {
        public static function who() {
            echo __CLASS__."\n";
        }
    }

    C::test();
    ?>
``` 
以上例程会输出：

A
C
C




参考博客：
http://php.net/manual/zh/language.oop5.static.php

https://www.cnblogs.com/jerrypro/p/6422923.html