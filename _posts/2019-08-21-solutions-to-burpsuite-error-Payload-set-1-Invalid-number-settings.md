---
layout:     post                    # 使用的布局（不需要改）
title:      "solutions to burpsuite intruder error \"Payload set 1: Invalid number settings\"" # 标题 
subtitle:   a burpsuite bug #副标题
date:       2019-08-21              # 时间
author:     fjh1997                 # 作者
header-img: img/burp.jpg         #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - burp
    - bug
---

#  solutions to burpsuite intruder error "Payload set 1: Invalid number settings"
![]({{site.url}}/img/burpprompt.png)
If you see the prompt as "Payload set 1: Invalid number settings "after clicking start attrack, the solution is : first click "hex" and then "decimal" ,and try again to start attrack. This is a software bug that needs you to manually refresh.
![]({{site.url}}/img/burpbug.png)