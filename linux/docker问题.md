Title: docker问题
Date: 2018-08-16 20:52:31
Category: linux
keywords: docekr，docekr问题

>总结使用docker中遇到的问题


## bash执行不了

**操作**
```
docker-compose exec swagger-editor bash
```

**问题**
```
OCI runtime exec failed: exec failed: container_linux.go:348: starting container process caused "exec: \"bash\": executable file not found in $PATH": unknown
```

**解决**

原因是该容器并没有bash，所以尝试用sh

```
docker-compose exec swagger-ui sh
```

这样就可以了

## push 无权限 denied

```
docker push visonforcoding/php7.2:php7.2-fpm-latest
```

**结果**

![2018-08-20-18-24-48](http://img.rc5j.cn/2018-08-20-18-24-48.png)

**原因**

tag不存在且未登录

```
 docker tag 73e1d96ecf52 visonforcoding/php7.2:kafka-ext
```
需要用docker login 进行登录

如果没有账号，需要去https://hub.docker.com 进行创建

```
docker login
```
![2018-08-20-18-27-54](http://img.rc5j.cn/2018-08-20-18-27-54.png)

再进行push

```
docker push visonforcoding/php7.2:kafka-ext
```


