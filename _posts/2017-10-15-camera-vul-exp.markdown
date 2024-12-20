---
layout: post
title: "摄像头漏洞实验"
date: 2017-10-15 周日
author: "John Niu"
header-img : "img/170827/bg.jpg"
tags:
    - 视频监控
    - 安全
 
---


# 实现内容

实现一个小目标：
从互联网上找一个已经开放的IOT摄像头poc漏洞信息，从互联网抓取多个存在该漏洞的IP地址，实现利用Poc对目标摄像头实施自动化的漏洞利用，获得目标的最高权限。

# 整体思路

1. 调研
1. 选取漏洞
1. 写POC
1. 利用漏洞提权
1. 自动化测试
1. 结果展示
1. 参考相关链接

## 调研

- 因为本人之前主要做入侵检测工作，很少做漏洞相关的工作，所以前期必须调用清楚。感谢您给的参考，pocsuite是个好东西。
- 正好赶上十一，本来打算十一回家不玩，好好搞，把他搞出来，后来发现我想多了，在家还是从最基本的书看起，把《Kali Linux渗透测试技术详解》读了。
- 十一回来后，向实验室的博士师兄请教了一下，该如何着手。
- 调研pocsuite。

## 选取漏洞

### CVE-2017-7923

#### 漏洞概述

海康威视自 2014 年以后的 IP 摄像头产品被爆出存在后门，当攻击者构造一个包含 base64(admin:password) 字段的请求时，将被后台识别为一个特别的用户，攻击者可能利用该漏洞提升权限，获取或修改设备信息。该后门最早于 2017 年 03 月 05 日被发现，2017 年 03 月 14 日，海康威视官方发布安全预警，2017 年 05 月 05 日，该漏洞被 CVE 收录(CVE-2017-7923)。

#### 影响版本

产品型号| 受影响版本
---|---
DS-2CD2xx2F-I Series | V5.2.0 build 140721 to V5.4.0 Build 160530
DS-2CD2xx0F-I Series | V5.2.0 build 140721 to V5.4.0 Build 160401
DS-2CD2xx2FWD Series | V5.3.1 build 150410 to V5.4.4 Build 161125
DS-2CD4x2xFWD Series | V5.2.0 build 140721 to V5.4.0 Build 160414
DS-2CD4xx5 Series | V5.2.0 build 140721 to V5.4.0 Build 160421
DS-2DFx Series | V5.2.0 build 140805 to V5.4.5 Build 160928
DS-2CD63xx Series | V5.0.9 build 140305 to V5.3.5 Build 160106

#### CVE-2017-7923 漏洞覆现

Zoomeye 搜索 dork = “hikvision ip camera httpd”，选取一个IP，在浏览器输入ip+/Security/user?auth=“base64(admin:password)” 这里admin 与 password随意，然后使用base64加密。

**展示**

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7923_result.png)

## 写POC
### 采用Pocsuite 框架

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7923_poc1.png)

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7923_poc2.png)

想了很久，除了拿到这个文件信息外，不知道怎么提权，所以最后放弃了。于是开始尝试换漏洞。

### CVE-2017-7925
#### 漏洞概述

Dahua backdoor vulnerability，通过访问未授权的后门url,获取摄像头产品的用户数据库，提取出用户名及哈希密码。攻击者可以利用用户名与哈希密码直接登录该摄像头，从而获得该摄像头的相关权限。

#### 影响版本

- DH-IPC-HDW23A0RN-ZS
- DH-IPC-HDBW23A0RN-ZS 
- DH-IPC-HDBW13A0SN
- DH-IPC-HDW13A0SN
- DH-IPC-HFW13A0SN-W 
- DH-IPC-HDBW13A0SN
- DH-IPC-HDW13A0SN
- DH-IPC-HFW13A0SN-W 
- DHI-HCVR51A04HE-S3
- DHI-HCVR51A08HE-S3	
- DHI-HCVR58A32S-S2

#### CVE-2017-7925 漏洞覆现

使用shodan 搜索（最近zoomeye api接口不能用了，所以用shodan 抓取IP） dork = “dahua ipc”。对IP发起get 请求 uri = /current_config/Account1 或者 uri = /current_config/passwd。摄像头会把存有的用户名、密码和相应用户对应的权限文件返回。

请求返回的文件内容：
![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7925_show.png)

#### 采用Pocsuite 框架

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7925_poc1.png)


## 利用漏洞提权
### 利用原理
请求返回的信息中包括session，username，password(加密的)等信息，将username，加密的密码encrypted_password，还有random三个组合起来，采用MD5加密后作为新的password，在同一session上发起请求，此时将通过认证，可以获取摄像头最高权限。

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7925_exp.png)

### 采用Pocsuite 写attack方法

![](http://niubencoolboy.github.io/img/ipc_poc/cve_2017_7925_poc2.png)

## 自动化测试
### 获取IP
最近实验室网络不知道怎么回事，特别卡（据说是因为十九大），zoomeye api也不能使用了，所以就试试shodan，买了年会员（今天shodan也上不去了），安装shodan的Python库，输入“dahua ipc”好像只有300左右，不过还好，够测试了。
![](http://niubencoolboy.github.io/img/ipc_poc/getip.png)

### 采用Pocsuite 多线程 测试
#### verify 测试

```
pocsuite -r modules/dahua_ipc_backdoor_cve_2017_7925.py -f ipdir/dh-ipc.txt --verify --threads 50 --report dahua_verify_report.html
```
![](http://niubencoolboy.github.io/img/ipc_poc/verify_success.png)

#### attack 测试

```
pocsuite -r modules/dahua_ipc_backdoor_cve_2017_7925.py -f ipdir/dh-ipc.txt --attack --threads 50 --report dahua_attack_report.html
```
![](http://niubencoolboy.github.io/img/ipc_poc/attack_success.png)

## 结果展示
### Verify 展示
![](http://niubencoolboy.github.io/img/ipc_poc/verify_show.png)
详情见：[Report-dahua-verify-report.html](http://niubencoolboy.github.io/img/ipc_poc/dahua_verify_report.html)

### Attack 展示
![](http://niubencoolboy.github.io/img/ipc_poc/attack_show.png)
详情见：[Report-dahua-attack-report.html](http://niubencoolboy.github.io/img/ipc_poc/dahua_attack_report.html)

## 参考相关链接

1. [https://www.zoomeye.org](https://www.zoomeye.org) 
2. [https://www.seebug.org](https://www.seebug.org) 
3. [https://www.shodan.io](https://www.shodan.io) 
4. [https://github.com/knownsec/Pocsuite](https://github.com/knownsec/Pocsuite) 
5. [http://www.exploit-db.com/exploits](http://www.exploit-db.com/exploits) 
6. [http://www.cnvd.org.cn/webinfo/show/4172](http://www.cnvd.org.cn/webinfo/show/4172) 
7. [http://toutiao.secjia.com/dahua-webcam-vulnerability-analysis-protection](http://toutiao.secjia.com/dahua-webcam-vulnerability-analysis-protection) 
8. [http://us.dahuasecurity.com/en/us/Security-Bulletin_030617.php](http://us.dahuasecurity.com/en/us/Security-Bulletin_030617.php) 
9. [http://seclists.org/fulldisclosure/2017/Mar/7](http://seclists.org/fulldisclosure/2017/Mar/7) 
10. [https://ipvm.com/reports/dahua-backdoor?code=bash](https://ipvm.com/reports/dahua-backdoor?code=bash) 
11. [https://www.seebug.org/vuldb/ssvid-92745](https://www.seebug.org/vuldb/ssvid-92745) 







