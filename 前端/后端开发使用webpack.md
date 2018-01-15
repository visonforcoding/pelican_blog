Title: 后端开发使用webpack
Date: 2018-01-11 14:45:09
Category: 前端

>前端技术发展之快，与我这个后端开发都产生代沟了，感觉最近才弄懂这个东西的用处

## 前言

![2018-01-11-14-52-24](http://img.rc5j.cn/2018-01-11-14-52-24.png)

在了解webpack之前一定要弄懂为什么要用webpack? 为了解答这个为什么,就需要了解es6的module。
建议阅读[《阮一峰的es6入门》](http://es6.ruanyifeng.com/#docs/module)

## 安装

```shell
mkdir webpack-demo && cd webpack-demo
cnpm init -y
cnpm install --save-dev webpack
```
官方推荐本地安装而非全局安装以免造成一些版本兼容带来的困扰，我这里使用cnpm淘宝npm镜像。

## 使用

按照官方首页最简单的例子

![2018-01-11-14-58-18](http://img.rc5j.cn/2018-01-11-14-58-18.png)

我稍稍做了一些改动。

```js
//app.js
import bar from './bar';

bar();
```
在bar.js 中console.log 出了一串信息
```js
//bar.js
export default function bar() {
    //
    console.log('this function has been imported!')
}
```
我将page.html 命名成index.html
```html
<!-- index.html -->
<html>
  <head>
    <title>webpack</title>
  </head>
  <body>
    <h1>hello,webpack</h1>
    <script src="bundle.js"></script>
  </body>
</html>
```

```js
//webpack.config.js
module.exports = {
    entry: './app.js',
    output: {
        filename: 'bundle.js'
    },
    // devServer: {
    //     // contentBase: './dist'
    // },

};
```

## 运行

运用webpack的dev-server可以搭建一个web环境并且可以监听到更改自动运行webpack命令进行构建

```shell
cnpm install --save-dev webpack-dev-server  #安装
```
```
node_modules/.bin/webpack-dev-server #运行dev-server
```

![2018-01-11-15-08-29](http://img.rc5j.cn/2018-01-11-15-08-29.png)

可以看到webpck-dev-server做了几件事情！

1.自动编译了项目

2.在localhost:8080搭建了一个web服务器，通过这个地址可以访问你的项目

![2018-01-11-15-10-34](http://img.rc5j.cn/2018-01-11-15-10-34.png)

访问地址http://localhost:8080可以看得到结果。

## 后记

在我看来webpack提供了几大便利

- 为利用es6 module特性进行开发提供了良好的工具，大大的提高了开发效率
- 帮前端开发搭建了一个webserver,构建了一个比较舒适的开发环境
- 热更新机制减去了每次手动编译的麻烦

用专业术语来解释

- 动态打包(dynamically bundle)