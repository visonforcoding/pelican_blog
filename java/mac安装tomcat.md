Title: mac安装tomcat
Date: 2018-06-18 15:15:46
Category: java
keywords:


## 安装

```bash
brew install tomcat

```

## 运行

```
catalina start
```

访问http://localhost:8080

## 更改 webapps

```bash
cd /usr/local/opt/tomcat/libexec/conf
```

更改 host appBase值 再重启 tomcat


## web.xml