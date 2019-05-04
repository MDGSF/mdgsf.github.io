---
layout: post
title: "[openssl] 常用命令"
date: 2018-06-01
author: mdgsf
comments: true
categories: openssl
tags: [Openssl]
description:
published: true #default true
---

openssl 命令主要分为 3 大类：

1. Standard commands
2. Message Digest commands
3. Cipher commands

```
openssl help 查看 openssl 帮助文档
openssl dgst -help 查看消息摘要命令文档
openssl enc -help  查看加密命令文档
man openssl
```

```
root@jian-VirtualBox:~# openssl help
Standard commands
asn1parse         ca                ciphers           cms
crl               crl2pkcs7         dgst              dhparam
dsa               dsaparam          ec                ecparam
enc               engine            errstr            gendsa
genpkey           genrsa            help              list
nseq              ocsp              passwd            pkcs12
pkcs7             pkcs8             pkey              pkeyparam
pkeyutl           prime             rand              rehash
req               rsa               rsautl            s_client
s_server          s_time            sess_id           smime
speed             spkac             srp               storeutl
ts                verify            version           x509

Message Digest commands (see the `dgst' command for more details)
blake2b512        blake2s256        gost              md4
md5               mdc2              rmd160            sha1
sha224            sha256            sha3-224          sha3-256
sha3-384          sha3-512          sha384            sha512
sha512-224        sha512-256        shake128          shake256
sm3

