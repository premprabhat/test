---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Install Nginx Plus"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to install Nginx Plus.
---

## Pre-requisites

* An Amazon Web Services(AWS) account.
* An NGINX Plus subscription (purchased or trial)
* root privilege
* Your credentials to the [MyF5 Customer Portal](https://account.f5.com/myf5), provided by email from F5, Inc.
* Your NGINX Plus certificate and public key (nginx-repo.crt and nginx-repo.key files), provided by email from F5, Inc.

## Install Nginx Plus

Using your AWS Account, launch an ARM 64-bit instance running Ubuntu.

Then follow [this documentation](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-plus/) to install Nginx Plus.

### Steps in brief

NOTE: The below mentioned steps are used to install Nginx Plus.

* Create the /etc/ssl/nginx directory:

```console
sudo mkdir /etc/ssl/nginx
cd /etc/ssl/nginx
```

* Log in to MyF5 Customer Portal and download your nginx-repo.crt and nginx-repo.key files.

* Copy the files to the /etc/ssl/nginx/ directory:

```console
sudo cp nginx-repo.crt /etc/ssl/nginx/
sudo cp nginx-repo.key /etc/ssl/nginx/
```

* Install the prerequisites packages:

```console
sudo apt-get install apt-transport-https lsb-release ca-certificates wget gnupg2 ubuntu-keyring
```

* Download and add [NGINX signing key](https://nginx.org/keys/nginx_signing.key?_ga=2.189873707.1437862557.1661841695-1317742238.1661167617) and [App Protect security updates signing key](https://cs.nginx.com/static/keys/app-protect-security-updates.key?_ga=2.189873707.1437862557.1661841695-1317742238.1661167617):

```console
wget -qO - https://cs.nginx.com/static/keys/nginx_signing.key | gpg --dearmor | sudo tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null
wget -qO - https://cs.nginx.com/static/keys/app-protect-security-updates.key | gpg --dearmor | sudo tee /usr/share/keyrings/app-protect-security-updates.gpg >/dev/null
```
* Add the NGINX Plus repository:

```console
printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] https://pkgs.nginx.com/plus/ubuntu `lsb_release -cs` nginx-plus\n" | sudo tee /etc/apt/sources.list.d/nginx-plus.list
```

* Download the nginx-plus apt configuration to /etc/apt/apt.conf.d:

```console
sudo wget -P /etc/apt/apt.conf.d https://cs.nginx.com/static/files/90pkgs-nginx
```

* Update the repository information:

```console
sudo apt-get update
```

* Install the nginx-plus package:

```console
sudo apt-get install -y nginx-plus
```

* Check the nginx binary version to ensure that you have NGINX Plus installed correctly:

```console
nginx -v
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
