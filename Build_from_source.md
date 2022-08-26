---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Build Nginx from source"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to build Nginx from source.
---

## Pre-requisites

* An Amazon Web Services(AWS) account.
* Install wget, unzip, mercurial using command 
```console
apt-get install wget unzip mercurial -y
```

## Build Nginx from source

Using your AWS Account, launch an ARM 64-bit instance running Ubuntu.

Then follow [this documentation](http://nginx.org/en/docs/configure.html) to build Nginx from source.

### Steps in brief

NOTE: The below mentioned steps are used to build Nginx from source.

* Download and extract the source code of PCRE from [here](http://www.pcre.org/). The rest is done by nginx’s ./configure and make:

```console
wget https://github.com/PCRE2Project/pcre2/releases/download/pcre2-10.39/pcre2-10.39.zip
unzip pcre2-10.39.zip
```
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

* Clone the source code:

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
./auto/configure --sbin-path=/usr/local/nginx/nginx --conf-path=/usr/local/nginx/nginx.conf --pid-path=/usr/local/nginx/nginx.pid --with-http_ssl_module --with-pcre=../pcre2-10.39 --with-zlib=../zlib-1.2.11
```
There are many configuration options available in NGINX, you can use it as per your need. To find all the configuration options available in NGINX check [here](http://nginx.org/en/docs/configure.html).

After configuration, nginx is compiled and installed using make:

```console
make
make install
```

* Now set Nignix in your path:

```console
export PATH=/usr/local/nginx:$PATH
```

* To verify if Nignix is insatlled or not check the Nignix version by using the command :

```console
nginx -v
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
