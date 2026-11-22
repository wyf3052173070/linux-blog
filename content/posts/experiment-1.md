---
title: "Linux 虚拟机安装与 SSH 互连实验"
date: 2026-10-20
draft: false
categories: ["Linux课程"]
tags: ["虚拟机", "Ubuntu", "SSH"]
---

## 实验目标

在 Windows 宿主机上用 VirtualBox 安装两台 Ubuntu 22.04 虚拟机，配置 NAT 与 Host-only 双网卡，完成主机名、IP 地址和 SSH 远程登录，为后续文件管理、Shell 编程实验准备可复用的环境。

## 实验环境

| 项目 | 配置 |
|---|---|
| 宿主机 | Windows 11，VirtualBox 7.2.20 |
| 虚拟机 | Ubuntu 22.04 LTS，4 GB 内存，4 核 CPU，45 GB 虚拟硬盘 |
| node1 | NAT 10.0.2.15，Host-only 192.168.56.101 |
| node2 | NAT 10.0.2.15，Host-only 192.168.56.102 |

## 安装与网络配置

创建第一台虚拟机时选择 NAT 网络，安装完成后确认 sshd 已随系统启动：

```bash
hostname
ip addr
ping -c 4 www.baidu.com
systemctl status ssh --no-pager
```

![node1 双网卡与主机名](/images/exp1.png)

为了在两台虚拟机之间通信，给 node1 增加 Host-only 网卡，地址设为 192.168.56.101/24；然后用完整克隆生成 node2，并重新生成 MAC 地址，避免克隆后 MAC 冲突。启动 node2 后修改主机名与 Host-only 地址：

```bash
sudo hostnamectl set-hostname node2
sudo sed -i 's/node1/node2/g' /etc/hosts
ip addr
```

## SSH 远程登录

在 node1 上先写入 node2 的主机指纹，避免首次连接时交互卡住：

```bash
ssh-keyscan -H 192.168.56.102 >> ~/.ssh/known_hosts
ssh wyf2024900938@192.168.56.102
```

登录成功后执行：

```bash
hostname
uname -a
exit
```

终端显示当前主机为 node2，内核为 6.8.0-149-generic，说明 SSH 链路正常。

## 遇到的问题

1. 克隆后两台虚拟机的主机名和 IP 完全相同，导致 ssh 登录到“自己”。解决方法是重新生成 MAC、修改 hostname 与 Host-only 地址，并清理 known_hosts 中的旧指纹。
2. 第一次 ssh 连接提示主机指纹确认，自动化脚本会超时。使用 `ssh-keyscan` 预先写入 known_hosts 后解决。
3. Host-only 网卡未启用时，两台虚拟机只能通过 NAT 访问外网，不能互连。在 VirtualBox 的“网络”设置中启用 Host-only 适配器后恢复。

## 小结

虚拟机克隆可以快速复制实验环境，但必须处理 MAC、主机名、IP 和 SSH 指纹等唯一性问题。掌握 `ip addr`、`ping`、`systemctl status ssh` 与 `ssh` 之后，后续实验可以在 node1 上完成，也可以通过 SSH 在 node2 上验证，环境管理效率明显提高。
