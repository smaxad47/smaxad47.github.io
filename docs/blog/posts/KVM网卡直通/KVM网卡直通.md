---
draft: false 
date: 2023-10-19
authors:
  - smaxad47
categories:
  - kvm
  - 虚拟机
---
# OMV下使用KVM虚拟机安装OP系统,并直通网卡
家庭网络核心是天宝的AMD300U,现在只有[升级款](https://item.taobao.com/item.htm?spm=a1z10.5-c-s.w4002-14967572216.16.2571f762d4dwSR&id=680979057915)了,不过大差不差. 网关+NAS ALL IN ONE.

<!-- more -->
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

## 硬件设备
分配思路  

|从上往下|系统名称|总线位置|OP内|
|-|-|-|-|
|网口1|enp5s0|05:00.0|ETH1|
|网口2|enp4s0|04:00.0|ETH2|
|网口3|eno1|03:00.0|没直通|
|网口4|enp2s0|02:00.0|没直通 ETH0(桥接)|

## 直通网卡
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
