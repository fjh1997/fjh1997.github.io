---
layout:     post                    # 使用的布局（不需要改）
title:      "pwntools:to remove gdb and install pwndbg instead" # 标题 
subtitle:   completely remove gdb! #副标题
date:       2019-08-26              # 时间
author:     fjh1997                 # 作者
header-img: img/windows-10.jpg      #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - pwngdb
    - pwntools
---

#  pwntools:to remove gdb and install pwndbg instead"


It is well known that pwndbg is an enhanced version of gdb (you can print the stack directly, etc.). Using gdb.attach() to call pwndbg in pwntool,which enables you to implement pwning while debugging.But if you have already installed general gdb before installing pwndbg. Then you couldn't complete the installation because the python module of gdb and pwndbg conflict,therefore gdb.attach () 
is to call gdb rather than pwndbg, so we have to remove gdb and then install pwndbg, this remove method is a bit troublesome (environment is kali, could also apply to ubuntu or debian)
```bash
sudo apt-get remove --purge gdb
sudo rm -rf /usr/share/gdb

```
install pwndbg after finishing the removal
```bash
git clone https://github.com/pwndbg/pwndbg
cd pwndbg
./setup.sh

``` 
Then enjoy it!
![]({{site.url}}/img/pwndbg.png)