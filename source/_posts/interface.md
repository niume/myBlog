---
title: 如何优雅的使用接口
date: 2017-6-2 15:22:28
tags: 
- 接口
- 设计模式
categories: PHP
top : 99
---

嗯，6.1刚过完，我们不在是宝宝了，来吧，撸一篇接口的文章(interface).

在编程的过程中我们应该学会如何使用接口来给变我们的生活，极大的提升自我能力。
接口不是新特性，但是非常重要，下面我们来撸个接口的小例子。

虚构一个DocumentStore的类，这个类负责从不同的资源收集文本。可以从远程url读取html，也可以读取资源，也可以收集终端命令输出。

**定义DocumentStore类**

``` bash
class DocumentStore{
    protected $data = [];
    
    public function addDocument(Documenttable $document){
        $key = $document->getId();
        $value = $document->getContent();
        $this->data[key] = $value;        
    }
    
    public function getDocuments(){
        return $this->data;
    }
    
}

```

既然addDocument()方法的参数只能是Documenttable的类的实例，这样定义DocumentStore的类怎么行呢? 其实Documenttable不是类，是接口；

**定义Documenttable**

``` bash

interface Documenttable{
    public function getId();
    public function getContent(); 
   
}

```

这个接口定义表名，实现Documenttable接口的任何对象都必须提供一个公开的getId()方法和一个公开的getContent()方法。

可是这么做有什么用呢？这么做的好处就是，我们可以分开定义获取稳定的类，而且能使用十分不同的方法。下面是一种实现方式，这种方式使用curl从远程url获取html。

**定义HtmlDocument类**

``` bash
class HtmlDocument implements Documenttable{

    protected $url;

    public function __construct($url)
    {
        $this->url = $url;
    }

    public function getId(){
        return $this->url;
    }

    public function getContent(){
        $ch = curl_init();
        curl_setopt($ch,CURLOPT_URL,$this->url);
        curl_setopt($ch,CURLOPT_RETURNTRANSFER,1);
        curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,3);
        curl_setopt($ch,CURLOPT_FOLLOWLOCATION,1);
        curl_setopt($ch,CURLOPT_MAXREDIRS,3);
        curl_close($ch);
        return $thml;
    }
}

```

**下面一个方法是获取流资源。**

``` bash
class StreamDocument implements Documenttable{
    protected $resource;
    protected $buffer;

    public function __construct($resource,$buffer = 4096)
    {
        $this->resource=$resource;
        $this->buffer=$buffer;
    }

    public function getId(){

        return 'resource-' .(int)$this->resource;
    }

    public function getContent(){
        $streamContent = '';
        rewind($this->resource);
        while (feof($this->resource) === false){
            $streamContent .= fread($this->resource,$this->buffer);
        }
        return $streamContent;
    }
}
```


**下面一个类是获取终端命令行的执行结果。**


``` bash
class CommandOutDocument implements Documenttable{
    protected $command;
    public function __construct($command)
    {
        $this->command=$command;
    }
    
    public function getId(){
        return $this->command;
    }

    public function getContent(){
        return shell_exec($this->command);
    }

}

```

**下面我们来演示一下借助上面的三个类来实现DocumentStore类。**

``` bash

$documentStore = new DocumentStore();

//添加html文档
$htmlDoc = new HtmlDocument('https:// www.i360.me');

$documentStore->addDocument($htmlDoc);

//添加流文档

$streamDOC = new StreamDocument(fopen('stream.txt','rb'));

$documentStore->addDocument($streamDOC);

//添加终端命令文档

$cmdDoc = new CommandOutDocument('cat /etc/hosts');

$documentStore->addDocument($command);

print_r($documentStore->getDocuments());die;

```

这里HtmlDocument，StreamDocument，CommandOutDocument这三个类没有任何共同点，只是实现了同一个接口。

