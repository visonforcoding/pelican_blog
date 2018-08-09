Title: php+kafka使用
Date: 2018-08-09 11:33:06
Category: 数据
keywords: php,kafka,消息队列

> 在之前的文章介绍了,消息队列应用场景，也单独介绍了kafka现在介绍下在php中如何使用kafka

## 安装php-rdkafka拓展

以ubuntu 为例

```bash

## 1.安装librdkafka
apt-get install wget
wget https://github.com/edenhill/librdkafka/archive/master.zip

apt-get install unzip

unzip master.zip

cd librdkafka

./configure 

make

make install 

## 2.安装rdkafka拓展
pecl install http://pecl.php.net/get/rdkafka-3.0.4.tgz

#在php.ini 配置启用 rdkafka拓展

```



