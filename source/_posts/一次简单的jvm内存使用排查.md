---
title: 一次简单的jvm内存使用排查
date: 2021-08-14 16:41:09
tags: java
id: 1628930496
---
# 起因
本地服务起来之后没有请求，但是通过
```
jconsole
```
查看 Eden 区，内存一直稳步升高，达到阈值后被 ygc 回收，不会触发 fgc。但是不正常要弄清楚原因，于是开始排查。

# 经过
## 首
先想到的是 jconsole 对服务的请求导致，于是关闭 jconsole，使用
```sh
$ jps # 查看 pid
97424 
45479 JConsole
45833 AppServiceApplication
45290 GradleDaemon
47467 Jps
45787 GradleDaemon
41086 GradleDaemon

$ jstat -gc pid
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
36864.0 37376.0  0.0    0.0   624128.0 125440.3  194560.0   56782.3   113612.0 108595.5 14848.0 14025.1     28    0.539   6      1.551    2.090
```
再过一段时间
```
$ jstat -gc pid
 S0C    S1C    S0U    S1U      EC       EU        OC         OU       MC     MU    CCSC   CCSU   YGC     YGCT    FGC    FGCT     GCT   
36864.0 37376.0  0.0    0.0   624128.0 169705.6  194560.0   56782.3   113612.0 108595.5 14848.0 14025.1     28    0.539   6      1.551    2.090
```
发现内存增长较大，排除 jconsole jstat 等工具调用

然后关闭 zk, mq 依赖，发现还是有，排除 zk, mq 心跳

## 然
后使用
```
jmap -dump:format=b,file=aaa.hprof pid
```
导出内存，再在 mat 里分析，发现内存泄露非常少，而且没有增加。即增加在 Eden 的内存不属于泄露。

## 然
后使用
```sh
jmap -histo pid > all.txt # 导出堆区所有内存对象
jmap -histo:live pid > gc.txt # 先执行 gc，然后导出堆区所有内存对象
```
发现 all.txt 是
```
 num     #instances         #bytes  class name
----------------------------------------------
   1:        830787       66800632  [C
   2:         97630       26007600  [I
   3:        116249       15713024  [B
   4:        540884       12981216  java.lang.String
   5:        253263       12591912  [Ljava.lang.Object;
```
gc.txt 是
```
 num     #instances         #bytes  class name
----------------------------------------------
   1:        131103       17091664  [C
   2:        129867        3116808  java.lang.String
   3:         32805        2886840  java.lang.reflect.Method
   4:         84468        2702976  java.util.concurrent.ConcurrentHashMap$Node
   5:         22404        2476608  java.lang.Class
   6:         10266        2240408  [B
   7:         25721        1992728  [Ljava.lang.Object;
```
其中 java.lang.String 减少非常多，立即想到不是输入，是不是输出。查看本地服务输出，果然一直有内容在输出。对那个依赖修改，不再输出后，查看 jconsole，果然内存不再增长了。

# 结果
通过这次排查，找到了本地服务内存占用一直升高的原因，并且做了修复，由于线上没有出现同样的问题，不需要修复。同时了解到，日志输出产生的 String 只能等到 gc 的时候被清理。

# 附录
## 打印gc日志
-XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCTimeStamps

## 频繁GC问题或内存溢出问题
一、使用jps查看线程ID

二、使用jstat -gc 3331 250 20 查看gc情况，一般比较关注PERM区的情况，查看GC的增长情况。

三、使用jstat -gccause：额外输出上次GC原因

四、使用jmap -dump:format=b,file=heapDump 3331生成堆转储文件

五、使用jhat或者可视化工具（Eclipse Memory Analyzer 、IBM HeapAnalyzer）分析堆情况。

六、结合代码解决内存溢出或泄露问题。

## 死锁问题
一、使用jps查看线程ID

二、使用jstack 3331：查看线程情况
