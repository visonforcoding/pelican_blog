Title: python日志模块logging
Date: 2018-04-02 16:03:55
Category: python
keywords: python,logging

>python的logging模块强大但也复杂，复杂是说用法有很多

```python
import logging  
  
# 创建一个logger  
logger = logging.getLogger('mylogger')  
logger.setLevel(logging.DEBUG)  
  
# 创建一个handler，用于写入日志文件  
fh = logging.FileHandler('test.log')  
fh.setLevel(logging.DEBUG)  
  
# 再创建一个handler，用于输出到控制台  
ch = logging.StreamHandler()  
ch.setLevel(logging.DEBUG)  
  
# 定义handler的输出格式  
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')  
fh.setFormatter(formatter)  
ch.setFormatter(formatter)  
  
# 给logger添加handler  
logger.addHandler(fh)  
logger.addHandler(ch)  
  
# 记录一条日志  
logger.info('foorbar')  
```