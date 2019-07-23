---
layout:     post                    # 使用的布局（不需要改）
title:      How to fix your v2ray not working when using cloudflare cdn and tls  # 标题 
subtitle:   small tip about ssl problem #副标题
date:       2019-07-23              # 时间
author:     fjh1997                 # 作者
header-img: img/windows-10.jpg         #这篇文章标题背景图片
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
and my conf file is like these:
<br>client
```js
{
  "inbounds": [
    {
      "port": 1091,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "sniffing": {
        "enabled": true,
        "destOverride": ["http", "tls"]
      },
      "settings": {
        "auth": "noauth",
        "udp": false
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "vmess",
      "settings": {
        "vnext": [
          {
            "address": "yourdomain",
            "port": 443,
            "users": [
              {
                "id": "youruuid",
                "alterId": 64
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "security": "tls",
          "wsSettings": { 
          "path": "/yourpath"
        }
      }
    }
  ]
}
```

v2ray server:
```js
{
"log": {
                "access": "/var/log/v2ray/access.log",
                "error": "/var/log/v2ray/error.log",
                "loglevel":"info"
        },
  "inbounds": [{
    "port": 19706,
    "protocol": "vmess",
    "settings": {
      "clients": [
        {
          "id": "youruuid",
          "level": 1,
          "alterId": 64
        }
      ]
    },
  "streamSettings":{
    "network":"ws",
    "wsSettings":{
      "path":"/yourpath"
     }
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  },{
    "protocol": "blackhole",
    "settings": {},
    "tag": "blocked"
  }],
  "routing": {
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "blocked"
      }
    ]
  }
}

```
nginx
``` 
server {
  listen  443 ssl;
  ssl_certificate       /etc/v2ray/yourcrt.pem;
  ssl_certificate_key   /etc/v2ray/yourkey.key;
  ssl_protocols         TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers           HIGH:!aNULL:!MD5;
  server_name           yourdomain;
        location /yourpath { # . V2Ray .... path ....
        proxy_redirect off;
        proxy_pass http://127.0.0.1:19706
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $http_host;

        # Show realip in v2ray access.log
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
}


```