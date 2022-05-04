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

```json
{
    "code":0,
    "message":"0",
    "ttl":1,
    "data":{
        "mid":50995243,
        "name":"岭南城北",
        "sex":"保密",
        "face":"http://i1.hdslb.com/bfs/face/00d2d5a3a1ff49fd0149fd69dc7758bf5baf9f2b.jpg",
        "face_nft":0,
        "sign":"",
        "rank":10000,
        "level":5,
        "jointime":0,
        "moral":0,
        "silence":0,
        "coins":0,
        "fans_badge":false,
        "fans_medal":{
            "show":false,
            "wear":false,
            "medal":null
        },
        "official":{
            "role":0,
            "title":"",
            "desc":"",
            "type":-1
        },
        "vip":{
            "type":2,
            "status":1,
            "due_date":1675008000000,
            "vip_pay_type":1,
            "theme_type":0,
            "label":{
                "path":"",
                "text":"年度大会员",
                "label_theme":"annual_vip",
                "text_color":"#FFFFFF",
                "bg_style":1,
                "bg_color":"#FB7299",
                "border_color":""
            },
            "avatar_subscript":1,
            "nickname_color":"#FB7299",
            "role":3,
            "avatar_subscript_url":"http://i0.hdslb.com/bfs/vip/icon_Certification_big_member_22_3x.png"
        },
        "pendant":{
            "pid":0,
            "name":"",
            "image":"",
            "expire":0,
            "image_enhance":"",
            "image_enhance_frame":""
        },
        "nameplate":{
            "nid":0,
            "name":"",
            "image":"",
            "image_small":"",
            "level":"",
            "condition":""
        },
        "user_honour_info":{
            "mid":0,
            "colour":null,
            "tags":[

            ]
        },
        "is_followed":false,
        "top_photo":"http://i2.hdslb.com/bfs/space/cb1c3ef50e22b6096fde67febe863494caefebad.png",
        "theme":{

        },
        "sys_notice":{

        },
        "live_room":{
            "roomStatus":1,
            "liveStatus":0,
            "url":"https://live.bilibili.com/24510489?broadcast_type=0\u0026is_room_feed=1",
            "title":"新人开播day1",
            "cover":"http://i0.hdslb.com/bfs/live/new_room_cover/84a5824feff0c7756f30886452f588cd75a586ac.jpg",
            "roomid":24510489,
            "roundStatus":0,
            "broadcast_type":0,
            "watched_show":{
                "switch":true,
                "num":21,
                "text_small":"21",
                "text_large":"21人看过",
                "icon":"https://i0.hdslb.com/bfs/live/a725a9e61242ef44d764ac911691a7ce07f36c1d.png",
                "icon_location":"",
                "icon_web":"https://i0.hdslb.com/bfs/live/8d9d0f33ef8bf6f308742752d13dd0df731df19c.png"
            }
        },
        "birthday":"01-01",
        "school":null,
        "profession":{
            "name":"",
            "department":"",
            "title":"",
            "is_show":0
        },
        "tags":null,
        "series":{
            "user_upgrade_status":3,
            "show_upgrade_window":false
        },
        "is_senior_member":0
    }
}
```

## 用户卡片
GET https://api.bilibili.com/x/web-interface/card?jsonp=jsonp&mid=50995243&photo=1

参数 | 意义 | 备注
-|-|-
mid | 用户id | 
photo | 是否显示背景图片 | 0,1

```json
{
    "code":0,
    "message":"0",
    "ttl":1,
    "data":{
        "card":{
            "mid":"50995243",
            "name":"岭南城北",
            "approve":false,
            "sex":"保密",
            "rank":"10000",
            "face":"http://i1.hdslb.com/bfs/face/00d2d5a3a1ff49fd0149fd69dc7758bf5baf9f2b.jpg",
            "face_nft":0,
            "DisplayRank":"0",
            "regtime":0,
            "spacesta":0,
            "birthday":"",
            "place":"",
            "description":"",
            "article":0,
            "attentions":[

            ],
            "fans":713,
            "friend":17, // 关注数
            "attention":17, // 粉丝数
            "sign":"",
            "level_info":{
                "current_level":5,
                "current_min":0,
                "current_exp":0,
                "next_exp":0
            },
            "pendant":{
                "pid":0,
                "name":"",
                "image":"",
                "expire":0,
                "image_enhance":"",
                "image_enhance_frame":""
            },
            "nameplate":{
                "nid":0,
                "name":"",
                "image":"",
                "image_small":"",
                "level":"",
                "condition":""
            },
            "Official":{
                "role":0,
                "title":"",
                "desc":"",
                "type":-1
            },
            "official_verify":{
                "type":-1,
                "desc":""
            },
            "vip":{
                "type":2,
                "status":1,
                "due_date":1675008000000,
                "vip_pay_type":1,
                "theme_type":0,
                "label":{
                    "path":"",
                    "text":"年度大会员",
                    "label_theme":"annual_vip",
                    "text_color":"#FFFFFF",
                    "bg_style":1,
                    "bg_color":"#FB7299",
                    "border_color":""
                },
                "avatar_subscript":1,
                "nickname_color":"#FB7299",
                "role":3,
                "avatar_subscript_url":"http://i0.hdslb.com/bfs/vip/icon_Certification_big_member_22_3x.png",
                "vipType":2,
                "vipStatus":1
            },
            "is_senior_member":0
        },
        "space":{
            "s_img":"http://i2.hdslb.com/bfs/space/768cc4fd97618cf589d23c2711a1d1a729f42235.png",
            "l_img":"http://i2.hdslb.com/bfs/space/cb1c3ef50e22b6096fde67febe863494caefebad.png"
        },
        "following":false,
        "archive_count":19,
        "article_count":0,
        "follower":713,
        "like_num":36705
    }
}
```

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

