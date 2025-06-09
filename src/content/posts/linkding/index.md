---
title: linkding 自建书签网站
published: 2025-06-09
description: "使用linkding自建书签网站,全平台共享书签"
tags: ["Bookmark", "Nas"]
category: 'Home'
draft: false
---

# linkding
托管于自家NAS之上,使用Cloudflare argo tunnel来反代出去实现任何地方任何平台都可以访问自己的书签.

## 安装教程
```
docker run --name linkding -p 9090:9090 -v {host-data-folder}:/etc/linkding/data -d sissbruecker/linkding:latest
```

## 迁移问题
迁移data文件夹内容时,如果全新创建一个docker会自动生成新的管理员账号导致无法登录.
需在docker创建时增加环境变量:
```
# Username of the initial superuser to create, leave empty to not create one
LD_SUPERUSER_NAME=
# Password for the initial superuser, leave empty to disable credentials authentication and rely on proxy authentication instead
LD_SUPERUSER_PASSWORD=
```
值为空,使用迁移文件创建docker后则可以正常登录使用.