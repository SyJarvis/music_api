# python调用QQ音乐API



1. 音乐搜索接口、其他接口
2. 获取token
3. 拼接播放地址

先上一份代码：

```python
import requests
import pprint
import json
from selenium import webdriver

# 音乐搜索接口

p, n, w = 1, 5, '碰'
url_1 = 'https://c.y.qq.com/soso/fcgi-bin/client_search_cp?aggr=1&cr=1&flag_qc=0&p={}&n={}&w={}'.format(p, n, w)
response1 = requests.get(url_1)
# 获取返回参数并且删除多余空格
text = response1.text.strip()                 
# 删除多余的字符使之符合json格式进行转换
j_datas = json.loads(text[9 : len(text) - 1])   
songmid = j_datas['data']['song']['list'][0]['songmid'] # 获取songmid
print('songmid:'+ songmid)

url_2 = 'https://c.y.qq.com/base/fcgi-bin/fcg_music_express_mobile3.fcg?format=json205361747&platform=yqq&cid=205361747&songmid={}&filename=C400{}.m4a&guid=126548448'.format(songmid, songmid)
response2 = requests.get(url_2)
#这个返回参数不会出错，可以直接转换json格式，并获取vkey
vkey = response2.json()['data']['items'][0]['vkey']        
print('vkey:' + vkey)

url_3 = 'http://ws.stream.qqmusic.qq.com/C400{}.m4a?fromtag=0&guid=126548448&vkey={}'.format(songmid, vkey)
borwser = webdriver.Chrome()
borwser.get(url_3)

```



## 解析

#### 音乐搜索接口

**url:**

```
https://c.y.qq.com/soso/fcgi-bin/client_search_cp?aggr=1&cr=1&flag_qc=0&p=1&n=30&w=碰
```

- p：页数，从1开始
- n：每一页显示的条数
- w：搜索关键字

通过p和n参数，可以实现分页请求加载。

链接可以放在浏览器上查看返回的数据格式，返回的数据需要进行处理，因为要转为json格式，所以要把**callback(**这段字符以及最后面的 “**)**” 这个字段删除，以符合json格式。

下面是返回的参数（不完整）：

![1565188404354](C:\Users\SYjarvis\AppData\Roaming\Typora\typora-user-images\1565188404354.png)

![1565188444555](C:\Users\SYjarvis\AppData\Roaming\Typora\typora-user-images\1565188444555.png)

参数：

* curPage：当前页数
* totalnum：总数
* list：返回的30条歌曲的数据
  * songname：歌曲名称
  * singer：歌手名称，是个列表结构
  * albumname：专辑名称
  * songmid:歌曲标识



#### 获取token

```
'https://c.y.qq.com/base/fcgi-bin/fcg_music_express_mobile3.fcg?format=json205361747&platform=yqq&cid=205361747&songmid=0014kBdU3LPmCA&filename=C4000014kBdU3LPmCA.m4a&guid=126548448'
```

##### 参数：

* songmid：在歌曲信息中找
* filename：根据songmid生成C400 + songmid + .m4a



##### 返回参数：

![1565188591832](C:\Users\SYjarvis\AppData\Roaming\Typora\typora-user-images\1565188591832.png)

* expiration：有效时长
* vkey：播放歌曲密钥



#### 拼接播放地址

```
http://ws.stream.qqmusic.qq.com/C4000014kBdU3LPmCA.m4a?fromtag=0&guid=126548448&vkey=286DB371BE286D676A4B0D7ACD2B49CD488389A3EF91B7728F80B52E2B4691CCD8028F5EAEFD4413F91E98D5D3CC7484B3C2C09EC61D131E
```

vkey:在token中的vkey字段

将播放地址放到浏览器中可以播放