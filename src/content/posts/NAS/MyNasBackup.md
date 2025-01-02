---
title: 天宝AMD300U AIO
published: 2025-01-02
description: "自用AIO使用备用"
tags: ["Nas","IstoreOS","KVM"]
category: 'Home'
draft: false
---

# 自家All In One
使用的OMV作为系统,时间久了好多配置都忘光了,记录一下,以防突然BOOM了
<!-- more -->


## Docker使用中

HomePage:
从 v0.7.1 开始，主页从 benphelps/homepage 迁移到位于gethomepage/homepage的“组织” 。这样做的原因是为了使项目长久存在并允许社区维护。
迁移您的安装应该像更改image: ghcr.io/benphelps/homepage:latest为一样简单image: ghcr.io/gethomepage/homepage:latest。
```
docker run --name HomePage2 -p 7000:3000 -v /<custom_address>/homepage/config:/app/config -v /var/run/docker.sock:/var/run/docker.sock ghcr.io/gethomepage/homepage:latest
```

kavita:
```
sudo docker run --name kavita -p 5000:5000 \
-v /<custom_address>/kavita/data:/manga \
-v /<custom_address>/kavita/config:/kavita/config \
--restart unless-stopped \
-e TZ=Asia/Shanghai \
-d jvmilazz0/kavita:latest
```
nastools2.91:
```
docker run -d --name nastools291 --hostname nastools291 --network=host -v /<custom_address>/nastool/config:/config -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeVideoCenter:/media -e PUID=0 -e PGID=0 -e UMASK=000 -e NASTOOL_AUTO_UPDATE=false 19970688/nastools-bt:latest
```
baidunetdisk:
```
docker create --name=baidunetdisk -p 8084:5800 -p 8086:5900 -v /<custom_address>/baidunetdisk:/config -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeFileCenter/BaiduNetDisk:/config/baidunetdiskdownload johngong/baidunetdisk:latest
```

Qbittorrent:
```
docker run -dit \
  -v /<custom_address>/qbit/data:/data \
  -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeVideoCenter/sources:/downloads \
  -e TZ="Asia/Shanghai" \
  -e WEBUI_PORT=8081 \
  -e BT_PORT=34567 \
  -p 8081:8081 \
  -p 34567:34567/tcp \
  -p 34567:34567/udp \
  --name qbittorrent \
  --hostname qbittorrent \
  nevinee/qbittorrent:4.3.9
```
metatube:
```
docker run -d -p 8080:8080 -v /<custom_address>/metatube/config:/config --name metatube metatube/metatube-server:latest -dsn /config/metatube.db
```
Aia2-pro:
```
docker run -d --name aria2-pro --network host -e RPC_SECRET=AiD1erika -e RPC_PORT=6800 -e LISTEN_PORT=6888 -v /<custom_address>/aria2/config:/config -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeVideoCenter/sources:/downloads -v /srv/dev-disk-by-uuid-a4b9ac85-2128-4f9c-a6a8-72dc75fe2bde/HomeFileCenter:/pcdownload p3terx/aria2-pro
```

netdata:
```
docker run -d --name=netdata \
  --pid=host \
  --network=host \
  -v /<custom_address>/netdataconfig/netdata:/etc/netdata \
  -v netdatalib:/var/lib/netdata \
  -v netdatacache:/var/cache/netdata \
  -v /etc/passwd:/host/etc/passwd:ro \
  -v /etc/group:/host/etc/group:ro \
  -v /proc:/host/proc:ro \
  -v /sys:/host/sys:ro \
  -v /etc/os-release:/host/etc/os-release:ro \
  -v /var/run/docker.sock:/var/run/docker.sock:ro \
  --restart unless-stopped \
  --cap-add SYS_PTRACE \
  --cap-add SYS_ADMIN \
  --security-opt apparmor=unconfined \
  netdata/netdata
```

vaultwarden:
```
docker run -d --name vaultwarden -v /<custom_address>/vaulwarden/vault-data/:/data/ -p 3002:80 vaultwarden/server:latest
```
## Docker弃用
Calibre-Web:
```
docker run -d --name=calibrewebEbook -e PUID=1000 -e PGID=1000 -e TZ=Asia/Shanghai -e DOCKER_MODS=linuxserver/mods:universal-calibre -p 8085:8083 -v <custom_address>:/books -v <custom_address>:/config linuxserver/calibre-web:latest
```

## 
使用[Cloudflare](https://dash.cloudflare.com/)的Zero Trust Tunnel反代内部服务。


# 网卡直通
## 网卡
|从上往下|系统名称|总线位置|OP内|
|-|-|-|-|
|网口1|enp5s0|05:00.0|ETH1|
|网口2|enp4s0|04:00.0|ETH2|
|网口3|eno1|03:00.0|没直通|
|网口4|enp2s0|02:00.0|没直通 ETH0(桥接)|

## 基础设置
```
sudo vim /etc/default/grub
```
修改**GRUB_CMDLINE_LINUX_DEFAULT**项:  
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on pcie_acs_override=downstream,multifunction"
```
Intel平台的话,改成`GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"`,后面的自行测试要不要加,这台机器是要加的.  
修改之后使用命令更新grub  
```
sudo update-grub
```


编辑KVM虚拟机的XML文件,根据总线位置添加内容,添加了网口1和网口2的直通
```
<devices>
......
    <hostdev mode='subsystem' type='pci' managed='yes'>
      <source>
        <address domain='0x0000' bus='0x05' slot='0x00' function='0x0'/>
      </source>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x00' function='0x0'/>
    </hostdev>
    <hostdev mode='subsystem' type='pci' managed='yes'>
      <source>
        <address domain='0x0000' bus='0x04' slot='0x00' function='0x0'/>
      </source>
      <address type='pci' domain='0x0000' bus='0x00' slot='0x00' function='0x0'/>
    </hostdev>
......
</devices>
```