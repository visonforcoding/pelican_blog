Title: funkload性能测试
Date: 2017-12-26 13:45
Category: 其他

## 安装

我试了在MacOS下和centos下用常规方法：
```shell
pip install funkload
```
均告诉我失败


![2017-12-26-15-31-13](http://img.rc5j.cn/2017-12-26-15-31-13.png)

Google一番 按stackoverflow上说的

```shell
sudo -H pip install -i https://pypi.python.org/simple/ --trusted-host pypi.python.org  funkload
```
还是不行，最后在github上看到一条

```shell
sudo -H pip install git+https://github.com/nuxeo/FunkLoad.git
```
终于成功!

![2017-12-26-15-34-35](http://img.rc5j.cn/2017-12-26-15-34-35.png)

## 使用

编写脚本

```python
# coding=utf-8
import unittest
from random import random
from funkload.FunkLoadTestCase import FunkLoadTestCase

class Simple(FunkLoadTestCase):
    """This test use a configuration file Simple.conf."""
    def setUp(self):
        """Setting up test."""
        self.server_url = self.conf_get('main', 'url')

    def test_deposit(self):
        self.url = self.conf_get('bench','url')
        self.post(self.url,
          params=[['driver_id', '23'],
                  ['_sign', 'C9F694967B798FD6C70B6F36C182647B']],
          description="Login as scott")
if __name__ in ('main', '__main__'):
    unittest.main()
```

执行命令

```shell
fl-run-bench -c 400 -D 10 test_deposit.py Simple.test_deposit
```

得到结果

![2017-12-27-11-37-58](http://img.rc5j.cn/2017-12-27-11-37-58.png)

