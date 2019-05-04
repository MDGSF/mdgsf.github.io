---
layout: post
title: "[openssl] 生成SSL证书"
date: 2017-03-03
author: mdgsf
comments: true
categories: openssl
tags: [Openssl,Crt]
description:
published: true #default true
---

### openssl中有如下后缀名的文件

.key格式：私有的密钥

.csr格式：证书签名请求（证书请求文件），含有公钥信息，certificate signing request的缩写

.crt格式：证书文件，certificate的缩写

.crl格式：证书吊销列表，Certificate Revocation List的缩写

.pem格式：用于导出，导入证书时候的证书的格式，有证书开头，结尾的格式

### CA根证书的生成步骤

生成CA私钥（.key）-->生成CA证书请求（.csr）-->自签名得到根证书（.crt）（CA给自已颁发的证书）。

```
# Generate CA private key
openssl genrsa -out ca.key 2048
# Generate CSR
openssl req -new -key ca.key -out ca.csr
# Generate Self Signed certificate（CA 根证书）
openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
```

在实际的软件开发工作中，往往服务器就采用这种自签名的方式，因为毕竟找第三方签名机构是要给钱的，也是需要花时间的。

### 用户证书的生成步骤

生成私钥（.key）-->生成证书请求（.csr）-->用CA根证书签名得到证书（.crt）

```
# private key
$openssl genrsa -des3 -out server.key 1024
# generate csr
$openssl req -new -key server.key -out server.csr
# generate certificate
$openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key
```

### 注意点

在执行$openssl ca -in server.csr -out server.crt -cert ca.crt -keyfile ca.key时可能会出错：

Using configuration from /usr/share/ssl/openssl.cfg I am unable to access the ./demoCA/newcerts directory ./demoCA/newcerts: No such file or directory

解决方法：

```
1)mkdir -p ./demoCA/newcerts
2)touch demoCA/index.txt
3)touch demoCA/serial
4)echo 01 > demoCA/serial
```

### 命令行：数字签名

```
To generate RSA private key, 2048 bit.
openssl genrsa -out privatekey.pem

To generate RSA public key
openssl rsa -in privatekey.pem -pubout -out publickey.pem

chage private key to pkcs8 format:
openssl pkcs8 -topk8 -inform PEM -in privatekey.pem -outform PEM -nocrypt


To create a hex-encoded message digest of a file:
openssl dgst -md5 -hex file.txt

To sign a file using SHA-256 with binary file output:
openssl dgst -sha256 -sign privatekey.pem -out signature.sign file.txt

To verify a signature:
openssl dgst -sha256 -verify publickey.pem \
 -signature signature.sign \
 file.txt
```
