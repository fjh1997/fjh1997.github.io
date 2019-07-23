---
layout:     post                    # 使用的布局（不需要改）
title:      How to fix your v2ray not working when using cloudflare cdn and tls  # 标题 
subtitle:   small tip about ssl problem #副标题
date:       2019-07-23              # 时间
author:     fjh1997                 # 作者
header-img: img/ssl.jpg         #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - cdn
    - ssl
---

# How to fix your v2ray not working when using cloudflare cdn and tls
If any error outputs like this:

```
 2019/07/23 19:38:30 [Warning] [335345444] v2ray.com/core/app/proxyman/outbound:
  failed to process outbound traffic > v2ray.com/core/proxy/vmess/outbound: failed 
  to find an available destination > v2ray.com/core/common/retry: [v2ray.com/core/
  transport/internet/websocket: failed to dial WebSocket > v2ray.com/core/transport/
  internet/websocket: failed to dial to (wss://[yourdomain]):  > read tcp
   [IP Address]->[IP Address]: i/o timeout v2ray.com/core/transport/internet/websocket:
    failed to dial WebSocket > v2ray.com/core/transport/internet/websocket: failed to 
    dial to (wss://[yourdomain]):  > read tcp [IP Address]:9826-
   >[IP Address]:443: i/o timeout v2ray.com/core/transport/internet/websocket: failed 
   to dial WebSocket > v2ray.com/core/transport/internet/websocket: failed to dial to 
   (wss://[yourdomain]):  > read tcp [IP Address]->[IP Address]: i/o timeout v2ray.com
   /core/transport/internet/websocket: failed to dial WebSocket > v2ray.com/core/transport
   /internet/websocket: failed to dial to (wss://[yourdomain]):  > read tcp [IP Address]
   :9929->[IP Address]:443: i/o timeout v2ray.com/core/transport/internet/websocket: fai
   led to dial WebSocket > v2ray.com/core/transport/internet/websocket: failed to dial 
   to (wss://[yourdomain]):  > read tcp [IP Address]:9949->[IP Address]:443: i/o timeout] 
   > v2ray.com/core/common/retry: all retry attempts failed
```

that's easy,just make your ssl option in "crypto" configure of cloudflare is "full" , not "flexible" or any else.
![]({{site.url}}/img/ssl.jpg)