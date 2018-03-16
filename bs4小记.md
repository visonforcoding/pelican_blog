Title: bs4小记
Date: 2018-03-09 14:25:32
Category: python
keywords: python 爬虫


## Attributes 获取属性

一个tag可能有很多个属性. tag class="boldest" 有一个 “class” 的属性,值为 “boldest” . tag的属性的操作方法与字典相同:

```
tag['class'] #获取boldest

```

## 编码
任何HTML或XML文档都有自己的编码方式,比如ASCII 或 UTF-8,但是使用Beautiful Soup解析后,文档都被转换成了Unicode:

