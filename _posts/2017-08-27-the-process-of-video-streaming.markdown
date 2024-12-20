---
layout: post
title: "摄像头与NVR之间视频流建立协议详情(UPNP RTSP RTP/RTCP)"
date: 2017-08-27 周日
author: "John Niu"
header-img : "img/170827/bg.jpg"
tags:
    - 视频监控
    - 协议
---

#### 1. ARP请求
- ARP广播自己的地址
- 网关响应
![](http://niubencoolboy.github.io/img/170827/arpbroad.png) 
![](http://niubencoolboy.github.io/img/170827/arpbroad02.png) 
	
#### 2. 查询DNS 更新时间
- 第一次查询 time.windows.com
- 第二次查询 www.hik-onlink.com
![](http://niubencoolboy.github.io/img/170827/DNS.png) 
	
#### 3. IGMP 加入组播
- 组播地址：239.255.255.250
![](http://niubencoolboy.github.io/img/170827/IGMP.png) 
	
#### 4. SSDP 广播自己的服务 Request Method: NOTIFY
- LOCATION: http://192.168.13.15:49152/upnpdevicedesc.xml\r\n
![](http://niubencoolboy.github.io/img/170827/ssdp.png) 

#### 5. NVR tcp 尝试连接
由上图可以看出，当摄像头（192.168.13.15）发出SSDP NOTIFY后，NVR（192.168.13.101）发送TCP数据包尝试建立三次握手。

#### 6. IPCamera ARP 广播请求 NVR
仅接着摄像头发送ARP广播，请求NVR（192.168.13.101）的MAC地址。

#### 7. NVR arp 响应
之后NVR（192.168.13.101）发送ARP单播相应。
	
#### 8. NVR 三次握手后 建立HTTP 请求
- TCP 三次握手
![](http://niubencoolboy.github.io/img/170827/tcp.png) 
- 发起UPNP请求 GET /upnpdevicedesc.xml HTTP/1.1\r\n
![](http://niubencoolboy.github.io/img/170827/upnp.png) 
	
#### 9. 摄像头响应 http/xml UPNP
UPNP服务响应采用HTTP/XML的形式描述设备的相信信息。
![](http://niubencoolboy.github.io/img/170827/upnp02.png) 

#### 10. RTSP协议通信
	
> ```
> OPTIONS rtsp://192.168.13.15/ch1/main/av_stream RTSP/1.0
> CSeq: 1
> User-Agent: HIKVISION player NVR V3.4.82
> 
> RTSP/1.0 200 OK
> CSeq: 1
> Public: OPTIONS, DESCRIBE, PLAY, PAUSE, SETUP, TEARDOWN, SET_PARAMETER, GET_PARAMETER
> Date:  Fri, Aug 25 2017 14:00:05 GMT
> 
> DESCRIBE rtsp://192.168.13.15/ch1/main/av_stream RTSP/1.0
> CSeq: 2
> Accept: application/sdp
> User-Agent: HIKVISION player NVR V3.4.82
> 
> RTSP/1.0 401 Unauthorized
> CSeq: 2
> WWW-Authenticate: Digest realm="4419b65e39d0", nonce="b67c83f03b76a7aea24d021ae1c00e30", stale="FALSE"
> WWW-Authenticate: Basic realm="4419b65e39d0"
> Date:  Fri, Aug 25 2017 14:00:05 GMT
> 
> DESCRIBE rtsp://192.168.13.15/ch1/main/av_stream RTSP/1.0
> CSeq: 3
> Accept: application/sdp
> Authorization: Basic YWRtaW46QWRtaW41MjI1MjI= 
> User-Agent: HIKVISION player NVR V3.4.82
> 
> RTSP/1.0 200 OK
> CSeq: 3
> Content-Type: application/sdp
> Content-Length: 487
> 
> v=0
> o=- 1503669605046795 1503669605046795 IN IP4 192.168.13.15
> s=Media Presentation
> e=NONE
> b=AS:5050
> t=0 0
> a=control:*
> m=video 0 RTP/AVP 96
> c=IN IP4 192.168.13.15
> b=AS:5000
> a=recvonly
> a=control:trackID=1
> a=rtpmap:96 H264/90000
> a=fmtp:96 profile-level-id=420029; packetization-mode=1; sprop-parameter-sets=Z00AH5pmAoAt/zUBAQFAAAD6AAAw1AE=,aO48gA==
> a=Media_header:MEDIAINFO=494D4B48010100000400010000000000000000000000000000000000000000000000000000000000;
> a=appversion:1.0
> 
> SETUP rtsp://192.168.13.15/ch1/main/av_stream/trackID=1 RTSP/1.0
> CSeq: 4
> Transport: RTP/AVP/TCP;unicast;interleaved0-1
> Authorization: Basic YWRtaW46QWRtaW41MjI1MjI= 
> User-Agent: HIKVISION player NVR V3.4.82
> 
> RTSP/1.0 200 OK
> CSeq: 4
> Session:       1470203379;timeout=60
> Transport: RTP/AVP/TCP;unicast;interleaved=0-1;ssrc=404fa5f3;mode="play"
> Date:  Fri, Aug 25 2017 14:00:05 GMT
> 
> PLAY rtsp://192.168.13.15/ch1/main/av_stream RTSP/1.0
> CSeq: 5
> Range: npt=0.000-
> Session:       1470203379
> Authorization: Basic YWRtaW46QWRtaW41MjI1MjI='
> User-Agent: HIKVISION player NVR V3.4.82
> RTSP/1.0 200 OK
> CSeq: 5
> Session:       1470203379
> RTP-Info: url=trackID=1;seq=17769
> Date:  Fri, Aug 25 2017 14:00:05 GMT
> ....................................................
> ```

#### 11. RTP/RTCP 视频流的传输
- RTP/RTCP继续在RTSP 554的端口上实现基于TCP的视频流传输。
![](http://niubencoolboy.github.io/img/170827/rtp.png) 
- RTSP RTP/RTCP协议的格式，以及基于tcp udp通信的实现详情见：
[RTP](https://github.com/EasyDarwin/Course/blob/master/%E6%B5%81%E5%AA%92%E4%BD%93%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE(RTSP%20RTP%20SDP)%E8%AF%A6%E8%A7%A3/rtp.md) 
[RTSP](https://github.com/EasyDarwin/Course/blob/master/%E6%B5%81%E5%AA%92%E4%BD%93%E4%BC%A0%E8%BE%93%E6%8E%A7%E5%88%B6%E5%8D%8F%E8%AE%AE(RTSP%20RTP%20SDP)%E8%AF%A6%E8%A7%A3/rtsp.md) 













