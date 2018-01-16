Title: 抓取mac壁纸
Date: 2017-11-23 14:19
Category: 其他
>如图，一个好的工作环境，可以让心情好不少

![image.png](http://upload-images.jianshu.io/upload_images/4033700-3a9f37b2be736bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
抓取的是爱壁纸的资源，它们最多只提供20页一个类别，但是一页有60张。
总共有11个类别，就是有20x60x11张。我这里只筛选了2种类别，看你需要了。

话不多说，直接上代码吧

```python
# coding=utf-8
from pymongo import MongoClient
import requests
import logging
import uniout
import json
import logger
import threading
import time
import sys
import os




# 创建 日志 对象
logger = logging.getLogger()
handler = logging.StreamHandler()
formatter = logging.Formatter(
    '%(asctime)s %(name)-12s %(levelname)-8s %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)
logger.setLevel(logging.DEBUG)

# mongodb
mongoconn = MongoClient('127.0.0.1', 27017)
mdb = mongoconn.data_analysis
das_collection = mdb.bizhi

#
categories = {
    "moviestar": 1,
    "landscape": 2,
    "beauty": 3,
    "plant": 4,
    "animal": 5,
    "game": 6,
    "cartoon": 7,
    "festival": 8,
    "car": 798,
    "food": 1546,
    "sport": 1554
};

# pic path
pic_path = '/Library/Desktop Pictures/'


def scrapy_it(page, tid, width=2560, height=1600):
    # 地址
    start_url = '''http://api.lovebizhi.com/macos_v4.php?a=category&tid=%d&
    device=105&uuid=436e4ddc389027ba3aef863a27f6e6f9&mode=0&retina=1&
    client_id=1008&device_id=31547324&model_id=105&size_id=0&channel_id=
    70001&screen_width=%d&screen_height=%d&version_code=19&order=newest&color_id=3&p=%d''' % (
        tid, width, height, page)
    print start_url
    res = requests.get(start_url)
    content = res.json()
    return content


def getFilename(url):
    url_split = url.split('/')
    name = url_split[len(url_split) - 1]
    name_split = name.split(',')
    ext_split = name.split('.')
    ext = ext_split[1]
    return name_split[0] + '.' + ext


def store_it(follow):
    if das_collection.find_one({'id': follow['id']}):
        logging.debug(u'%s在库中已存在' % follow['id'])
    else:
        print type(follow['id'])
        logging.debug('插入记录:%s' % follow['id'])
        das_collection.insert_one(follow)


def download_it(link, filename):
    if os.path.exists(filename):
        logging.info(u'图片%s已存在' % filename)
        return False
    res = requests.get(link)
    with open(filename, 'wb') as f:
        f.write(res.content)
    logging.info(u'存入图片%s' % filename)


if __name__ == '__main__':

    tids = [categories["landscape"], categories['plant']]
    for tid in tids:
        for p in range(1, 21):
            res = scrapy_it(p, tid)
            data_list = res['data']
            for data in data_list:
                if 'vip_original' in data['image']:
                    img_url = data['image']['vip_original']
                else:
                    img_url = data['image']['original']
                filename = pic_path + getFilename(img_url)
                download_it(img_url, filename)

```