Cipher commands (see the `enc' command for more details)
aes-128-cbc       aes-128-ecb       aes-192-cbc       aes-192-ecb
aes-256-cbc       aes-256-ecb       aria-128-cbc      aria-128-cfb
aria-128-cfb1     aria-128-cfb8     aria-128-ctr      aria-128-ecb
aria-128-ofb      aria-192-cbc      aria-192-cfb      aria-192-cfb1
aria-192-cfb8     aria-192-ctr      aria-192-ecb      aria-192-ofb
aria-256-cbc      aria-256-cfb      aria-256-cfb1     aria-256-cfb8
aria-256-ctr      aria-256-ecb      aria-256-ofb      base64
bf                bf-cbc            bf-cfb            bf-ecb
bf-ofb            camellia-128-cbc  camellia-128-ecb  camellia-192-cbc
camellia-192-ecb  camellia-256-cbc  camellia-256-ecb  cast
cast-cbc          cast5-cbc         cast5-cfb         cast5-ecb
cast5-ofb         des               des-cbc           des-cfb
des-ecb           des-ede           des-ede-cbc       des-ede-cfb
des-ede-ofb       des-ede3          des-ede3-cbc      des-ede3-cfb
des-ede3-ofb      des-ofb           des3              desx
idea              idea-cbc          idea-cfb          idea-ecb
idea-ofb          rc2               rc2-40-cbc        rc2-64-cbc
rc2-cbc           rc2-cfb           rc2-ecb           rc2-ofb
rc4               rc4-40            seed              seed-cbc
seed-cfb          seed-ecb          seed-ofb          sm4-cbc
sm4-cfb           sm4-ctr           sm4-ecb           sm4-ofb
```

### openssl rand 生成随机数

```
opoenssl rand -h  //查看帮助文档
Usage: rand [options] num
openssl rand 10  //生成一个 10 位的随机数
openssl rand 10 -base64  //生成一个 10 位的随机数，以 base64 编码输出
openssl rand 10 -hex  //生成一个 10 位的随机数，以 16 进制输出
openssl rand 10 -base64 -out fileName  //生成一个 10 位的随机数，以 base64 编码输出到文件
```

### openssl enc 加密

```
openssl enc -h  //查看帮助文档
-e 加密
-d 解密
-a 以 base64 编码
-iter 1024 加密迭代 1024 次

openssl enc -aes-256-cbc -salt -in record.txt -out CryptFile -pass "file:keyFileName"  //把 record.txt 加密为 CryptFile，密码在 keyFileName 文件中
openssl enc -d -aes-256-cbc -in CryptFile -out PlainFile -pass "file:keyFileName"  //把 CryptFile 解密为 PlainFile
diff record.txt PlainFile  //用 diff 和 md5sum 查看解密出来的文件和源文件是否一样
md5sum record.txt PlainFile

openssl enc -e -a -aes-256-cbc -in record.txt -out CryptFile -pass pass:1234 -iter 1024
openssl enc -d -a -aes-256-cbc -in CryptFile -out PlainFile -pass pass:1234 -iter 1024
diff record.txt PlainFile  //用 diff 和 md5sum 查看解密出来的文件和源文件是否一样
md5sum record.txt PlainFile
```

### -pass 参数用法

```
man openssl 的最底部有介绍
-pass pass:password  //直接 password 作为密码
-pass env:var  //从环境变量读
-pass file:pathname  //从文件中读取
-pass fd:number  //从文件描述符读取
-pass stdin
```

### openssl genrsa 生成 RSA 私钥

```
openssl genrsa -help //查看帮助文档
-out outfile 输出到文件

openssl genrsa -out key.pem 1024  //生成 1024 位的私钥
openssl genrsa -out key.pem 2048  //生成 2048 位的私钥
openssl genrsa -out key.pem 4096  //生成 4096 位的私钥
```

```
key.pem 文件格式
-----BEGIN RSA PRIVATE KEY-----
-----END RSA PRIVATE KEY-----
```

```
openssl rsa -in key.pem -pubout -out pubkey.pem  //生成公钥
openssl rsa -in key.pem -RSAPublicKey_out -out rsapubkey.pem  //生成 RSAPublicKey 格式的公钥
```

```
pubkey.pem 文件格式
-----BEGIN PUBLIC KEY-----
-----END PUBLIC KEY-----
```

```
rsapubkey.pem 文件格式
-----BEGIN RSA PUBLIC KEY-----
-----END RSA PUBLIC KEY-----
```

用的时候，都是用 key.pem 和 pubkey.pem。

### openssl dgst

```
man dgst  //查看帮助文档
-sign 使用私钥 key.pem 对文件 record.txt 的消息摘要进行签名
-verify 使用公钥 pubkey.pem 对数字签名进行验证

openssl dgst -sha256 -sign key.pem -out signature.sin record.txt  //用私钥 key.pem 对 record.txt 进行签名，生成数字签名 signature.sin
openssl dgst -sha256 -verify pubkey.pem -signature signature.sin record.txt  //用公钥 pubkey.pem 和 signature.sin 对 record.txt 进行验证
```

```
生成 record.txt 文件的消息摘要
root@jian-VirtualBox:~/opensslDemo# openssl dgst -md5 -hex record.txt
MD5(record.txt)= bb6b962338f9d1c0dbcdb6e2b376d9db
root@jian-VirtualBox:~/opensslDemo# openssl dgst -sha256 -hex record.txt
SHA256(record.txt)= 02ba968cbe7576ed5c41a98f508d4d2dce24873a4d1f0045e2567731bf4a5d30
```

### openssl genpkey

### openssl rsa

### openssl 生成根证书 CA，并签发子证书

```
locate openssl.cnf  //找到系统中 openssl 的配置文件
```

```
修改 openssl.cnf 文件
dir = ./demoCA
修改为
dir = /root/CA
```

```
mkdir /root/CA
cd /root/CA
mkdir certs
mkdir newcerts
mkdir private
mkdir crl
touch index.txt
echo 01>serial
openssl rand -out private/.rand 1000
```

```
//生成根证书私钥
openssl genrsa -aes256 -out private/cakey.pem -passout pass:123456

//生成根证书签发申请文件
openssl req -new -key private/cakey.pem -out private/ca.csr -subj "/C=CN/ST=myprovince/L=mycity/O=myorganization/OU=mygroup/CN=localhost" -passin pass:123456

//自签发根证书
openssl x509 -req -days 365 -sha256 -extensions v3_ca -signkey private/cakey.pem -in private/ca.csr -out certs/ca.cer -passin pass:123456

//用根证书签发服务器证书
openssl genrsa -aes256 -out private/sever-key.pem -passout pass:123456
openssl req -new -key private/server-key.pem -out private/server.csr -subj "/C=CN/ST=myprovince/L=mycity/O=myorganization/OU=mygroup/CN=localhost" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_req -CA certs/ca.cer -CAkey private/cakey.pem -CAserial ca.srl -CAcreateserial -in private/server.csr -out certs/server.cer -passin pass:123456

//用根证书签发客户端证书
openssl genrsa -aes256 -out private/client-key.pem -passout pass:123456
openssl req -new -key private/client-key.pem -out private/client.csr -subj "/C=CN/ST=myprovince/L=mycity/O=myorganization/OU=mygroup/CN=localhost" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_req -CA certs/ca.cer -CAkey private/cakey.pem -CAserial ca.srl -in private/client.csr -out certs/client.cer -passin pass:123456

//把加密的私钥转换为不加密的私钥
openssl rsa -in private/server-key.pem -out private/server-key-out.pem -passin pass:123456
openssl rsa -in private/client-key.pem -out private/client-key-out.pem -passin pass:123456
```

最终使用的文件：ca.cer，client.cer，client-key-out.pem，server.cer，server-key-out.pem

查看证书内容

```
openssl x509 -in root-ca.cert.pem -noout -text
```

```
# root-ca.cert.pem
openssl genrsa -aes256 -out private/cakey.pem -passout pass:123456 4096
openssl req -new -key private/cakey.pem -out private/ca.csr -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=MINIEYE/OU=tech/CN=mdgsf.xyz" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_ca -signkey private/cakey.pem -in private/ca.csr -out certs/root-ca.cert.pem -passin pass:123456

# s0/cert/am1.test.mdgsf.xyz.cert.pem  s0/cert/am1.test.mdgsf.xyz.key.pem
openssl genrsa -aes256 -out private/server-key.pem -passout pass:123456 4096
openssl req -new -key private/server-key.pem -out private/server.csr -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=MINIEYE/OU=tech/CN=am1.test.mdgsf.xyz" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_req -CA certs/root-ca.cert.pem -CAkey private/cakey.pem -CAserial ca.srl -CAcreateserial -in private/server.csr -out certs/am1.test.mdgsf.xyz.cert.pem -passin pass:123456
openssl rsa -in private/server-key.pem -out private/am1.test.mdgsf.xyz.key.pem -passin pass:123456

# c1/cert/asp1.test.mdgsf.xyz.cert.pem  c1/cert/asp1.test.mdgsf.xyz.key.pem
openssl genrsa -aes256 -out private/client-c1-key.pem -passout pass:123456 4096
openssl req -new -key private/client-c1-key.pem -out private/client-c1.csr -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=MINIEYE/OU=tech/CN=asp1.test.mdgsf.xyz" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_req -CA certs/root-ca.cert.pem -CAkey private/cakey.pem -CAserial ca.srl -in private/client-c1.csr -out certs/asp1.test.mdgsf.xyz.cert.pem -passin pass:123456
openssl rsa -in private/client-c1-key.pem -out private/asp1.test.mdgsf.xyz.key.pem -passin pass:123456

# c2/cert/arp1.test.mdgsf.xyz.cert.pem  c2/cert/arp1.test.mdgsf.xyz.key.pem
openssl genrsa -aes256 -out private/client-c2-key.pem -passout pass:123456 4096
openssl req -new -key private/client-c2-key.pem -out private/client-c2.csr -subj "/C=CN/ST=Guangdong/L=Shenzhen/O=MINIEYE/OU=tech/CN=arp1.test.mdgsf.xyz" -passin pass:123456
openssl x509 -req -days 365 -sha256 -extensions v3_req -CA certs/root-ca.cert.pem -CAkey private/cakey.pem -CAserial ca.srl -in private/client-c2.csr -out certs/arp1.test.mdgsf.xyz.cert.pem -passin pass:123456
openssl rsa -in private/client-c2-key.pem -out private/arp1.test.mdgsf.xyz.key.pem -passin pass:123456
```

**Note:** client 和 server 的时间要一致。

## Golang 中 HTTPS 实现双向验证

https://github.com/MDGSF/GoPractice/tree/master/HTTPSDemo

## 参考链接

https://www.openssl.org/
https://www.openssl.org/docs/manmaster/man1/
https://github.com/openssl/openssl

https://github.com/bigwhite/experiments/tree/master/gohttps/6-dual-verify-certs
https://yq.aliyun.com/articles/40398
https://www.cnblogs.com/littleatp/p/5878763.html
https://blog.csdn.net/liuchunming033/article/details/48470575
https://blog.csdn.net/gladmustang/article/details/42240999
https://timnash.co.uk/guessing-ssl-questions/
https://zhuanlan.zhihu.com/p/26646377
https://www.jianshu.com/p/79c284e826fa
https://github.com/openssl/openssl/blob/master/apps/openssl.cnf
