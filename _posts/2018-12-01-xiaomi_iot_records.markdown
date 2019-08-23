---
layout: post
title: "2018 小米IoT安全峰会参会纪要"
date: 2018-12-01 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - 安全
    - 学习
       
---

# 2018 小米IoT安全峰会参会纪要

## 活动介绍

1月29日，MIDC • 2018 小米IoT安全峰会第二届在北京举办，AI、IoT行业的安全专家及爱好者齐聚一堂，在这万物互联新时代，共筑IoT安全生态。

会议主题经过总结，大致可以分为三类：硬件安全、软件安全、运营相关

## 硬件层面

### 演讲题目：IOT Security Reverse Engineering 
### 演讲者：美国东北大学安全研究员Dennis Giese
### 摘要：

主要讲逆向工程的流程，前期的硬件设备准备，摸清电路板引脚，获得硬件设备信息，发现设备漏洞，修改固件等一系列实战指南。

IoT 设计架构里有三个重点，调试接口、固件与存储，都是容易出问题的地方。那比较常见的防护措施有哪些呢？

* 固件来讲就是加密与验签
* 硬件上会有一种叫做一次性存储芯片和eFuses熔断的产品
* 调试口常见的方式就是禁用
* SecureBoot 和Flash整体加密比较少使用，可能开发者觉得比较麻烦或需要额外的成本

### PPT

![DEFCON26-Having_fun_with_IoT-Xiaomi](http://niubencoolboy.github.io/ppt/DEFCON26-Having_fun_with_IoT-Xiaomi.pdf)

### 演讲题目：Beyond logical attacks 
### 演讲者：NXP首席安全技术主管Dr . Markus Hinkelmann
### 摘要：

（1）先讲一下逻辑漏洞，（自己搜的资料）：

常见的逻辑漏洞：

具体可参考：

[Web安全测试中常见逻辑漏洞解析（实战篇）](https://www.freebuf.com/vuls/112339.html)

（2）本报告主要讲的内容

主要是将硬件层面，如利用RSA算法弱点实现电磁分析，侧信道攻击？（不懂）。

利用ZigBee协议实现上的漏洞（临近性检查），远程安装带有木马的固件。（用智能电灯包做的测试）

最后讲述了2017年不断增加的攻击（恶意文件、勒索软件等）。

![](http://niubencoolboy.github.io/img/sec/logical_attacks.png)

### PPT

![Beyond logical attacks](http://niubencoolboy.github.io/ppt/Beyond%20logical%20attacks%20-%20Markus%20Hinkelmann.pdf)



