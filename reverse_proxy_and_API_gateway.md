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

* Physical machines or cloud nodes with Ubuntu installed.
* Install Nginx Plus. Steps can be found [here](/Install_nginx_plus.md)
* Setup a [basic static file server](/Basic_static_file_server.md)

NOTE: Here I have created 2 basic static file server. You can create as many as per your requirement.

## Setup Reverse Proxy and API Gateway

Follow [this documentation](https://armkeil.blob.core.windows.net/developer/Files/pdf/white-paper/guidelines-for-deploying-nginx-plus-on-aws.pdf) to setup Reverse Proxy and API Gateway(RP/APIGW).

### Steps in brief

NOTE: The below mentioned steps are used to setup Reverse Proxy and API Gateway(RP/APIGW).

* Switch to root user:

```console
sudo su -
```

* Set the below parameters:

```console
sysctl net.ipv4.ip_local_port_range=”32768 60999”
sysctl net.ipv4.tcp_max_syn_backlog=60999
sysctl net.core.somaxconn=60999
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
 # error_log off will not turn off error logs. Error logs will redirect to /usr/share/nginx/off
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

# HTTPS reverse proxy and API Gateway
server {
 listen 443 ssl reuseport backlog=60999;
 root /usr/share/nginx/html;
 index index.html index.htm;
 server_name $hostname;
 ssl on;
 ssl_certificate /etc/nginx/ssl/ecdsa.crt;
 ssl_certificate_key /etc/nginx/ssl/ecdsa.key;
 ssl_ciphers ECDHE-ECDSA-AES256-GCM-SHA384;
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
Here $hostname will be replaced with the DNS of the machine and private_ip is the private IP of the file servers.

Follow the instructions to [create](/key_and_certification.md) ECDSA key and certificate

* Check the configuration for correct syntax run and then start Nginx Server using below commands:

```console
nginx -t -v
systemctl start nginx
```

* To verify the reverse proxy server is running open the URL in your browser and now the content of your html file which is present in your file server will be displayed:

```console
https://<IP>/
```
Here IP is the DNS name of the host

* To verify the API gateway:

Run the following commands on the upstreams to generate the files that will be served:
```console
# Create 1kb file in RP use case directory
dd if=/dev/urandom of=/usr/share/nginx/html/1kb bs=1024 count=1
# Copy files into the APIGW use case directory
mkdir -p /usr/share/nginx/html/api_new
cp /usr/share/nginx/html/1kb /usr/share/nginx/html/api_new
```

* Now open the URL in your browser and now the URL will be replaced:

```console
https://<IP>/api_old/1kb
```
Here IP is the DNS name of the host

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
