Title: python的编码问题
Date: 2018-01-23 12:03:55
Category: python
keywords: python,字符集,编码,unicode,utf-8,ascii

> 使用python2编码经常会遇到一些提示编码的错误,这篇文章将好好捋一下python的编码问题

## ascii

**American Standard Code for Information Interchange** 美国信息交换标准代码,
是基于拉丁字母的一套电脑编码系统,<font color=red>它主要用于显示现代英语</font>.
ASCII第一次以规范标准的类型发表是在1967年，最后一次更新则是在1986年，至今为止共定义了128个字符；其中33个字符无法显示.


![2018-01-23-15-10-24](http://img.rc5j.cn/2018-01-23-15-10-24.png)

上图为ascii所能表示的字符。

而python2的默认编码格式是ascii,如此一来诸如中文、日文等将不能表示,于是就有了之后的unicode


## unicode

Unicode（中文：万国码、国际码、统一码、单一码）是计算机科学领域里的一项业界标准。它对世界上大部分的文字系统进行了整理、编码，使得电脑可以用更为简单的方式来呈现和处理文字。

**Unicode是为了解决传统的字符编码方案的局限而产生的**

## utf-8

UTF-8（8-bit Unicode Transformation Format）是一种针对Unicode的可变长度字符编码，又称万国码。

**utf-8其实就是unicode的一个子集**

## coding=utf-8

参考[https://www.python.org/dev/peps/pep-0263/](!https://www.python.org/dev/peps/pep-0263/) 为了申明python 源代码文件的编码格式，默认是ascii.所以当源代码没有申明utf-8时候，就写入中文会报错。python解释器会不认识这个中文字符。
