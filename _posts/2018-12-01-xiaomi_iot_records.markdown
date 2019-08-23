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

## 1 活动介绍

1月29日，MIDC • 2018 小米IoT安全峰会第二届在北京举办，AI、IoT行业的安全专家及爱好者齐聚一堂，在这万物互联新时代，共筑IoT安全生态。

会议主题经过总结，大致可以分为三类：硬件安全、软件安全、运营相关

## 2 硬件层面

### 2.1 演讲题目：IOT Security Reverse Engineering 
### 演讲者：美国东北大学安全研究员Dennis Giese
### 摘要：

主要讲逆向工程的流程，前期的硬件设备准备，摸清电路板引脚，获得硬件设备信息，发现设备漏洞，修改固件等一系列实战指南。

IoT 设计架构里有三个重点，调试接口、固件与存储，都是容易出问题的地方。那比较常见的防护措施有哪些呢？

* 固件来讲就是加密与验签
* 硬件上会有一种叫做一次性存储芯片和eFuses熔断的产品
* 调试口常见的方式就是禁用
* SecureBoot 和Flash整体加密比较少使用，可能开发者觉得比较麻烦或需要额外的成本

### PPT

[IOT Security Reverse Engineering](http://niubencoolboy.github.io/ppt/DEFCON26-Having_fun_with_IoT-Xiaomi.pdf)

[IOT Security Reverse Engineering（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/IoT%20Reverse%20Engineering%20-%20Dennis%20Giese%EF%BC%88%E4%B8%AD%E6%96%87%E7%89%88%EF%BC%89.pdf)


### 2.2 演讲题目：Beyond logical attacks 
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

[Beyond logical attacks](http://niubencoolboy.github.io/ppt/Beyond%20logical%20attacks%20-%20Markus%20Hinkelmann.pdf)

[Beyond logical attacks（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/Beyond%20logical%20attacks%20-%20Markus%20Hinkelmann.pdf)

## 3 软件层面

### 3.1 演讲题目：小米IOT安全思考与实践 
### 演讲者：小米首席安全官 陈洋
### 摘要：


* 认证层面 保证每台设备预制不同密钥，用户绑定产生唯一关联性的token，利用密钥与token的关系防止越权操作问题。
* 通讯层面 要做流量加密与数据签名。
* 硬件层面 尽可能的把调试接口关掉，并且引入安全芯片，安全存储密钥信息。
* 固件层面 防止固件被篡改，所以验签与防降级的保护是基本要求。
* 系统层面 禁止开启任何处miio外的通讯或管理功能服务端口，局域网是不可信的环境。
* 应用层面 向开发者提供成熟可靠的SDK，减少额外开发带来新的安全风险。

![](http://niubencoolboy.github.io/img/sec/xiaomi_sec.png)

![](http://niubencoolboy.github.io/img/sec/xiaomi_sec02.png)

### PPT

[小米IOT安全思考与实践](http://niubencoolboy.github.io/ppt/27日IoT安全思考与实践-陈洋.pdf)

[小米IOT安全思考与实践（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/27%E6%97%A5IoT%E5%AE%89%E5%85%A8%E6%80%9D%E8%80%83%E4%B8%8E%E5%AE%9E%E8%B7%B5-%E9%99%88%E6%B4%8B.pdf)


### 3.2 演讲题目：IoT + AI + 安全 = ？
### 演讲者：腾讯安全平台部总监 胡珀
### 摘要：

这几年很明显就能感觉到 AI 与 IoT 技术发展迅猛，对我们整个安全行业其实是一个好消息。

因为随着智能设备、AI 的发展，安全问题的影响也变的不同，比如过去的安全可能只是你的钱被盗了，那都有支付保险可以让厂商赔付。

现在被盗的是我们的隐私，比如说AI音箱会放在卧室、客厅，处于用户的敏感生活区域；还有车联网安全问题对我们的人身安全会形成很大的威胁，所以说这些机遇对于我们安全从业者来说其实是个好事。

新技术的发展一定会带来新的安全问题，我们觉得问题主要来自以下方面：

* 手机APP端问题
* 硬件自身问题
* 云端问题
* APP到云端间的通讯问题

![](http://niubencoolboy.github.io/img/sec/ai_sec01.png)

![](http://niubencoolboy.github.io/img/sec/ai_sec02.png)

### PPT

[IoT + AI + 安全 = ？](http://niubencoolboy.github.io/ppt/IoT%20%2B%20AI%20%2B%20%E5%AE%89%E5%85%A8%20%3D%EF%BC%9F%20-%20%E8%83%A1%E7%8F%80.pdf)

[IoT + AI + 安全 = ？（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/IoT%20%2B%20AI%20%2B%20%E5%AE%89%E5%85%A8%20%3D%EF%BC%9F%20-%20%E8%83%A1%E7%8F%80.pdf)

## 4 运营相关

### 4.1 演讲题目：小米IoT隐私合规与实践
### 演讲者：小米隐私律师 朱玲凤
### 摘要：

公司内部的企业合规制度应该怎么建立呢？我根据经验整理了一些比较实用的方法论：

* 建立数据保护官，我们称之为DPO
* 记录。数据如何收集、使用，必须有记录
* 数据保护影响评估
* 安全保护措施
* 数据泄露通知
* 相应行业规则建立细分制度

![](http://niubencoolboy.github.io/img/sec/law_sec.png)


### PPT

[小米IoT隐私合规与实践](http://niubencoolboy.github.io/ppt/小米IoT隐私数据合规实践-朱玲凤.pdf)

[小米IoT隐私合规与实践（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/小米IoT隐私数据合规实践-朱玲凤.pdf)

## 5 其他

### 5.1 演讲题目：大安全下的 IoT 安全威胁演变与应对
### 演讲者：360黑客研究院负责人、独角兽团队创始人杨卿
### 摘要：

从做无线电研究到现在应该有将近十年的时间，归纳出来这个领域的几大特征，一个是危害大；另外一个是修补难度大，修补周期长；最后一个是重视程度低。我们依然在使用一些历史悠久的无线电通信系统，都是一些没有经过足够加密的通信流，这是未来的一个威胁。另外还有ADS-B，蓝牙和无线里面有很多的安全隐患。


### PPT

[大安全下的 IoT 安全威胁演变与应对](http://niubencoolboy.github.io/ppt/%E5%A4%A7%E5%AE%89%E5%85%A8%E4%B8%8B%E7%9A%84%20IoT%20%E5%AE%89%E5%85%A8%E5%A8%81%E8%83%81%E6%BC%94%E5%8F%98%E4%B8%8E%E5%BA%94%E5%AF%B9%20-%20%E6%9D%A8%E5%8D%BF.pdf)

[大安全下的 IoT 安全威胁演变与应对（小米官方地址）](https://cnbj1.fds.api.xiaomi.com/src/xiaomi-IoT-security-conference/%E5%A4%A7%E5%AE%89%E5%85%A8%E4%B8%8B%E7%9A%84%20IoT%20%E5%AE%89%E5%85%A8%E5%A8%81%E8%83%81%E6%BC%94%E5%8F%98%E4%B8%8E%E5%BA%94%E5%AF%B9%20-%20%E6%9D%A8%E5%8D%BF.pdf)



## 6 详情以及其他

见小米官方微信报道

[小米官方微信总报道](https://mp.weixin.qq.com/s/lNO3epqBnCp8R4nfPA_Qsg)
