# -*- coding: utf-8 -*-
"""
2019/8/7

"""

import requests
import pprint
import json
from selenium import webdriver



def get_music(p=1, n=5, w=None):
    """
        p:页数，从1开始
        n:每一页显示的条数
        w:搜索关键字
    """
    # 音乐搜索url
    url = 'https://c.y.qq.com/soso/fcgi-bin/client_search_cp?aggr=1&cr=1&flag_qc=0&p={}&n={}&w={}'.format(p, n, w)
    response = requests.get(url)
    datas = response.text[9: len(text) - 1]
    # 返回songmid
    songmid = json.loads(datas)['data']['song']['list'][0]['songmid']
    print('songmid:' + songmid)

    # 获取token
    token = 'https://c.y.qq.com/base/fcgi-bin/fcg_music_express_mobile3.fcg?format=json205361747&platform=yqq&cid=205361747&songmid={}&filename=C400{}.m4a&guid=126548448'.format(songmid, songmid)
    datas = requests.get(token)
    vkey = datas.json()['data']['items'][0]['vkey']
    print('vkey:' + vkey)

    # 拼接播放链接
    show_music = 'http://ws.stream.qqmusic.qq.com/C400{}.m4a?fromtag=0&guid=126548448&vkey={}'.format(songmid, vkey)
    
    # 在浏览器中播放
    browser = webdriver.Chrome()
    browser.get(show_music)

    # 本地生成音频文件
    # response = requests.get(show_music)
    # with open(songmid+'.m4a', 'wb')as f:
    #     f.write(response.content)
    #     f.close()

    return songmid+'.mp3'


def main():
    name = get_music(w='缘分一道桥')
    print(name)

if __name__ == '__main__':
    main()

