---
title: PFX文件提取公钥私钥
date: 2017-10-23 16:24:42
tags: [ openssl]
---

```sh
// 原版PFX证书
openssl pkcs12 -in myssl.pfx -nodes -out server.pem
// 提取私钥
openssl rsa -in server.pem -out server.key
// 提取公钥
openssl x509 -in server.pem -out server.crt
```
