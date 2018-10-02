---
title: python拉取云音乐数据
tag: python
date: 2018-10-02
---

不多说，先看代码

```
"""抓取云音乐内容
"""
import base64
import random
from Crypto.Cipher import AES

import binascii
import json
import requests
import pymongo


def random_b(b):
    """生成一个任意长度随机数

    Arguments:
        b {[type]} -- [description]

    Returns:
        [type] -- [description]
    """

    return bytes(''.join(random.sample(
        'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789',
        b
    )), 'utf-8')


PUB_KEY = '010001'

MODULUS = '00e0b509f6259df8642dbc35662901477df22677ec152b5ff68ace615bb7b725152b3ab17a876aea8a5aa76d2e417629ec4ee341f56135fccf695280104e0312ecbda92557c93870114af6c9d05c4f7f0c3685b7a46bee255932575cce10b424d813cfe4875d3e82047b97ddef52741d546b8e289dc6935b3ece0462db0a22b8e7'

SECRET_KEY = '0CoJUm6Qyw8W8jud'


def aes_encrypt(text, key):
    """ase加密

    Arguments:
        text {[type]} -- [description]
        key {[type]} -- [description]
    """
    # 偏移量
    iv = b'0102030405060708'
    # 对长度不是16倍数的字符串进行补全，然后在转为bytes数据
    pad = 16 - len(text) % 16
    try:
        # 如果接到bytes数据（如第一次aes加密得到的密文）要解码再进行补全
        text = text.decode()
    except:
        pass
    text = text + pad * chr(pad)
    try:
        text = text.encode()
    except:
        pass

    encryptor = AES.new(key, AES.MODE_CBC, iv)
    ciphertext = encryptor.encrypt(text)
    ciphertext = base64.b64encode(ciphertext).decode(
        'utf-8')  # 得到的密文还要进行base64编码
    return ciphertext


def rsa_encrypt(random_char):
    text = random_char[::-1]  # 明文处理，反序并hex编码
    rsa = int(binascii.hexlify(text),
              16) ** int(PUB_KEY, 16) % int(MODULUS, 16)
    return format(rsa, 'x').zfill(256)


def request_param(data):
    """构造请求参数

    Arguments:
        data {[type]} -- [description]
    """
    text = json.dumps(data)
    random_char = random_b(16)
    params = aes_encrypt(text, SECRET_KEY)  # 两次aes加密
    params = aes_encrypt(params, random_char)
    enc_sec_key = rsa_encrypt(random_char)
    data = {
        'params': params,
        'encSecKey': enc_sec_key
    }
    return data


HEADERS = {
    'Connection': 'keep-alive',
    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36',
    'Host': 'music.163.com',
    'Origin': 'https://music.163.com',
}


def find_song_content(song_id):
    """根据歌曲ID查询歌词

    Arguments:
        song_id {[type]} -- [description]
    """
    referer = 'https://music.163.com/song?id=' + song_id
    HEADERS['referer'] = referer
    url = 'https://music.163.com/weapi/song/lyric?csrf_token='
    param = {'id': song_id, 'lv': -1, 'tv': -1, 'csrf_token': ''}
    data = request_param(param)
    result = requests.post(url, data=data, headers=HEADERS)
    result = result.json()
    return result['lrc']['lyric']


client = pymongo.MongoClient('mongodb://127.0.0.1:27017/')
db = client.song
detail_db = db.song_detail


def save_songs(songs):
    for song in songs:
        song_id = song['id']
        song_content = find_song_content(str(song_id))
        song_name = song['name']
        song_arr = song['ar']
        songer = []
        for ar in song_arr:
            song_dict = {'id': ar['id'], 'name': ar['name']}
            songer.append(song_dict)
        detail_db.insert({'song_id': song_id, 'song_name': song_name,
                          'song_ar': songer, 'song_content': song_content})


if __name__ == '__main__':
    query_url = 'https://music.163.com/weapi/cloudsearch/get/web?csrf_token='
    data = {"hlpretag": "<span class=\"s-fc7\">",
            "hlposttag": "</span>",
            "s": "新说唱",
            "type": "1",
            "offset": "0",
            "total": "true",
            "limit": "30",
            "csrf_token": ""
            }
    data = request_param(data)
    referer = 'https://music.163.com/search/'
    HEADERS['Referer'] = referer
    result = requests.post(query_url, data=data, headers=HEADERS)
    result = result.json()
    songs = result['result']['songs']
    song_count = result['result']['songCount']
    save_songs(songs)

    # 抓取其它页内容
    for num in range(int(song_count / 30 - 1)):
        data_other = {"hlpretag": "<span class=\"s-fc7\">",
                      "hlposttag": "</span>",
                      "s": "新说唱",
                      "type": "1",
                      "offset": str((num + 1) * 30),
                      "total": "false",
                      "limit": "30",
                      "csrf_token": ""
                      }
        data_other = request_param(data_other)
        result_other = requests.post(
            query_url, data=data_other, headers=HEADERS)
        result_other = result_other.json()
        songs_other = result_other['result']['songs']
        save_songs(songs_other)
```

一般情况下网站都是有反抓取策略的，现在来看一下云音乐的方式。

主要包括以下几个方面：
1. AES（Advanced Encryption Standard）对称加密算法是一种高级数据加密标准，是美国联邦政府采用的一种区块加密标准，可有效抵制针对DES的攻击算法。特点：密钥建立时间短、灵敏性好、内存需求低、安全性高。
2. RSA加密算法是一种非对称加密算法。在公开密钥加密和电子商业中RSA被广泛使用。到目前为止，世界上还没有任何可靠的攻击RSA算法的方式。只要其钥匙的长度足够长，用RSA加密的信息实际上是不能被解破的。
3. 动态16位随机数。
4. RSA是非对称加密，无法通过encSecKey解密出入参，没有入参也就无法加密生成params，所以只能通过chrome调试js每个接口，观察请求的构造。

理解了以上步骤就可以自由下载内容了