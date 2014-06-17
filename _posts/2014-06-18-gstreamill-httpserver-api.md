---
layout: post
title: gstreamill
category: products
---

调用httpserver_start接口，注册一个回调函数。

无论是request还是continue都要处理毁掉函数的三种可能的返回值。

httpserver根据回调函数的返回值进行处理，有三种可能的返回值，

* 返回0则结束
* 返回正数则等待这个数值的时间
* 返回GST_CLOCK_TIME_NONE则处于阻塞状态。

上层对于httpserver的使用都应按照这个接口
