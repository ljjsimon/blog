---
title: python操作蒙版和阿里云抠图客户端
date: 2023-03-18 16:47:58
tags: 代码片段
id: 1679129307
---
```python
# -*- coding: utf-8 -*-
# This file is auto-generated, don't edit it. Thanks.
import sys


from alibabacloud_imageseg20191230.client import Client as imageseg20191230Client
from alibabacloud_tea_openapi import models as open_api_models
from alibabacloud_imageseg20191230 import models as imageseg_20191230_models
from alibabacloud_tea_util import models as util_models
#from alibabacloud_tea_console.client import Client as ConsoleClient
from alibabacloud_tea_util.client import Client as UtilClient
import cv2
import requests
import string
import random

from PIL import Image
import numpy as np


class AliClient:
    def __init__(self):
        pass

    @staticmethod
    def create_client(
        access_key_id: str,
        access_key_secret: str,
    ) -> imageseg20191230Client:
        """
        使用AK&SK初始化账号Client
        @param access_key_id:
        @param access_key_secret:
        @return: Client
        @throws Exception
        """
        config = open_api_models.Config(
            # 必填，您的 AccessKey ID,
            access_key_id=access_key_id,
            # 必填，您的 AccessKey Secret,
            access_key_secret=access_key_secret
        )
        # 访问的域名
        config.endpoint = f'imageseg.cn-shanghai.aliyuncs.com'
        return imageseg20191230Client(config)

    @staticmethod
    def create_client_with_sts(
        access_key_id: str,
        access_key_secret: str,
        security_token: str,
    ) -> imageseg20191230Client:
        """
        使用STS鉴权方式初始化账号Client，推荐此方式。本示例默认使用AK&SK方式。
        @param access_key_id:
        @param access_key_secret:
        @param security_token:
        @return: Client
        @throws Exception
        """
        config = open_api_models.Config(
            # 必填，您的 AccessKey ID,
            access_key_id=access_key_id,
            # 必填，您的 AccessKey Secret,
            access_key_secret=access_key_secret,
            # 必填，您的 Security Token,
            security_token=security_token,
            # 必填，表明使用 STS 方式,
            type='sts'
        )
        # 访问的域名
        config.endpoint = f'imageseg.cn-shanghai.aliyuncs.com'
        return imageseg20191230Client(config)
    
    # 只支持png
    @staticmethod
    def parseMask(
        src: str
    ):
        """
        图片转反色黑白mask
        原来透明的部分为白色，不透明部分为黑色
        """
        image = cv2.imread(src, flags=-1)
        b,g,r,alpha = cv2.split(image)
        #alpha = cv2.imread(src)
        butmask = cv2.bitwise_not(alpha)
        mask_file = src[:-4] + '_mask.png'
        cv2.imwrite(mask_file, butmask)
        return mask_file

    @staticmethod
    def alphaBlend(
        foreground: str,
        background: str,
    ):
        """
        重叠两张图片
        """
        
        """
        img1 = Image.open(foreground)
        img1 = img1.convert("RGBA")
        img2 = Image.open(background)
        img2 = img2.convert("RGBA")
        #r, g, b, alpha = img2a.split()
        #img2b = img2.convert("L")
        return Image.alpha_composite(img2, img1)
        """
        img1 = cv2.imread(foreground, cv2.IMREAD_UNCHANGED)
        img2 = cv2.imread(background)
        alpha = img1[:,:,3] / 255.0
        
        img2[:,:,0] = img2[:,:,0] * (1-alpha) + img1[:,:,0] * alpha
        img2[:,:,1] = img2[:,:,1] * (1-alpha) + img1[:,:,1] * alpha
        img2[:,:,2] = img2[:,:,2] * (1-alpha) + img1[:,:,2] * alpha

        cv2.imwrite(background[:-1] + '_final.png', img2)

        return img2


    @staticmethod
    def segmentImage(
        src_file: str
    ) -> str:
        # 工程代码泄露可能会导致AccessKey泄露，并威胁账号下所有资源的安全性。以下代码示例仅供参考，建议使用更安全的 STS 方式，更多鉴权访问方式请参见：https://help.aliyun.com/document_detail/378659.html
        client = AliClient.create_client('key', 'key')

        with open(src_file, 'rb') as f:
            segment_body_request = imageseg_20191230_models.SegmentBodyAdvanceRequest(
                image_urlobject = f
            )
            runtime = util_models.RuntimeOptions()
            resp = client.segment_body_advance(segment_body_request, runtime)
            #ConsoleClient.log(UtilClient.to_jsonstring(resp))
            resp_map = UtilClient.to_map(resp)
            r = requests.get(resp_map["body"]["Data"]["ImageURL"])

            file_name = src_file[:-4] + '_matting.png'
            with open(file_name, 'wb') as code:
                code.write(r.content)
            
            return file_name

    @staticmethod
    def generateRandomStr(randomlength=6):
        return ''.join(random.sample(string.ascii_letters + string.digits, randomlength))

if __name__ == "__main__":
    #a = AliClient.segmentImage("/Users/linjiangjian/Downloads/data/leg_s.jpg", ".")
    #print(a)
    img2 = AliClient.alphaBlend('output/5NAT7f_matting.png', 'output/8POjxY.png')
    cv2.imshow("img", img2)
    cv2.waitKey(0)
```
