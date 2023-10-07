---
title: 生成 SAN 证书并自签
date: 2018-02-26 14:16:30
tags: [ san, openssl ]
---

生成 SAN 证书。因为群晖的证书生成有个 BUG， 创造多域名证书居然不能输入`*`。只好网上找资料手动创建多域名证书 SAN certificater(SubjectAltName Certificater)。

## 工作文件夹

```shell
├── ca.srl
├── certs
│   ├── ca.cer # ca 证书
│   └── server.cer # server 证书
├── private
│   ├── ca.csr # ca csr 文件
│   ├── ca.pem # ca 私钥
│   ├── server.pem # server 私钥 
│   └── server.csr # server csr 文件
└── san.conf # san.conf 配置文件
```

## 生成 CA 证书

### 生成 CA 私钥

```
openssl genrsa -aes256 -passout pass:111111 -out private/ca.pem 2048
```

### 生成 CA 证书

```shell
penssl req -new -x509 -days 36500 -key private/ca.pem -out certs/ca.cer -subj "/C=CN/ST=Zhejiang/L=Hangzhou/O=maple/OU=maple/emailAddress=maple@mail.com"
```

## 生成 Server 证书

因为要创建多域名的证书，不能直接用参数来生成证书，而需要一个证书申请文件(CSR)。

### 生成 CSR 证书请求文件

> server.csr

```sh
[ req ]
default_bits = 2048
default_md = sha256
default_keyfile = private/server.pem
distinguished_name = req_distinguished_name
req_extensions = req_ext

[ req_distinguished_name ]
commonName_default = www.domain.com
commonName_max = 64
organizationName_default = maple
organizationalUnitName_default = maple
localityName_default = Hangzhou
stateOrProvinceName_default = Zhejiang
countryName_default = CN
emailAddress = llkj13579@126.com
emailAddress_default = llkj13579@126.com

[ req_ext ]
subjectAltName = @alt_names

[alt_names]
DNS.1 = domain.com
DNS.2 = *.domain.com
DNS.3 = domain2.moe
DNS.4 = *.domain2.moe
```

### 生成  SAN-Server CSR & Server 私钥

```sh
openssl req -new -out private/server.csr -nodes -config san.conf -batch
```

这边有一个参数`-nodes`，如果不添加该参数，在执行命令之后会让你输入四位数以上的密码。之后，只要使用该证书(比如重启 Nginx 服务)都会需要让你输入密码。

>   CSR 是证书请求文件，如果向第三方机构申请证书，那只要用 CSR 文件申请即可，里面包含你的域名的一些信息。

>在网上找到了一个网站，可以自动生成你需要的 CSR 文件: [https://certificatetools.com/]()

### 用 CA 证书签名并生成 Server 证书

```sh
openssl x509 -req -sha256 -in private/server.csr -CA certs/ca.cer -CAkey private/ca.pem -CAcreateserial -out certs/server.cer -extensions req_ext -extfile san.conf -days 7300
```

`-days` 证书有效期。

## 最后

辣鸡 `Typora`丢数据，之前写完保存失败，却没提示。关闭 `Typora`之后连数据残骸都找不到，所以第二次写了，但是之前测试的语句早已找不到了，不过也算补上了。

