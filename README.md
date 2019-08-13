# aiofetch

## 简介

- 这是一个知乎 (https://www.zhihu.com/) 爬虫
- 支持 **用户** / **问题** / **回答** / **文章** / **话题** 等主要对象的**几乎全部可见信息**的API
- API支持扩展性强，定制方便

## 依赖

- 使用Python 3编写
- 仅在Python 3.7下测试过，建议使用最新版本Anaconda
```
import sys
import time
import random
import aiohttp
import asyncio
import logging
from multiprocessing import Pool, Manager
```
## 使用步骤
1.将文件夹 `aiofetch` 克隆到本地

2.在IDE中(推荐使用PyCharm)将`aiofetch`加入`Sources Root`中

3.在文件 `aiofetch/headers_pool.py` 和 `aiofetch/zhihu_APIs.py` 中搜索 `<` 或 `>` ，并根据提示替换内容

4.使用 `from data_getter import *` 导入本项目，并根据 `aiofetch/data_getter.py` 中 `if __name__ == '__main__':` 的示例构建请求

5.运行并等待结果返回

## 原理及参数解释

- 本项目基于 `aiohttp`；
- 主要逻辑请参考 `aiofetch/data_getter.py`。
- 返回的字典结构请参考 `aiofetch/data_getter.py`，或参考 `aiofetch/zhihu_APIs.py` 和使用示例少量爬取并手动查看结构。
- `fetch_body` 是请求构建的核心，参数格式及使用示例请参考 `aiofetch/data_getter.py` 和 `aiofetch/zhihu_APIs.py`，请善用IDE的跳转和提示功能。
- `fetch_body` 的元素中的 `"query_args"` 字段用于生成额外查询参数，详情请参考 `aiofetch/zhihu_APIs.py` 中的注释。目前，注释中的参数列举并不完善，可参考文件中的其他对象的url生成函数注释。如有能力，请自行使用Chrome开发者工具抓包分析。
- `fetch_body` 的元素中的 `"range"` 字段用于生成大部分API的必须参数 `offset` (或它的其他形式)和 `limit`，**出于稳定性考虑，请勿尝试在不需要这两个翻页参数的地方使用** `"range"` 字段。
- `"range"` 字段解释: 

  1.`start` :用法同内置函数range()。
  
  2.`end` :用法同内置函数range()。
  
  3.`step` :用法同内置函数range()，可选，默认值参考所选择的func的 `limit`。
  
  4.`limit` : 只请求每 `step` 中的前 `limit`个 ，可选，选用时 `step` 参数为必选，默认值参考所选择的func的 `limit`。

## 输出示例
**关键代码:**
```
FUNC = ZHI.members.followers

FETCH_BODY = [{"identifier": 'zhang-jia-wei',
                   "query_args": ["following_count"], "range":[0, 1]},
                  {"identifier": 'imike', "range": [0, 21, 20, 2]}, ]
```
**输出:**
```
{
    'zhang-jia-wei':
    {
        'paging':
        {
            'is_end': False,
            'is_start': True,
            'next': 'https://www.zhihu.com/members/zhang-jia-wei/followers?include=data%5B%2A%5D.following_count&limit=2&offset=2',
            'previous': 'https://www.zhihu.com/members/zhang-jia-wei/followers?include=data%5B%2A%5D.following_count&limit=2&offset=0',
            'totals': 2262395,
            'identifier': 'zhang-jia-wei'
        },
        'data': [
        {
            'id': 'c69fe0f07b2f640c2829e572251065bb',
            'url_token': 'xia-xia-18-24',
            'name': '夏夏',
            'use_default_avatar': False,
            'avatar_url': 'https://pic4.zhimg.com/v2-cc413975144e318f7f2d72ca03133e92_is.jpg',
            'avatar_url_template': 'https://pic4.zhimg.com/v2-cc413975144e318f7f2d72ca03133e92_{size}.jpg',
            'is_org': False,
            'type': 'people',
            'url': 'https://www.zhihu.com/people/xia-xia-18-24',
            'user_type': 'people',
            'headline': '',
            'gender': -1,
            'is_advertiser': False,
            'vip_info':
            {
                'is_vip': False,
                'rename_days': '60'
            },
            'badge': [],
            'is_following': False,
            'is_followed': False,
            'follower_count': 0,
            'answer_count': 0,
            'articles_count': 0
        },]
    },
    'imike':
    {
        'paging':
        {
            'is_end': False,
            'is_start': True,
            'next': 'https://www.zhihu.com/members/imike/followers?limit=2&offset=2',
            'previous': 'https://www.zhihu.com/members/imike/followers?limit=2&offset=0',
            'totals': 860744,
            'identifier': 'imike'
        },
        'data': [
        {
            'id': 'c78a6368017b0da1ac95a0ba08b808b8',
            'url_token': 'twotk',
            'name': 'Mist',
            'use_default_avatar': False,
            'avatar_url': 'https://pic3.zhimg.com/v2-ee24360e1c36b01755fd224c0e27d00a_is.jpg',
            'avatar_url_template': 'https://pic3.zhimg.com/v2-ee24360e1c36b01755fd224c0e27d00a_{size}.jpg',
            'is_org': False,
            'type': 'people',
            'url': 'https://www.zhihu.com/people/twotk',
            'user_type': 'people',
            'headline': '正在牙牙学语',
            'gender': 1,
            'is_advertiser': False,
            'vip_info':
            {
                'is_vip': False,
                'rename_days': '60'
            }
        },
        {
            'id': 'c4fcea123c0bd07849031b78a4c3c7bf',
            'url_token': 'mimosa-81-23',
            'name': 'Mimosa',
            'use_default_avatar': False,
            'avatar_url': 'https://pic4.zhimg.com/v2-aa18ca0797f565abe8d416a3cbe4a487_is.jpg',
            'avatar_url_template': 'https://pic4.zhimg.com/v2-aa18ca0797f565abe8d416a3cbe4a487_{size}.jpg',
            'is_org': False,
            'type': 'people',
            'url': 'https://www.zhihu.com/people/mimosa-81-23',
            'user_type': 'people',
            'headline': '苦逼的留学生',
            'gender': 1,
            'is_advertiser': False,
            'vip_info':
            {
                'is_vip': False,
                'rename_days': '60'
            }
        },
        {
            'id': '5d80d77df933455265e2ec5887a6d023',
            'url_token': 'zhao-yin-xuan-8',
            'name': '赵梓荀',
            'use_default_avatar': False,
            'avatar_url': 'https://pic2.zhimg.com/v2-72aeaa6a0a6d7c9458da5c6b1f24e21d_is.jpg',
            'avatar_url_template': 'https://pic2.zhimg.com/v2-72aeaa6a0a6d7c9458da5c6b1f24e21d_{size}.jpg',
            'is_org': False,
            'type': 'people',
            'url': 'https://www.zhihu.com/people/zhao-yin-xuan-8',
            'user_type': 'people',
            'headline': '好奇大千世界',
            'gender': 0,
            'is_advertiser': False,
            'vip_info':
            {
                'is_vip': False,
                'rename_days': '60'
            }
        }]
    }
}
```

**关键代码:**
```
FUNC = ZHI.questions.log

FETCH_BODY = [{"identifier": 334583368}, ]
```
**输出:**
```
{
    334583368:
    {
        'identifier': 334583368,
        'html': '<!DOCTYPE html><html lang="zh-CN"><body></body></html>'
    }
}
```
## 其他

*The following statements are translated by youdao*

The crawler is asynchronous multi-process crawler, efficient and easy to use, easy to expand

It is recommended to set up storage locally, and not to cause too much unnecessary pressure on the servers of zhihu

The crawler only warns of HTTP errors and does not stop running

Please use the browser to log in zhihu and manually pass the man-machine verification.

If you encounter a quick jump in the human-machine verification interface, please press Esc to stop

It is recommended to crawl the content in batches, and control 'task_count' of each batch within 100000

Some apis do not require cookie to simulate login, and this crawler USES cookie by default

If you have personalized requirements, you can directly modify the source code

Any Suggestions from the visitors are welcome

Star this repo if you think it`s OK~

zhihu[@stringstrange](https://www.zhihu.com/people/.people./activities).
