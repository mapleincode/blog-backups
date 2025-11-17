---
title: 生成 SAN 证书并自签
date: 2018-02-26 14:16:30
tags: [ san, openssl ]
---

> 生成 SAN 证书。因为群晖的证书生成有个 BUG， 创造多域名证书居然不能输入`*`。只好网上找资料手动创建多域名证书 SAN certificater(SubjectAltName Certificater)。

## 工作文件夹

```shell
├── ca.srl
├── ca
│   ├── ca.cer # ca 证书
│   └── ca.pem # ca 私钥
├── server
│   ├── server.cer # server 证书
│   ├── ca.csr # ca csr 文件
│   ├── server.pem # server 私钥 
│   └── server.csr # server csr 文件
└── san.conf # san.conf 配置文件
```



生成证书分为以下几个步骤

1. 生成 CA 证书
   1. 生成 CA 私钥
   2. 生成 CA 证书
2. 生成域名证书
   1. 创建 san.conf
   2. 生成域名 CSR 文件 & 私钥
   3. 生成域名证书并使用 CA 证书进行签名



## 1. 生成 CA 证书



### 1.1 生成 CA 私钥

```
openssl genrsa -aes256 -passout pass:111111 -out ca/ca.pem 2048
```

CA 私钥是不包含日期的，所以可以重复使用。

需要设置 pass 密码，使用 CA 私钥和证书时都需要输入密码验证。



### 1.2 生成 CA 证书

```shell
penssl req -new -x509 -days 36500 -key ca/ca.pem -out ca/ca.cer -subj "/C=CN/ST=Zhejiang/L=Hangzhou/O=maple/OU=maple/emailAddress=maple@mail.com"
```

-days 是有效时间。建议设置稍微长一点，比如 20 年或者更多。

一旦 CA 证书过期，用它签名的证书都作废。



## 2. 生成 Server 证书



因为要创建多域名的证书，不能直接用参数来生成证书，而需要一个证书申请文件(CSR)。



### 2.1 创建 san.conf

> san.conf

```sh
[ req ]
default_bits = 2048
default_md = sha256
default_keyfile = server/server.pem
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
IP.1 = 192.168.1.1
```



### 2.2 生成  SAN-Server CSR & Server 私钥

```sh
openssl req -new -out server/server.csr -nodes -config san.conf -batch
```



这边有一个参数`-nodes`，如果不添加该参数，在执行命令之后会让你输入四位数以上的密码。之后，只要使用该证书(比如重启 Nginx 服务)都会需要让你输入密码。



私钥的文件位置取决于 san.config 的 `default_keyfile` 配置项。



>   CSR 是证书请求文件，如果向第三方机构申请证书，那只要用 CSR 文件申请即可，里面包含你的域名的一些信息。

>在网上找到了一个网站，可以自动生成你需要的 CSR 文件: [https://certificatetools.com/]()



### 2.3 用 CA 证书签名并生成 Server 证书

```sh
openssl x509 -req -sha256 -in server/server.csr -CA ca/ca.cer -CAkey ca/ca.pem -CAcreateserial -out server/server.cer -extensions req_ext -extfile san.conf -days 7300
```

`-days` 证书有效期。





## 3. 怎么让浏览器可以信任你的自签证书

证书的信任是可以被继承的。

例如在我们使用案例中，如果我们 CA 证书被系统信任，那使用 CA 证书签名出来的任何证书都不需要被专门信任，它们都是被信任的。



我们的操作系统在更新时，系统服务商会提供一些公共的并且可以被信任的根证书，依靠这些根证书，系统会对证书供应商进行信任，然后所有网站都会在证书供应商那边购买证书，或者免费创建证书（例如 Let's Encrypt，即便他的证书只有三个月，也依然是被主流操作系统公开信任）。



而如果系统不更新，或者系统服务商已经停止维护项目，那可能造成根证书过期，以至于被根证书信任的证书服务商以及域名证书统统无法被识别。这也是老系统无法通过一些比较新的证书的证书校验的原因。

