---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Create ECDSA key and certificate"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to create ECDSA key and certificate.
---

## Pre-requisites

* Physical machines or cloud nodes with Ubuntu installed.
* Install Nginx from [package](/Install_from_package.md) or [source](/Build_from_source.md).

## Create ECDSA key and certificate

Follow [this documentation](https://www.arm.com/-/media/global/solutions/infrastructure/NGINX_A1%20whitepaper.pdf) create ECDSA key and certificate.

### Steps in brief

NOTE: The below mentioned steps are used to create ECDSA key and certificate.

* Install Openssl:

```console
apt-get install openssl -y
```

* Run the following commands to create the keys and certificate and then copy them to the location specified in the Nginx configuration files:

```console
mkdir /etc/nginx/ssl/
openssl ecparam -out ecdsa.key -name prime256v1 -genkey
openssl req -new -sha256 -key ecdsa.key -out server.csr
openssl x509 -req -sha256 -days 365 -in server.csr -signkey ecdsa.key -out ecdsa.crt
cp ecdsa.key /etc/nginx/ssl/ecdsa.key 
cp ecdsa.crt /etc/nginx/ssl/ecdsa.crt
```
On the option named COMMON_NAME, you need to enter the IP address or hostname.

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
