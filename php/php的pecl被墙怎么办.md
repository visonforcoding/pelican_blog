Title: php的pecl被墙怎么办
Date: 2018-08-07 16:48:38
Category: php
keywords: php,pecl


> pecl install 无反应

## 解决

可以先去 http://pecl.php.net/ 找到源码下载地址

比如redis

![2018-08-07-16-54-16](http://img.rc5j.cn/2018-08-07-16-54-16.png)

找到下载链接

```shell
pecl install http://pecl.php.net/get/redis-4.1.0RC2.tgz
```

问题解决