---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Install Nginx from package"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to install Nginx from package.
---

## Pre-requisites

* An Amazon Web Services(AWS) account.

## Install Nginx from package

Using your AWS Account, launch an ARM 64-bit instance running Ubuntu.

Then follow [this documentation](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-open-source/#installing-a-prebuilt-ubuntu-package-from-an-ubuntu-repository) to install Nginx from package.

### Steps in brief

NOTE: The below mentioned steps are used to install Nginx from package.

* Update the Ubuntu repository information:

```console
apt-get update
```

* Install the package:

```console
apt-get install nginx
```

* Verify the installation:

```console
nginx -v
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
