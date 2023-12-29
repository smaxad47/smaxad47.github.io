---
draft: true 
date: 2023-12-28
authors:
  - smaxad47
categories:
  - NAS
  - docker
---
# 自家All In One
使用的OMV作为系统,时间久了好多配置都忘光了,记录一下,以防突然BOOM了
<!-- more -->

## 网卡
|从上往下|系统名称|总线位置|OP内|
|-|-|-|-|
|网口1|enp5s0|05:00.0|ETH1|
|网口2|enp4s0|04:00.0|ETH2|
|网口3|eno1|03:00.0|没直通|
|网口4|enp2s0|02:00.0|没直通 ETH0(桥接)|
## Docker
Calibre-Web:  
```
docker run -d --name=calibrewebEbook -e PUID=1000 -e PGID=1000 -e TZ=Asia/Shanghai -e DOCKER_MODS=linuxserver/mods:universal-calibre -p 8085:8083 -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeBookCenter:/books -v /nasconfig/calibre/config:/config linuxserver/calibre-web:latest
```


