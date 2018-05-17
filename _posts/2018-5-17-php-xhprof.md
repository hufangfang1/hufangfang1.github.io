---
layout: post
title: XHProf报告字段含义的解析
categories: php
description: php xhprof
keywords: lphp xhprof
---

Calls：方法被调用的次数。

Calls%：方法调用次数在同级方法总数调用次数中所占的百分比。

Incl.Wall Time(microsec)：方法执行花费的时间，包括子方法的执行时间。（单位：微秒）

IWall%：方法执行花费的时间百分比。

Excl. Wall Time(microsec)：方法本身执行花费的时间，不包括子方法的执行时间。（单位：微秒）

EWall%：方法本身执行花费的时间百分比。

Incl. CPU(microsecs)：方法执行花费的CPU时间，包括子方法的执行时间。（单位：微秒）

ICpu%：方法执行花费的CPU时间百分比。

Excl. CPU(microsec)：方法本身执行花费的CPU时间，不包括子方法的执行时间。（单位：微秒）

ECPU%：方法本身执行花费的CPU时间百分比。

Incl.MemUse(bytes)：方法执行占用的内存，包括子方法执行占用的内存。（单位：字节）

IMemUse%：方法执行占用的内存百分比。

Excl.MemUse(bytes)：方法本身执行占用的内存，不包括子方法执行占用的内存。（单位：字节）

EMemUse%：方法本身执行占用的内存百分比。

Incl.PeakMemUse(bytes)：Incl.MemUse峰值。（单位：字节）

IPeakMemUse%：Incl.MemUse峰值百分比。

Excl.PeakMemUse(bytes)：Excl.MemUse峰值。单位：（字节）

EPeakMemUse%：Excl.MemUse峰值百分比。