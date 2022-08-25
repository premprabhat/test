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

## Build Nginx from source

Using your AWS Account, launch an ARM 64-bit instance running Ubuntu.

Then follow [this documentation](http://nginx.org/en/docs/configure.html) to build Nginx from source.

### Steps in brief

NOTE: The below mentioned steps are used to build install Nginx from source.

* Download Clair v4.4.4:

```console
wget https://github.com/quay/clair/releases/download/v4.4.4/clair-v4.4.4.tar.gz
tar -xvf clair-v4.4.4.tar.gz
```

* We will setup a postgres database for Clair to store all the vulnerabilities specific to containers. docker-compose.yaml already has a target "clair-database" to setup a postgres database for Clair. For the combo mode, since postgres service will run inside a private container network and Clair service runs on localhost, we are required to expose postgres port 5432 to localhost. To do so, simply add the following to "clair-database" target in docker-compose.yaml file.

```console
ports:
      - "5432:5432"
```

Next, setup the postgres service as below:

```console
sudo docker-compose up -d clair-database
```

* Clair uses a configuration file to configure indexer, matcher and notifier. In the combo mode, we have to configure indexer, matcher and notifier to communicate to postgres service exposed to port 5432 on localhost. We will use the configuration file present at clair/local-dev/clair/config.yaml, and modify the connstring of indexer, matcher and notifier as below:

```console
indexer:
  connstring: host=localhost port=5432 user=clair dbname=indexer sslmode=disable

matcher:
  connstring: host=localhost port=5432 user=clair dbname=matcher sslmode=disable

notifier:
  connstring: host=localhost port=5432 user=clair dbname=notifier sslmode=disable
```

* Next, generate the clair binary with go, as below:

```console
sudo go build ./cmd/clair
```

This will generate a clair binary in the root of the repo.

* Now that the postgres service is running and clair's configuration is ready, run Clair in the combo mode as below:

```console
./clair -conf "./local-dev/clair/config.yaml" -mode "combo"
```

The running logs on your screen confirms that Clair is running successfully in the combo mode. You can now open a new terminal and submit the manifest to generate the vulnerability report.

[<-- Return to Learning Path](/content/en/cloud/clair/#sections)

[How to Generate the Vulnerability Report -->](/content/en/cloud/clair/vulnerability_report.md)

