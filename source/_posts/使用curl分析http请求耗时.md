---
title: 使用curl分析http请求耗时
date: 2022-08-17 10:11:19
tags:
id: 1660702313
---
# curl -w 选项

> Defines what to display on stdout after a completed and successful operation. 
The format is a string that may contain plain text mixed with any number of variables. 
The string can be specified as "string", to get read from a particular file you specify it "@filename" 
and to tell curl to read the format from stdin you write "@-".

## 示例
```
$ cat curl_format.txt
\n\n
         http_code:  %{http_code}\n
      http_connect:  %{http_connect}\n
      http_version:  %{http_version}\n
        local_addr:  %{local_ip}:%{local_port}\n
       remote_addr:  %{remote_ip}:%{remote_port}\n
      num_connects:  %{num_connects}\n
    num_redirects:   %{num_redirects}\n
       size_upload:  %{size_upload}\n
      speed_upload:  %{speed_upload}\n
     size_download:  %{size_download}\n
    speed_download:  %{speed_download}\n
                   ----------\n
   time_namelookup:  %{time_namelookup}\n
      time_connect:  %{time_connect}\n
   time_appconnect:  %{time_appconnect}\n
     time_redirect:  %{time_redirect}\n
  time_pretransfer:  %{time_pretransfer}\n
time_starttransfer:  %{time_starttransfer}\n
                   ----------\n
        time_total:  %{time_total}\n


$ curl -v -w @curl_format.txt baidu.com
*   Trying 39.156.66.10:80...
* Connected to baidu.com (39.156.66.10) port 80 (#0)
> GET / HTTP/1.1
> Host: baidu.com
> User-Agent: curl/7.79.1
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Date: Wed, 17 Aug 2022 02:24:46 GMT
< Server: Apache
< Last-Modified: Tue, 12 Jan 2010 13:48:00 GMT
< ETag: "51-47cf7e6ee8400"
< Accept-Ranges: bytes
< Content-Length: 81
< Cache-Control: max-age=86400
< Expires: Thu, 18 Aug 2022 02:24:46 GMT
< Connection: Keep-Alive
< Content-Type: text/html
< 
<html>
<meta http-equiv="refresh" content="0;url=http://www.baidu.com/">
</html>
* Connection #0 to host baidu.com left intact


         http_code:  200
      http_connect:  000
      http_version:  1.1
        local_addr:  192.168.11.160:57343
       remote_addr:  39.156.66.10:80
      num_connects:  1
      num_redirects:   0
       size_upload:  0              // The total amount of bytes that were uploaded.
      speed_upload:  0              // The average upload speed that curl measured for the complete upload. Bytes per second.
     size_download:  81             // The total amount of bytes that were downloaded.
    speed_download:  697            // The average download speed that curl measured for the complete download. Bytes per second.
                   ----------
   time_namelookup:  0.007834       // 从开始到完成域名解析的耗时
      time_connect:  0.061172       // 从开始到完成TCP三次握手的耗时
   time_appconnect:  0.000000       // 从开始到完成ssl/ssh等上层协议建立的耗时
     time_redirect:  0.000000       //
  time_pretransfer:  0.061274       // 从请求开始到响应开始传输的时间
time_starttransfer:  0.115972       // 从请求开始到第一个字节将要传输的时间，即Time To First Byte (TTFB) 
                   ----------
        time_total:  0.116154
```

各变量对应的时间点详见下图
![](https://blog.cloudflare.com/content/images/2018/10/Screen-Shot-2018-10-16-at-14.51.29-1.png)

更多可打印变量参考[3]

-------------------------------
参考：  
https://www.cnblogs.com/standby/p/14299844.html  
https://blog.cloudflare.com/a-question-of-timing/  
[3] https://curl.se/docs/manpage.html