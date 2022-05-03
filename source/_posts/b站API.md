---
title: b站API
date: 2022-05-03 10:30:44
tags: 数据
id: 1651545089
---
# 用户
## 用户信息
GET https://api.bilibili.com/x/space/acc/info?mid=5818410

参数 | 意义 | 备注
-|-|-
mid | 用户id | 

## pv
GET https://api.bilibili.com/x/space/upstat?mid=524144317

参数 | 意义 | 备注
-|-|-
mid | 用户id | 

```json
{
    "data":{
        "archive":{
            "view":38420179 // 视频播放数
        },
        "article":{
            "view":18900 // 文章阅读数
        },
        "likes":2106947 // 获赞数
    }
}
```

## 用户状态信息
GET https://api.bilibili.com/x/relation/stat?vmid=5818410
参数 | 意义 | 备注
-|-|-
vmid | 用户id | 

```json
{
 "data":{
        "mid":5818410,
        "following":164, // 关注数
        "whisper":0,
        "black":0,
        "follower":1365298 // 粉丝数
    }
}
```

## 粉丝列表
GET https://api.bilibili.com/x/relation/followers?vmid=50995243&pn=1&ps=20&order=desc

参数 | 意义 | 备注
-|-|-
vmid | 用户id | 
ps | 页数 | 最大5
ps | 取结果的条数 | 默认为20，最大为50

## 关注列表
GET https://api.bilibili.com/x/relation/followings?vmid=5818410&pn=2&ps=20&order=desc

参数 | 意义 | 备注
-|-|-
vmid | 用户id | 
ps | 页数 | 最大5
ps | 取结果的条数 | 默认为20，最大为50

