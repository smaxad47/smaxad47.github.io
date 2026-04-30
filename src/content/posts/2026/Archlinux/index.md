---
title: 从Windows到Linux
published: 2026-04-30
description: "转Linux遇到的问题收集"
tags: ["linux"]
category: 'Linux'
draft: false
---

# Endeavouros
Arch系的发行版
1. Linux的OOM，8G内存的机器经常会触发OOM，这通常会导致系统变得非常缓慢甚至完全无响应，为了解决这个问题需安装[earlyoom](https://archlinux.org/packages/extra/x86_64/earlyoom/)
2. Chrome使用触摸板双指返回前进，在快捷方式里配置:
`/usr/share/applications`
`google-chrome.desktop`
找到`Exec`开头的行，在应用程序名称之后和其它参数之前添加 `--enable-features=TouchpadOverscrollHistoryNavigation`
不过也有个问题,Chrome更新之后要重新添加.

# Ubuntu
不用介绍了
1. SSH问题,令人迷惑的设计,除了`/etc/ssh/sshd_config`有SSH配置外,还有一个`/etc/ssh/sshd_config.d/60-cloudimg-settings.conf`,其中仅仅只有一行`PasswordAuthentication no`,导致无论在`/etc/ssh/sshd_config`怎么改,都会被那一行覆盖.