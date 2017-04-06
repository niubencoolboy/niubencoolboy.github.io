---
layout: post
title: "linux 下安装 dsniff"
date: 2017-04-06 21:23:00
author: "John Niu"
header-img: "img/post-bg-study.jpg"
tags:
    - linux 学习
    - 攻击工具     
---


## 在ubuntu14.04 下安装 dsniff 2.3

### 1. 安装Berkeley DB


   现在最新版是[Berkeley DB 6.2.23](http://download.oracle.com/berkeley-db/db-6.2.23.tar.gz)
   ```
   # wget http://download.oracle.com/berkeley-db/db-6.2.23.tar.gz
   # tar zxvf db-6.2.23.tar.gz
   # cd db-6.2.23/
   # cd build_unix/
   # ../dist/configure --enable-compat185
   # make
   # sudo make install
    
   ```
    
### 2. 安装libnet 


   网址[libnet](http://pkgs.fedoraproject.org/repo/pkgs/libnet/)
    
    ```
    # tar zxvf libnet-1.1.6.tar.gz
    # cd libnet-1.1.6/
    # ./configure
    # make
    # sudo make install
    ```
    
### 3. 安装libnits

   网址[libnids 1.24](https://sourceforge.net/projects/libnids/files/libnids/)
    
    ```
    # tar zxvf libnids-1.24.tar.gz
    # cd libnids-1.24/
    # ./configure
    # make
    # sudo make install
    
    ```
    
### 4. 安装其他软件


   1. 还需要安装openssl libpcap 由于这两个我都已经安装了，所以就不在介绍，方法与1. 2. 3. 类似。
   2. 安装gcc g++ flex bison
   这些可以使用apt-get 进行安装。 
    
    ```
    sudo apt-get install [softwarepacks] 
    eg:
    sudo apt-get install flex
    ```
   3. 其他的依赖数据包可以
    
    ```
    sudo apt-get -f install
    ```
    
### 5.安装dsniff


   依赖的软件包安装完毕乎，终于可以安装dsniff了。步骤与1. 2. 3. 一样。先下载[dnsiff](https://www.monkey.org/~dugsong/dsniff/dsniff-2.3.tar.gz)
    
    ```
    # wget https://www.monkey.org/~dugsong/dsniff/dsniff-2.3.tar.gz
    # tar zxvf dsniff-2.3.tar.gz
    # cd dsniff2.3
    # vim arp.c
    
    加入代码 #include "memory.h"
    
    # ./configure --enable-compat185 --with-db=/usr/local/BerkeleyDB.6.2
    
    # make
    # sudo make install
    
    ```
    
### 6. 遇到的错位


   1. 在第五步make的时候，发生如下错误：
    
    ```
    ./arpspoof.c: In function ‘main’:
    ./arpspoof.c:181:12: warning: assignment makes pointer from integer without a cast [enabled by default]
    if ((llif = libnet_open_link_interface(intf, ebuf)) == 0)
              ^
    make: *** [arpspoof.o] 错误 1

    ```
    
       问题原因：
       安装[libnet 1.0](http://pkgs.fedoraproject.org/repo/pkgs/libnet/libnet-1.0.2a.tar.gz/ddf53f0f484184390e8c2a1bd0853667/libnet-1.0.2a.tar.gz)
       安装步骤和以前一样。
       把1.1.6 卸载 sudo make uninstall 
    
   2. 在安装libnet 1.0 时候 make出现如下错误：
    
    ```
    ./install-sh doc/libnet.3
    install:	no destination specified
    make: *** [install] 错误 1
    ```
    ***
      解决办法
      edit “Makefile” file and fix MAN_PREFIX variable
    ***
    ```
    # vim Makefile
    
    添加
    # MAN_PREFIX  = /usr/share/doc/ 
    ```
    
### 7.总结

   最后，还有没有安装成功，make 的时候arpspoof.c error 最后没办法，把之前安装的全部卸载。
   直接 
    ```
    # sudo apt-get install dsniff
    ```
   系统自动安装了libnet1 libnids1.21
   安装好后，dsniff -v 居然还是2.4 版本的。
   我的心都快要碎了。这个事让我明白，以后能apt-get 安装的，千万不要作死源码安装。
