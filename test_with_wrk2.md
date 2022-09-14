---
processors : ["Neoverse-N1"]
software : ["linux"]
title: "Run a test with wrk2"
type: docs
weight: 4
hide_summary: true
description: >
    Learn how to run a test with wrk2.
---

## Pre-requisites

* An Amazon Web Services(AWS) account.
* Create a [reverse proxy/API Gateway](/reverse_proxy_and_API_gateway.md)
* Create a [file server](/Basic_static_file_server.md)

## Build Nginx from source

Using your AWS Account, launch an ARM 64-bit instance running with type m6g.2xlarge and Ubuntu 18.04 AMI.

Then follow [this documentation](https://armkeil.blob.core.windows.net/developer/Files/pdf/white-paper/guidelines-for-deploying-nginx-plus-on-aws.pdf) to build Nginx from source.

### Steps in brief

NOTE: The below mentioned steps are used to run a test with wrk2.

* Run the following commands on the upstreams to generate the files that will be served:

```console
# Create 1kb file in RP use case directory
dd if=/dev/urandom of=/usr/share/nginx/html/1kb bs=1024 count=1
#Create 5kb file in RP use case directory
dd if=/dev/urandom of=/usr/share/nginx/html/5kb bs=1024 count=5
#Create 10kb file in RP use case directory
dd if=/dev/urandom of=/usr/share/nginx/html/10kb bs=1024 count=10

# Copy files into the APIGW use case directory
mkdir -p /usr/share/nginx/html/api_new
cp /usr/share/nginx/html/1kb /usr/share/nginx/html/api_new
cp /usr/share/nginx/html/5kb /usr/share/nginx/html/api_new
cp /usr/share/nginx/html/10kb /usr/share/nginx/html/api_new
```

* Build the wrk2 HTTP benchmark:

```console
sudo apt update
sudo apt install -y make gcc zlib1g-dev libssl-dev
git clone https://github.com/giltene/wrk2
cd wrk2
make
```

* Reverse-Proxy Test Commands:

```console
./wrk --rate 10000000000 -t 64 -c 640 -d 60s https://<rp_apigw_ip_dns>/1kb
```
Here <rp_apigw_ip_dns> is the private IP or DNS name of the Reverse Proxy/API Gateway instances that is to be tested

* API Gateway Commands:
For the APIGW test, we use the same command as the RP test case, but with a different
URL (one that will be rewritten). Below is an example command for testing with a 1kb file:

```console
./wrk --rate 10000000000 -t 64 -c 640 -d 60s https://<rp_apigw_ip_dns>/api_old/1kb
```

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)
