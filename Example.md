---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Example to wget a file from server and curl to retrieve a static webpage"
type: docs
weight: 5
hide_summary: true
description: >
    Learn how to wget a file from server and curl to retrieve a static webpage.
---

## Pre-requisites

* Physical machines or cloud nodes with Ubuntu installed.
* Setup two [basic static file server(Upstreams)](/Basic_static_file_server.md)
* Setup a [Reverse Proxy and API gateway(RP/APIGW)](/reverse_proxy_and_API_gateway.md)

## To wget a file from server.

### Steps in brief

* Create a file in the upstreams in /usr/share/nginx/html folder:

```console
cd /usr/share/nginx/html
cat > /usr/share/nginx/html/file.txt
Hi this a text file //Write the content in the file
```

* Now to wget run the following command in reverse Proxy and API gateway instance:

```console
wget https://<ip_dns>/<file_name> --no-check-certificate
```

## To curl to retrieve a static webpage.

### Steps in brief

* In my upstreams there is already a file name index.html so run the following command in reverse Proxy and API gateway instance:

```console
curl -k https://<ip_dns>/index.html
```

NOTE: If you want you can create a new file in /usr/share/nginx/html folder in upstream instances.

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
