Title: webpack引入bootstrap
Date: 2018-01-12 10:24:21
Category: 前端

> 接上一篇,这篇尝试引入bootstrap进行开发

## 安装依赖

```shell
cnpm install jquery --save-dev
cnpm install bootstrap --save-dev
cnpm install bootstrap file-loader style-loader css-loader --save
```

## 更改文件

index.html

```html
<!doctype html>
<html lang="en">

<head>
  <!-- Required meta tags -->
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <!-- Bootstrap CSS -->

  <title>Hello, world!</title>
</head>

<body>
  <nav class="navbar navbar-default">
    <div class="container-fluid">
      <!-- Brand and toggle get grouped for better mobile display -->
      <div class="navbar-header">
        <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1"
          aria-expanded="false">
          <span class="sr-only">Toggle navigation</span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
          <span class="icon-bar"></span>
        </button>
        <a class="navbar-brand" href="#">Brand</a>
      </div>

      <!-- Collect the nav links, forms, and other content for toggling -->
      <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
        <ul class="nav navbar-nav">
          <li class="active">
            <a href="#">Link
              <span class="sr-only">(current)</span>
            </a>
          </li>
          <li>
            <a href="#">Link</a>
          </li>
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown
              <span class="caret"></span>
            </a>
            <ul class="dropdown-menu">
              <li>
                <a href="#">Action</a>
              </li>
              <li>
                <a href="#">Another action</a>
              </li>
              <li>
                <a href="#">Something else here</a>
              </li>
              <li role="separator" class="divider"></li>
              <li>
                <a href="#">Separated link</a>
              </li>
              <li role="separator" class="divider"></li>
              <li>
                <a href="#">One more separated link</a>
              </li>
            </ul>
          </li>
        </ul>
        <form class="navbar-form navbar-left">
          <div class="form-group">
            <input type="text" class="form-control" placeholder="Search">
          </div>
          <button type="submit" class="btn btn-default">Submit</button>
        </form>
        <ul class="nav navbar-nav navbar-right">
          <li>
            <a href="#">Link</a>
          </li>
          <li class="dropdown">
            <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown
              <span class="caret"></span>
            </a>
            <ul class="dropdown-menu">
              <li>
                <a href="#">Action</a>
              </li>
              <li>
                <a href="#">Another action</a>
              </li>
              <li>
                <a href="#">Something else here</a>
              </li>
              <li role="separator" class="divider"></li>
              <li>
                <a href="#">Separated link</a>
              </li>
            </ul>
          </li>
        </ul>
      </div>
      <!-- /.navbar-collapse -->
    </div>
    <!-- /.container-fluid -->
  </nav>
  <div class="container-fluid">

  <div class="jumbotron">
    <h1>Hello, world!</h1>
    <p>...</p>
    <p>
      <a class="btn btn-primary btn-lg" href="#" role="button">Learn more</a>
    </p>
  </div>
  </div>
  <!-- Optional JavaScript -->
  <!-- jQuery first, then Popper.js, then Bootstrap JS -->
  <script src="./bundle.js"></script>
</body>

</html>
```

在app.js当中引入

```js
import bar from './bar'
import 'bootstrap';
import 'bootstrap/dist/css/bootstrap.min.css'

bar();
```

在config当中进行配置

```js
var webpack = require('webpack')
module.exports = {
    entry: './app.js',
    plugins: [
        new webpack.ProvidePlugin({
            "$": "jquery",
            "jQuery": "jquery",
            "window.jQuery": "jquery"
        })
    ],
    output: {
        filename: 'bundle.js'
    },
    // devServer: {
    //     // contentBase: './dist'
    // },
    module: {
        loaders: [
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            { test: /\.eot(\?v=\d+\.\d+\.\d+)?$/, loader: "file-loader" },
            { test: /\.(woff|woff2)$/, loader: "file-loader?prefix=font/&limit=5000" },
            { test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/, loader: "file-loader?limit=10000&mimetype=application/octet-stream" },
            { test: /\.svg(\?v=\d+\.\d+\.\d+)?$/, loader: "file-loader?limit=10000&mimetype=image/svg+xml" }
        ]
    }

};
```

主要是对bootstrap需要依赖jquery的配置,和要引入css、svg、woff等这些文件的配置.随后运行就可以了。