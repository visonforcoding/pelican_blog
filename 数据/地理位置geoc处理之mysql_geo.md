Title: 地理位置geo处理之mysql geo 索引
Date: 2017-12-12 5:50
Category: 方案

> 目前越来越多的业务都会基于LBS，附近的人，外卖位置，附近商家等等，现就讨论离我最近这一业务场景的解决方案。

目前已知解决方案有:

- mysql 自定义函数计算
- mysql geo索引
- mongodb geo索引
- postgresql PostGis索引
- redis geo
- ElasticSearch

本文测试下mysql geo索引 函数运算的性能

## 准备工作

### 创建数据表

```sql
CREATE TABLE `driver_1` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `loc` point NOT NULL,
  PRIMARY KEY (`id`),
  SPATIAL KEY `id_loc_index` (`loc`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

## 创建数据python脚本

```python
# coding=utf-8
from peewee import MySQLDatabase, Model
import logging
import random
import threading
""" 中国的经纬度范围 纬度3.86~53.55，经度73.66~135.05。大概0.00001度差距1米 """

# 创建 日志 对象
logger = logging.getLogger()
handler = logging.StreamHandler()
formatter = logging.Formatter(
    '%(asctime)s %(name)-12s %(levelname)-8s %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)
logger.setLevel(logging.DEBUG)

# Connect to the database

db = MySQLDatabase('dbtest', user='root', password='')


class BaseModel(Model):
    class Meta:
        database = db


class Driver(BaseModel):
    class Meta:
        db_table = 'driver_1'


def ins_driver(thread_name, nums):
    logger.info('开启线程%s' % thread_name)
    for _ in range(nums):
        lng = '%.5f' % random.uniform(73.66, 135.05)
        lat = '%.5f' % random.uniform(3.86, 53.55)

        db.execute_sql(
            "INSERT INTO driver_1 (`loc`) VALUES(ST_GEOMFROMTEXT('POINT(%s %s)'))",
            (float(lat), float(lng)))


thread_nums = 10
for i in range(thread_nums):
    t = threading.Thread(target=ins_driver, args=(i, 40000))
    t.start()

```
![2017-12-13-11-09-11](http://img.rc5j.cn/2017-12-13-11-09-11.png)



以上脚本创建10个线程，10个线程插入4万条数据。耗费65.50s执行完,总共插入40万条数据

## 测试

- 测试环境

系统：mac os

内存：16G

cpu: intel core i5

硬盘: 500g 固态硬盘

测试下查找距离(134.38753,18.56734)附近20公里的司机

```sql
SELECT  *  
    FROM    driver_1  
    WHERE   MBRContains  
                    (  
                    LineString  
                            (  
                            Point  
                                    (  
                                    18.56734 + 20 / ( 111.1 / COS(RADIANS(134.38753))),  
                                    134.38753 + 20 / 111.1  
                                    ),  
                            Point  
                                    (  
                                    18.56734 - 20 / ( 111.1 / COS(RADIANS(134.38753))),  
                                    134.38753 - 20 / 111.1  
                                    )   
                            ),  
                    loc  
                    ) 
```
- 耗时：0.0006s
- explain:使用索引


![2017-12-13-11-50-14](http://img.rc5j.cn/2017-12-13-11-50-14.png)




