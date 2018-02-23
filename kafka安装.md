Title: kafka安装
Date: 2018-02-08 16:40:27
Category: linux
keywords: kafka java 消息中间件

## 安装jdk

步骤应如下:

1.下载

2.配置环境变量

在linux下使用wget下载总是会下载一个很小的html文件，是因为oracle将那个下载的链接进行过一次302的重定向,简易在本地用浏览器下载找到最终的下载链接，再在linux使用wget下载

```shell
JAVA_HOME=/usr/java
CLASSPATH=$JAVA_HOME/lib/
PATH=$PATH:$JAVA_HOME/bin
export PATH JAVA_HOME CLASSPATH
```
我将下载好的jdk放入了 /usr 下，编辑/etc/profile 配置好环境变量，source /etc/profile使其生效
