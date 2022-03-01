---
title: dpkg 安装问题
tags: dpkg
renderNumberedHeading: true
grammar_cjkRuby: true
---

# dpkg选择安装目录

选择安装.deb软件到其他目录
sudo dpkg -i --instdir=/opt/apache apache2

然后可以建立一个软链接
ln -s /opt/gsopcast/usr/local/bin/gsopcast  /usr/local/bin
