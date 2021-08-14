---
title: jstat命令基本用法
date: 2021-08-14 16:34:37
tags: java
id: 1628930173
---
诊断程序性能问题，或者OOM问题需要查看程序运行的一些统计信息，jstat为我们提供了很多虚拟机运行时的状态信息，它可以显示本地或者远程虚拟机进程中的类加载、内存、垃圾收集、JIT编译等信息。

# 基本用法

我的jdk是1.8.0_191

```
D:。。。。\dir1>jstat -help
Usage: jstat -help|-options
       jstat -<option> [-t] [-h<lines>] <vmid> [<interval> [<count>]]

Definitions:
  <option>      An option reported by the -options option
  <vmid>        Virtual Machine Identifier. A vmid takes the following form:
                     <lvmid>[@<hostname>[:<port>]]
                Where <lvmid> is the local vm identifier for the target
                Java virtual machine, typically a process id; <hostname> is
                the name of the host running the target Java virtual machine;
                and <port> is the port number for the rmiregistry on the
                target host. See the jvmstat documentation for a more complete
                description of the Virtual Machine Identifier.
  <lines>       Number of samples between header lines.
  <interval>    Sampling interval. The following forms are allowed:
                    <n>["ms"|"s"]
                Where <n> is an integer and the suffix specifies the units as
                milliseconds("ms") or seconds("s"). The default units are "ms".
  <count>       Number of samples to take before terminating.
  -J<flag>      Pass <flag> directly to the runtime system.
```

也就是
jstat 选项 进程id 间隔时间 采集次数

其中，选项有多个，例如， -gc， gcnew等等。 间隔时间和次数不是必须的。如果没有间隔时间和次数表示只采集一次。

```
E:\dir1\aaa>jstat  -options
-class
-compiler
-gc
-gccapacity
-gccause
-gcmetacapacity
-gcnew
-gcnewcapacity
-gcold
-gcoldcapacity
-gcutil
-printcompilation
```

# 具体使用
## 加载统计：
jstat -class 7
Loaded Bytes Unloaded Bytes Time
19138 34432.8 36 34.1 13.90

Loaded:加载class的数量
Bytes：所占用空间大小
Unloaded：卸载的class数量
Bytes:卸载释放的空间
Time：加载和卸载耗费的时间

## JIT编译统计
E:>jstat -compiler 16596
Compiled Failed Invalid Time FailedType FailedMethod
8455 3 0 35.63 1 java/util/concurrent/ConcurrentHashMap transfer

Compiled：编译数量。
Failed：失败数量
Invalid：不可用数量
Time：时间
FailedType：失败类型
FailedMethod：失败的方法

## 堆信息统计
E:>jstat -gc 16596
S0C S1C S0U S1U EC EU OC OU MC MU CCSC CCSU YGC YGCT FGC FGCT GCT
2560.0 24064.0 2080.0 0.0 407040.0 296932.8 225792.0 28917.2 60568.0 57891.2 8096.0 7581.1 18 0.535 3 0.293 0.828

S0C：第一个幸存区的大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小
OC：老年代大小
OU：老年代使用大小
MC：方法区大小
MU：方法区使用大小
CCSC:压缩类空间大小
CCSU:压缩类空间使用大小
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

## 堆信息统计2
与-gc基本相同，主要关注堆各个区域使用到的最大最小空间
jstat -gccapacity 7
NGCMN NGCMX NGC S0C S1C EC OGCMN OGCMX OGC OC MCMN MCMX MC CCSMN CCSMX CCSC YGC FGC
174592.0 349184.0 313856.0 44544.0 26112.0 224768.0 349696.0 699392.0 499200.0 499200.0 0.0 1140736.0 105496.0 0.0 1048576.0 13616.0 57 4

NGCMN：新生代最小容量
NGCMX：新生代最大容量
NGC：当前新生代容量
S0C：第一个幸存区大小
S1C：第二个幸存区的大小
EC：伊甸园区的大小
OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代大小
OC:当前老年代大小
MCMN:最小元数据容量
MCMX：最大元数据容量
MC：当前元数据空间大小
CCSMN：最小压缩类空间大小
CCSMX：最大压缩类空间大小
CCSC：当前压缩类空间大小
YGC：年轻代gc次数
FGC：老年代GC次数

