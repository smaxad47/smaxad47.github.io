---
title: 家庭网络
published: 2026-04-30
description: "家庭网络设计规划以及问题记录"
tags: ["network"]
category: 'Network'
draft: false
---

# 组网
1. Zerotier,2023年左右还能用,目前来看江苏联通是用不了的.
2. tailscale

# 梯子相关
1. ColoudFlare的argotunnel在透明代理下经常会出现断线.解决方法:
    * `argotunnel.com`,域名加入白名单
    * 创建容器时添加参数 --protocol http2 实例： docker run --name zero --restart always cloudflare/cloudflared:latest tunnel --no-autoupdate --protocol http2 run --token xxxxxxxxxxx
    * openclash用户打开覆写设置-DNS设置-Fake-IP-Filter,填写下面规则
        ```
        #cloudflare tunnel
        +.argotunnel.com
        ```
2. [Sublink](https://sublink.works/)可以很省力的配置clash的Yaml,生成多份订阅.