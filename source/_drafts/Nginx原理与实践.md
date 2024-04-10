---
title: Nginx原理与实践
cover_picture: images/cover/nginx.jpg
author: Veni
categories: notes
tags:
  - tag1
date: 2023-12-14 18:37:18
---

Remember to write a excerpt.<!--more-->

# Nginx Crush Course

## Nginx的工作位置

Nginx是一个服务器，工作于互联网和服务机之间。

![image-20231224230243850](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312242302045.webp)



Nginx完整配置示例：

```
worker_processes auto;
# 配置事件处理器
events {
    worker_connections 1024;
}
http{
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
    root /vshop/dist;
        server {
            listen 80;
            server_name 8.134.138.182;
            location / {
                try_files $uri $uri/ /index.html;
            }
            # 配置反向代理将 /api 开头的请求发送到后端
            location /api {
            proxy_pass http://127.0.0.1:8888;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
        }
    }
}

```

