Title: php获取http请求数据
Date: 2018-06-11 18:30:55
Category: php
keywords:

> $_POST $_REQUEST 和 php://input 具体区别真的了解么


### $_POST

当 HTTP POST 请求的 Content-Type 是 application/x-www-form-urlencoded 或 multipart/form-data 时，会将变量以关联数组形式传入当前脚本。


### php://input

php://input 是个可以访问请求的原始数据的只读流。 POST 请求的情况下，最好使用 php://input 来代替 $HTTP_RAW_POST_DATA，因为它不依赖于特定的 php.ini 指令。 而且，这样的情况下 $HTTP_RAW_POST_DATA 默认没有填充， 比激活 always_populate_raw_post_data 潜在需要更少的内存。 enctype="multipart/form-data" 的时候 php://input 是无效的。


### $_REQUEST

默认情况下包含了 $_GET，$_POST 和 $_COOKIE 的数组。
