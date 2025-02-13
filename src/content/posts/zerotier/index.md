---
title: Zerotier 自建Moon
published: 2025-02-13
description: "UDP被ISP QOS,打洞直连速度非常不理想,自建Moon中转"
tags: ["Zerotier", "网络通信"]
category: '组网'
draft: false
---

# Zerotier Moon 官方教程
[官方教程](https://docs.zerotier.com/roots/)

# 自整理教程及细节
1. 服务端防火墙放行9993的TCP以及UDP
2. 服务器安装好Zerotier后,加入你的Zerotier网络,命令为`zerotier-cli join xxxxxxx`
## 安装Zerotier
```
curl -s https://install.zerotier.com/ | sudo bash
```
## 生成配置文件
```
cd /var/lib/zerotier-one
sudo zerotier-idtool initmoon identity.public > moon.json
```
## 修改配置文件
生成的默认配置文件如下
```

    {
      "id": "deadbeef00",
      "objtype": "world",
      "roots": [
        {
          "identity": "deadbeef00:0:34031483094...",
          "stableEndpoints": []
        }
      ],
      "signingKey": "b324d84cec708d1b51d5ac03e75afba501a12e2124705ec34a614bf8f9b2c800f44d9824ad3ab2e3da1ac52ecb39ac052ce3f54e58d8944b52632eb6d671d0e0",
      "signingKey_SECRET": "ffc5dd0b2baf1c9b220d1c9cb39633f9e2151cf350a6d0e67c913f8952bafaf3671d2226388e1406e7670dc645851bf7d3643da701fd4599fedb9914c3918db3",
      "updatesMustBeSignedBy": "b324d84cec708d1b51d5ac03e75afba501a12e2124705ec34a614bf8f9b2c800f44d9824ad3ab2e3da1ac52ecb39ac052ce3f54e58d8944b52632eb6d671d0e0",
      "worldType": "moon"
    }
```
修改其中的`"stableEndpoints": []`,`"stableEndpoints": [ "10.0.0.2/9993","2001:abcd:abcd::1/9993" ]`,其中地址为服务器的外网地址,V4 V6都填或者填一个都行,如:
```
    {
      "id": "deadbeef00",
      "objtype": "world",
      "roots": [
        {
          "identity": "deadbeef00:0:34031483094...",
          "stableEndpoints": [ "10.0.0.2/9993","2001:abcd:abcd::1/9993" ]
        }
      ],
      "signingKey": "b324d84cec708d1b51d5ac03e75afba501a12e2124705ec34a614bf8f9b2c800f44d9824ad3ab2e3da1ac52ecb39ac052ce3f54e58d8944b52632eb6d671d0e0",
      "signingKey_SECRET": "ffc5dd0b2baf1c9b220d1c9cb39633f9e2151cf350a6d0e67c913f8952bafaf3671d2226388e1406e7670dc645851bf7d3643da701fd4599fedb9914c3918db3",
      "updatesMustBeSignedBy": "b324d84cec708d1b51d5ac03e75afba501a12e2124705ec34a614bf8f9b2c800f44d9824ad3ab2e3da1ac52ecb39ac052ce3f54e58d8944b52632eb6d671d0e0",
      "worldType": "moon"
    }
```
## 生成签名配置文件
```
zerotier-idtool genmoon moon.json
```
## 使用签名的配置文件
Linux:
```
# 创建moons.d文件夹
mkdir /var/lib/zerotier-one/moons.d/
# 放入moon文件
cp 0000xxxxxxxxxx.moon /var/lib/zerotier-one/moons.d/
# 重启zerotier服务，应用moon
systemctl restart zerotier-one
```
Windows:
运行`services.msc`
找到"ZeroTier One"服务,打开安装目录如`C:\ProgramData\ZeroTier\One\`,创建文件夹moons.d  
放入0000xxxxxxxxxx.moon文件  
重启"ZeroTier One"服务

## 验证
```
zerotier-cli listpeers
zerotier-cli peers
```
看到Moon,IP是服务器的地址就是生效了.