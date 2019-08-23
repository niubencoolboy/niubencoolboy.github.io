---
layout: post
title: "各种攻击原理以及防御方法"
date: 2018-05-13 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - 攻击工具
    - 安全
    - 学习
       
---

# 各种攻击原理以及防御方法

## 1 ARP

### 1.1 原理

[https://zhuanlan.zhihu.com/p/28771785](https://zhuanlan.zhihu.com/p/28771785)

①ARP缓存表基于"后到优先"原则，IP与MAC的映射信息能被覆盖；

②ARP攻击基于伪造的ARP回应包，黑客通过构造"错位"的IP和MAC映射，覆盖主机的ARP表（也被称为"ARP毒化"），最终截取用户的数据流；

③一旦遭受ARP攻击，账号密码都可能被窃取（如果通信协议不是加密的）；

④通过Wireshark数据包分析，我们掌握了真实网络中ARP底层攻击原理及数据包组成。

[https://zhuanlan.zhihu.com/p/28818627](https://zhuanlan.zhihu.com/p/28818627)

### 1.2 防御

[https://zhuanlan.zhihu.com/p/28865553](https://zhuanlan.zhihu.com/p/28865553)


## 2 DDOS

### 2.2 分类

* 死亡之ping（ping of death）
* 泪滴（teardrop）
* UDP洪水（UDP flood）
* SYN洪水（SYN flood）
* Land攻击
* Smurf攻击
* 放大攻击 DNS协议 NTP协议

## 3 缓冲区溢出

## 4 端口扫描

常用端口扫描工具以及使用

## 5 弱口令扫描

hydra

## 5 APT