---
title: prometheus的increase小数问题
date: 2022-06-17 15:37:25
tags: 运维
id: 1655451495
---
# 现象
```
increase(some_counter[5m])
```
明明增加的是整数，increase 出来的却是小数，而且比加的整数还大

# 解释
因为increase()的取值点间隔与真实的5m取样时间区间还是存在两个时间间隔的，也就是durationToStart和durationToEnd，而这两个时间间隔是在计算之外的，也就是说数据采集之间存在空白期，所以采集到的值只能算是一个低精度的近似值。Prometheus在这里使用了数学里的外推法（外推法：在数学里常用的数据精加工方法，通过以最近若干时期的平均值为基础，来计算预测期预期值，把低精度近似值加工到高精度的近似值的一种方法）假定在durationToStart和durationToEnd它就存在增量，用外推法减小每两个取样区间的durationToStart和durationToEnd这两个空白期里的可能存在的增量的误差。

如果空白期内真的存在增量，在这种情况下单纯的只靠取样点之差来构建曲线，数据并不准确的，所以设计团队通过将取样的差值乘以时间间隔之比来求得一个近似值：resultValue * (extrapolateToInterval / sampledInterval)，但是如果不存在增量，那么结果可能有偏差，这个结果只能算作一个高精度的“近似值”。

Prometheus假定了一个区间averageDurationBetweenSamples * 1.1，判断这个空白期是否过大，如果过大则需要一个更大的数字来求得近似值，比如一种情况，在durationToStart和durationToEnd都小于外推推算时间间隔时，则间隔较小：durationToStart + durationToEnd + sampledInterval) / sampledInterval * resultValue取值；另一种情况，在durationToStart和durationToEnd都大于外推推算时间间隔时，则间隔过大：(averageDurationBetweenSamples + sampledInterval) / sampledInterval * resultValue，这个值推算出来则可能比第一种情况更大。

**这个外推算法也是为什么会出现小数点和与真实值存在误差的原因。**

就如下图为例，在两个取样区间零界点前后分别有一个durationToStart和durationToEnd，而这两个时间间隔实际上是increase()函数计算的的空白期。假定没有外推算法，increase()在这两个5分钟的取值区间内获取到的增量应该是总共是1+1=2，而真实增量则是1+2+1=4，在durationToStart和durationToEnd区间内值发生了变化（2到4）。所以如果只是单纯的把取样区间内，最后一个取样值减去第一个取样值是不准确的，无法计算到这个空白期内的增量的，所以需要外推法来做一个趋势的预判得出一个更大一点的近似精确的值，使得结果更加接近真实值。

# 方法
因为取直间隔过大，在生产者一般不会出现两次间隔过大的情况，可以忽略。如果真的出现了可以通过增加真实间隔来减小近似值
```
increase(some_counter[10m])
```

或者使用不同时间点的差值
```
sum(some_counter) - sum(some_counter offset 5m)
```

---------------------------------
参考：  
https://blog.csdn.net/qq_36648860/article/details/115768087  
https://www.jianshu.com/p/8f6a10ec9dbb