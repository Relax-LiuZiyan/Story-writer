---
title: proxychains4& socks 
tags: socks ,proxychains4
renderNumberedHeading: true
grammar_cjkRuby: true
---

# proxychains4安装与配置
1. 安装proxychains4，`sudo apt-get install proxychains4`
2. 配置proxychains4.conf，`sudo vim /etc/proxychains4.conf`，注释掉最后一行，并在最后一行加上本地的端口`socks5 127.0.0.1 1080`。
# socks安装与配置

``` bash
sudo apt-get install libsodium-dev
sudo apt-get install python3-pip
pip3 install https://github.com/shadowsocks/shadowsocks/archive/master.zip -U
```

