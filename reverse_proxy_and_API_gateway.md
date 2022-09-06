---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Setup Reverse Proxy and API Gateway"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to setup Reverse Proxy and API Gateway.
---

## Pre-requisites

* An Amazon Web Services(AWS) account.
* Install Nginx Plus. Steps can be found [here](/Install_nginx_plus.md)
* Setup two [basic static file server](/Basic_static_file_server.md)

## Build Nginx from source

Using your AWS Account, launch an ARM 64-bit instance running with type M6g, size: 2XLarge and Ubuntu 18.04 AMI.

Then follow [this documentation](https://armkeil.blob.core.windows.net/developer/Files/pdf/white-paper/guidelines-for-deploying-nginx-plus-on-aws.pdf) to setup Reverse Proxy and API Gateway.

### Steps in brief

NOTE: The below mentioned steps are used to setup Reverse Proxy and API Gateway.

* Set the below parameters:

```console
sysctl net.ipv4.ip_local_port_range=”1024 65535”
sysctl net.ipv4.tcp_max_syn_backlog=65535
sysctl net.core.rmem_max=8388607
sysctl net.core.wmem_max=8388607
sysctl net.ipv4.tcp_rmem=”4096 8388607 8388607”
sysctl net.ipv4.tcp_wmem=”4096 8388607 8388607”
sysctl net.core.somaxconn=65535
sysctl net.ipv4.tcp_autocorking=0
```

* Add the following code in /etc/nginx/nginx.conf:

```console
user www-data;
worker_processes auto;
worker_rlimit_nofile 1000000;
pid /run/nginx.pid;
events {
 worker_connections 1024;
 accept_mutex off;
 multi_accept off;
}
http {
 ##
 # Basic Settings
 ##
 sendfile on;
 tcp_nopush on;
 tcp_nodelay on;
 keepalive_timeout 75;
 keepalive_requests 1000000000;
 types_hash_max_size 2048;
 include /etc/nginx/mime.types;
 default_type application/octet-stream;
 
 ##
 # Logging Settings
 ##
 
 access_log off;
 # error_log off will not turn off error logs. Error logs will
redirect to /usr/share/nginx/off
 # The below comes the closest to actually turning off error logs.
 error_log /dev/null crit;
 ##
 # Virtual Host Configs
 ##
 include /etc/nginx/conf.d/*.conf;
 include /etc/nginx/sites-enabled/*;
}
```

* Add the following code into /etc/nginx/conf.d/default.conf:

```console
# Upstreams for https
upstream ssl_file_server_com {
 server <private_ip>:443;
 server <private_ip>:443;
 keepalive 1024;
}
# JWT Authentication configs
auth_jwt “Performance Test API”;
auth_jwt_key_file /etc/nginx/jwk/server_jwt_keys.jwk;
# HTTPS reverse proxy and API Gateway
server {
 listen 443 ssl reuseport backlog=65535;
 root /usr/share/nginx/html;
 index index.html index.htm;
 server_name $hostname;
 ssl on;
 ssl_certificate /etc/nginx/ssl/ecdsa.crt;
 ssl_certificate_key /etc/nginx/ssl/ecdsa.key;
 ssl_ciphers “ECDHE-ECDSA-AES256-GCM-SHA384”;
 # API Gateway Path
 location ~ ^/api_old/.*$ {
 limit_except GET {
 deny all;
 }
 rewrite ^/api_old/(.*)$ /api_new/$1 last;
 }
 location /api_new {
 internal;
 proxy_pass https://ssl_file_server_com;
 proxy_http_version 1.1;
 proxy_set_header Connection “”;
 }
 # Reverse Proxy Path
 location / {
 limit_except GET {
 deny all;
 }
 proxy_pass https://ssl_file_server_com;
 proxy_http_version 1.1;
 proxy_set_header Connection “”;
 }
}
```
Here $hostname will be replaced with the IP of the machine and private_ip is the private IP of the file servers

Follow the instructions to [create](/key_and_certification.md) ECDSA key and certificate

* To verify the reverse proxy and API gateway:

```console
export PATH=/usr/local/nginx:$PATH
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
