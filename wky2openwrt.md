---
title: 玩客云刷openwrt并配置各类软件
description: 
published: true
date: 2024-12-26T09:13:59.278Z
tags: 
editor: markdown
dateCreated: 2024-12-17T10:20:57.388Z
---

# 一、下载固件

前往www.openwrt.ai订制固件，下载后刷到系统中。

使用终端ssh登录到openwrt中

通过命令查看分区使用情况：

`df -h`

```plaintext
[root@Kwrt:12:54 PM ~] # df -h
Filesystem                Size      Used Available Use% Mounted on
/dev/root                 1.1G    414.1M    687.2M  38% /
tmpfs                   500.0M    280.0K    499.7M   0% /tmp
tmpfs                   512.0K         0    512.0K   0% /dev
/dev/root                 1.1G    414.1M    687.2M  38% /opt/docker
/dev/mmcblk1p1           16.0M     13.7M      2.3M  86% /mnt/mmcblk1p1
```

# 二、使用openwrt官方提供的扩容脚本进行扩容

```plaintext
[root@Kwrt:05:24 PM ~] # wget -U "" -O expand-root.sh "https://openwrt.org/_export/code/docs/guide-user/advanced/expand_root?codeblock=0"
[root@Kwrt:05:25 PM ~] # chmod +x expand-root.sh 
[root@Kwrt:05:25 PM ~] # ./expand-root.sh
```

# 三、网络配置

停用DHCP

打开 DHCP 配置文件 `/etc/config/dhcp`，确认 `ignore` 选项是否已设置为 `1`：

`vi /etc/config/dhcp`

```plaintext
config dhcp 'lan'
    
option interface 'lan'
    
option ignore '1'   # 确保此处为 '1'
```

重启dnsmasq服务

```plaintext
/etc/init.d/dnsmasq restart
```

停用dnsmasq

```plaintext
/etc/init.d/dnsmasq stop
/etc/init.d/dnsmasq disable
```

安装SSR-Plus

```plaintext
opkg update

opkg install luci-app-ssr-plus
```

配置SSR-Plus

在左侧菜单中找到 **Passwall** 或 **SSR-Plus**

配置订阅节点、更新策略、更新gfw、ip数据库。

**全屋上网方案**

1.主路由器的网关和dns服务器指向openwrt 弃用

2.路由器分别开启两个WiFi节点，命名为name1、name2\_ssr，并将ssr的网关和dns指向openwrt，需要fq的设备连接name2\_ssr的WiFi，不需要fq的设备正常连接原有WiFi

√ 3.沿用现有的WiFi方案，需要fq的设备手动调整网络配置，将网关和dns指向openwrt，关闭随机mac地址

**安装smartDNS 及dig依赖**

```plaintext
opkg update
opkg install luci-app-smartdns bind-dig
```

配置smartDNS

在 **“服务”** 或 **“网络”** 菜单下找到 **SmartDNS，**

**分别勾选启动smartDNS、**自动设置 Dnsmasq选项

在上游服务器中添加以下内容：

#### **国内 DNS 服务器**

-   腾讯公共 DNS：`119.29.29.29`
-   阿里公共 DNS：`223.5.5.5`
-   114 DNS：`114.114.114.114`

#### **国外 DNS 服务器**

-   Google DNS：`8.8.8.8`
-   Cloudflare DNS：`1.1.1.1`
-   Quad9 DNS：`9.9.9.9`

高级设置中，打开缓存过期服务、持久化缓存、缓存大小更改为：10000

自定义设置中 添加分流规则

```xml
conf-file /usr/share/smartdns/chnlist.txt
server 223.5.5.5 -group china     # 阿里 DNS
server 119.29.29.29 -group china  # 腾讯 DNS
server 8.8.8.8 -group foreign     # Google DNS
server 1.1.1.1 -group foreign     # Cloudflare DNS
server 9.9.9.9 -group foreign     # Quad9 DNS
```

#### **创建目录并下载** `**chnlist.txt**`

先创建 SmartDNS 需要的目录，然后重新下载国内域名列表：

```plaintext
mkdir -p /usr/share/smartdns
wget -O /usr/share/smartdns/chnlist.txt https://raw.githubusercontent.com/felixonmars/dnsmasq-china-list/master/accelerated-domains.china.conf
```

验证文件是否下载成功

```plaintext
ls /usr/share/smartdns/chnlist.txt
```

重启服务

```plaintext
/etc/init.d/smartdns restart
```

测试分流规则

```plaintext
dig baidu.com @127.0.0.1
dig google.com @127.0.0.1
```

将SSR的dns服务指向smartDNS

SSR-Plus - 客户端 - 访问国外域名 DNS 服务器 调整为：

```plaintext
127.0.0.1:53
```

将openwrt的dns指向smartDNS

网络 - DHCP/DNS - 转发 - DNS转发 输入：

```plaintext
127.0.0.1#53
```

查看smart NDS监听端口

```plaintext
netstat -tulnp | grep smartdns
```

查看日志，是否存在解析冲突

```plaintext
logread | grep smartdns
```

重启服务

```plaintext
/etc/init.d/smartdns restart
```

配置Homeassistant

由于构建openwrt的时候已经选配了homeassistant，所以直接从界面上进行安装。

报错如下：

```plaintext
ERROR: Could not find the directory for Home Assistant
Manually change the directory to the root of your Home Assistant configuration
With the user that is running Home Assistant
and run the script again
restart homeassistant
```

确保主机上的配置目录 `/root/Configs/HomeAssistant` 存在，并且有正确的权限设置。

```plaintext
# 确保配置目录存在

mkdir -p /root/Configs/HomeAssistant

# 设置合适的权限

chown -R 1000:1000 /root/Configs/HomeAssistant

chmod -R 755 /root/Configs/HomeAssistant
```

**启动homeassistant**

**安装hacs，配置小米源**

设置 - 设备与服务 - 添加集成 - hacs - 安装

**安装xiaomi\_home**

HACS > Overflow Menu > Custom repositories > Repository: [_https://github.com/XiaoMi/ha\_xiaomi\_home.git_](https://github.com/XiaoMi/ha_xiaomi_home.git) & Category: Integration > ADD

设置 - 设备与服务 - 添加集成 - xiaomi\_home - 安装

**通过配置向导配置xiaomi\_home**

由于ha并非部署到本机，所以在配置向导中登录并授权后，会跳转到一个localhost，需要手动替换为openwrt地址以鉴权