## 新生代垃圾回收统计
E:>jstat -gcnew 16596
S0C S1C S0U S1U TT MTT DSS EC EU YGC YGCT
2560.0 24064.0 2080.0 0.0 6 15 24064.0 407040.0 301968.7 18 0.535

S0C：第一个幸存区大小
S1C：第二个幸存区的大小
S0U：第一个幸存区的使用大小
S1U：第二个幸存区的使用大小
TT:对象在新生代存活的次数
MTT:对象在新生代存活的最大次数
DSS:期望的幸存区大小
EC：伊甸园区的大小
EU：伊甸园区的使用大小
YGC：年轻代垃圾回收次数
YGCT：年轻代垃圾回收消耗时间

## 新生代内存统计
E:>jstat -gcnewcapacity 16596
NGCMN NGCMX NGC S0CMX S0C S1CMX S1C ECMX EC YGC FGC
86528.0 1384448.0 527872.0 461312.0 2560.0 461312.0 24064.0 1383424.0 407040.0 18 3

NGCMN：新生代最小容量
NGCMX：新生代最大容量
NGC：当前新生代容量
S0CMX：最大幸存1区大小
S0C：当前幸存1区大小
S1CMX：最大幸存2区大小
S1C：当前幸存2区大小
ECMX：最大伊甸园区大小
EC：当前伊甸园区大小
YGC：年轻代垃圾回收次数
FGC：Full GC次数

## 老年代垃圾回收统计
E:>jstat -gcold 16596
MC MU CCSC CCSU OC OU YGC FGC FGCT GCT
60568.0 57891.2 8096.0 7581.1 225792.0 28917.2 18 3 0.293 0.828

MC：方法区大小
MU：方法区使用大小
CCSC:压缩类空间大小
CCSU:压缩类空间使用大小
OC：老年代大小
OU：老年代使用大小
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

## 老年代内存统计
E:>jstat -gcoldcapacity 16596
OGCMN OGCMX OGC OC YGC FGC FGCT GCT
173568.0 2768896.0 225792.0 225792.0 18 3 0.293 0.828

OGCMN：老年代最小容量
OGCMX：老年代最大容量
OGC：当前老年代大小
OC：老年代大小
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

## 元数据空间统计
D:\dir1>jstat -gcmetacapacity 16596
MCMN MCMX MC CCSMN CCSMX CCSC YGC FGC FGCT GCT
0.0 1101824.0 60568.0 0.0 1048576.0 8096.0 18 3 0.293 0.828

MCMN:最小元数据容量
MCMX：最大元数据容量
MC：当前元数据空间大小
CCSMN：最小压缩类空间大小
CCSMX：最大压缩类空间大小
CCSC：当前压缩类空间大小
YGC：年轻代垃圾回收次数
FGC：老年代垃圾回收次数
FGCT：老年代垃圾回收消耗时间
GCT：垃圾回收消耗总时间

## 总结垃圾回收统计
与-gc基本相同，主要关注已使用空间站总空间比例
D:\dir1>jstat -gcutil 16596
S0 S1 E O M CCS YGC YGCT FGC FGCT GCT
81.25 0.00 70.12 12.81 95.58 93.64 18 0.535 3 0.293 0.828

S0：Survivor0区当前使用比例
S1：Survivor1区当前使用比例
E：伊甸园区使用比例
O：老年代使用比例
M：元数据区使用比例
CCS：压缩使用比例
YGC：年轻代垃圾回收次数， 18次
YGCT：年轻代垃圾回收总耗时， 0.535秒， 也就是平均每次耗时 0.535/18=0.0297秒
FGC：老年代垃圾回收次数 3次
FGCT：老年代垃圾回收消耗时间 0.293秒
GCT：垃圾回收消耗总时间

## JIT编译方法统计
D:\dir1>jstat -printcompilation 16596
Compiled Size Type Method
8444 75 1 com/sun/jersey/core/util/StringIgnoreCaseKeyComparator hash

Compiled：最近编译方法的数量
Size：最近编译方法的字节码数量
Type：最近编译方法的编译类型。
Method：方法名标识。


----------------
原文链接：https://blog.csdn.net/russle/article/details/99702149