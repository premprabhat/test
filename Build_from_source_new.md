---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Build Nginx from source"
type: docs
weight: 2
hide_summary: true
description: >
    Learn how to build Nginx from source.
---

## Pre-requisites

* Physical machines or cloud nodes with Ubuntu installed.
* Install wget, unzip, mercurial using command
```console
apt-get install wget unzip mercurial -y
```

## Build Nginx from source

Follow [this documentation](http://nginx.org/en/docs/configure.html) to build Nginx from source.

### Steps in brief

NOTE: The below mentioned steps are used to build Nginx from source.

* Download and extract the source code of PCRE from [here](http://www.pcre.org/). The rest is done by nginx’s ./configure and make:

```console
wget https://github.com/PCRE2Project/pcre2/releases/download/pcre2-10.39/pcre2-10.39.zip
unzip pcre2-10.39.zip
```

* The zlib library distribution (version 1.1.3 — 1.2.11) needs to be downloaded from the [zlib](https://zlib.net/fossils/) site and extracted. The rest is done by nginx’s ./configure and make:

```console
wget https://zlib.net/fossils/zlib-1.2.11.tar.gz
tar -xvf zlib-1.2.11.tar.gz
```

* Clone the Nignix source code:

```console
hg clone http://hg.nginx.org/nginx
cd nginx/
```

* Install the following dependecies:

```console
apt-get install gcc libssl-dev make -y
```

* The Build is configured using configure command. It defines various aspects of the system, including the methods nginx is allowed to use for connection processing. At the end it creates a Makefile.

```console
./auto/configure --prefix=/var/www/html --sbin-path=/usr/sbin/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/var/run/nginx.pid --with-http_ssl_module --modules-path=/etc/nginx/modules --with-stream=dynamic --with-pcre=../pcre2-10.39 --with-zlib=../zlib-1.2.11
```
There are many configuration options available in NGINX, you can use it as per your need. To find all the configuration options available in NGINX check [here](http://nginx.org/en/docs/configure.html).

After configuration, nginx is compiled and installed using make:

```console
make
make install
```

* To verify if Nignix is insatlled or not check the Nignix version by using the command :

```console
nginx -v
```

* Now add that systemd service. To enable the service, we're going to have to add a small script for that create a file named /lib/systemd/system/nginx.service and paste the below script:

```console
[Unit]
Description=The NGINX HTTP and reverse proxy server
After=syslog.target network-online.target remote-fs.target nss-lookup.target
Wants=network-online.target

[Service]
Type=forking
PIDFile=/var/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t
ExecStart=/usr/sbin/nginx
ExecReload=/usr/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

* Start your NGINX by using systemd with this command:

```console
systemctl start nginx
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
