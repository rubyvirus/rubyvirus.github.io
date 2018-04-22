---
title: 记录一次stf部署外网，通过nginx访问实况
tag: open-stf
date: 2018-04-22
---

# 项目复杂环境与需求

项目中复杂的环境与需求， 往  往需要我们做出一些调整。如下面这种场景：某公司部署了一个 open-stf，在  总部  内网使用非常舒服。当子公司或分部人员需要使用 open-stf 时候，就需要挂 VPN 才能访问，这种  情况有两个方案可提供：

1.  使用 open-stf 提供的 provider 方式，[参见](https://testerhome.com/topics/11055)provider 部分。使用过程你会发现网络环境差 ，效果非常不理想
2.  借用官网分部式部署原理， 通过 nginx 映射，将内网应用  投放到外网。（也是我们今天要说明的内容）

## 准备内容如下

1.  申请外网 ip:port
2.  将内网映射到外网
3.  修改 open-stf 内容
4.  配置 Nginx

## 主要说明第三点

> open-stf 还是使用内网地址启动

因为外网端口只有一个，所有要将  浏览器向 open-stf 发出通讯地址修改为外网 ip:port，有两个地方需要修改：

1.  $stf/lib/cli/provider/index.js 文件

```
.option('screen-ws-url-pattern', {
      describe: 'The URL pattern to use for the screen WebSocket.'
    , type: 'string'
    , default: 'ws://ip:port/d/${publicPort}/'
    })
```

2.  $stf/lib/cli/device/index.js 文件

```
.option('screen-ws-url-pattern', {
      describe: 'The URL pattern to use for the screen WebSocket.'
    , type: 'string'
    , default: 'ws://ip:port/d/${publicPort}/'
    })
```

3.  $stf/lib/cli/app/index.js 文件

```
module.exports.handler = function(argv) {
  return require('../../units/app')({
    port: argv.port
  , secret: argv.secret
  , ssid: argv.ssid
  , authUrl: argv.authUrl
  , websocketUrl: 'http://ip:port/'
  , userProfileUrl: argv.userProfileUrl
  })
}
```

## 主要说明第四点

```
daemon off;
worker_processes 4;

events {
  worker_connections 1024;
}

http {
  keepalive_timeout   65;
  types_hash_max_size 2048;

  default_type        application/octet-stream;

  upstream stf_app {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3100 max_fails=0;
  }

  upstream stf_auth {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3200 max_fails=0;
  }

  upstream stf_storage_apk {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3300 max_fails=0;
  }

  upstream stf_storage_image {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3400 max_fails=0;
  }

  upstream stf_storage {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3500 max_fails=0;
  }

  upstream stf_websocket {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3600 max_fails=0;
  }

  upstream stf_api {
    # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
    server 172.17.8.10{1|2|3}:3700 max_fails=0;
  }

  types {
    application/javascript  js;
    image/gif               gif;
    image/jpeg              jpg;
    text/css                css;
    text/html               html;
  }

  map $http_upgrade $connection_upgrade {
    default  upgrade;
    ''       close;
  }

  server {
    listen       80 default_server;
    listen       [::]:80 default_server;
    server_name  stf.mydomain.org;
    root         /dev/null;

    resolver 8.8.4.4 8.8.8.8 valid=300s;
    resolver_timeout 10s;

    location ~ "^/d/(?<port>[0-9]{4})/$" {
      # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
      proxy_pass http://172.17.8.10{1|2|3}:$port/;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_set_header X-Real-IP $remote_addr;
    }

    location ~ "^/d/(?<port>[0-9]{4})/$" {
      # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
      proxy_pass http://172.17.8.10{1|2|3}:$port/;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_set_header X-Real-IP $remote_addr;
    }

    location ~ "^/d/(?<port>[0-9]{4})/$" {
      # PLEASE UPDATE IP ADDRESS WITH APPROPRIATE ONE
      proxy_pass http://172.17.8.10{1|2|3}:$port/;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_set_header X-Forwarded-For $remote_addr;
      proxy_set_header X-Real-IP $remote_addr;
    }

    location /s/image/ {
      proxy_pass http://stf_storage_image;
    }

    location /s/apk/ {
      proxy_pass http://stf_storage_apk;
    }

    location /s/ {
      client_max_body_size 1024m;
      client_body_buffer_size 128k;
      proxy_pass http://stf_storage;
    }

    location /socket.io/ {
      proxy_pass http://stf_websocket;
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Real-IP $http_x_real_ip;
    }

    location /auth/ {
      proxy_pass http://stf_auth/auth/;
    }

    location /api/ {
      proxy_pass http://stf_api/api/;
    }

    location / {
      proxy_pass http://stf_app;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Real-IP $http_x_real_ip;
    }
  }
}
```
