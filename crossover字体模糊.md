Title: crossover字体模糊
Date: 2018-04-28 14:31:26
Category: 软件
keywords: mac heidsql

> heidsql真的是最好用的数据库gui程序，其他的都是垃圾，可惜没有mac版本，作者也一直没开发mac版


## 怎么办？

- crossover 安装windows软件

## 字体模糊解决

先从windows系统复制文件simsun.ttf到你的虚拟机下(c:\windows\fonts\),在CrossOver下打开regedit(命令行方式)

找到HKEY_CURRENT_USER\Software\Wine\Fonts\Replacements，删除下面的项目simsun/宋体/新宋体（重要）

ok

参考：http://blog.csdn.net/flyback/article/details/39669003

为安装的虚拟机多c盘位置：

/Users/myname/Library/Application Support/CrossOver/Bottles/myBottleName/drive_c/windows/fonts

CrosseOver的程序安装目录：
/Applications/CrossOver.app/Contents/SharedSupport/CrossOver/bin
