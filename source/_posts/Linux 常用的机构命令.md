---
title: Linux 的几个常用的命令
date: 2017-12-05 11:01:01
tags: 
- Linux
top: 90
categories: Linux
---

给某个文件权限（以后有时间总结一篇关于权限的文章）
```bash
chown -R www:www /home/wwwroot 
```

查看链接服务器的ip
```bash
 netstat -an | grep ESTABLISHED
 netstat -an | grep 192.168.1.99
```


centos7 开启80端口
```bash
iptables -I INPUT -p tcp --dport 80 -m state --state NEW -j ACCEPT
```

telnet 某个端口
```bash
telnet 172.0.0.101 8001

```
退出
```bash
ctrl + ]  然后 quit  注意，后面没有 ‘;’分号
```
统计80端口链接数
```bash
netstat -nat|grep -i "80"|wc -l
```