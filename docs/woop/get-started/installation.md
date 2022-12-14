---
id: installation
title: Installation
description: "How to install WoopChain."
keywords:
  - docs
  - woopchain
  - woop
  - install
  - installation
---

## Building from source

Prior to using `go install` make sure that you have Go `>=1.18` installed and properly configured.

```shell
git clone https://github.com/Woop-Chain/woopchain.git
cd woop/
go build -o woop main.go
sudo mv woop /usr/local/bin
```

---

## Using `go install`

Prior to using `go install` make sure that you have Go `>=1.17` installed and properly configured.

`go install github.com/Woop-Chain/woopchain`

The binary will be available in your `GOBIN` environment variable, and will include the latest changes from the mainline `main` branch.
