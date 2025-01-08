

## nginx 配置

```lua
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    keepalive_timeout  65;

    gzip  on;
    gzip_min_length 1k;
    gzip_comp_level 5;
    gzip_vary on;
    gzip_static on;
    client_max_body_size 2M;

    server {
      listen 80;
      root /usr/share/nginx/html;
      location /webapp/ {
          rewrite ^/webapp(/.*)$ $1 last;
          index index.html index.htm
          try_files $uri $uri/index.html $uri/ =404;
      }
    }
}

```

## 镜像打包

```docker
FROM nginx:alpine
LABEL group="liangmu"
COPY .deploy/nginx.conf /etc/nginx/nginx.conf
COPY build/web /usr/share/nginx/html
```