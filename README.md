# L2TP VPN 一键安装脚本

> Linux 下一键搭建 L2TP/IPSec VPN 服务器，支持 CentOS、Debian、Ubuntu 等主流发行版。

**作者**：Telegram：@TuuT1688 千门 | Telegram：@qiaozh1 乔治

---

## 📦 功能介绍

本脚本可以一键在你的 Linux VPS/服务器上搭建 L2TP/IPSec VPN 服务，安装完成后即可使用手机、电脑等设备连接 VPN。

### 核心功能

- **一键安装**：自动安装配置 libreswan（IPSec）+ xl2tpd（L2TP），全程无人值守
- **自动检测系统**：自动识别 CentOS、Debian、Ubuntu 版本，选择对应的安装路径
- **防火墙自动配置**：自动配置 iptables（CentOS 6/Debian）或 firewalld（CentOS 7）
- **开机自启**：安装完成后自动设置开机启动
- **用户管理**：支持添加、删除、查看、修改 VPN 用户

### 技术栈

| 组件 | 说明 |
|------|------|
| libreswan 3.27 | 提供 IPSec 加密隧道 |
| xl2tpd | 提供 L2TP 隧道协议 |
| PPP | 用户认证（MS-CHAP v2） |

---

## 💻 支持的操作系统

| 系统 | 支持版本 |
|------|----------|
| CentOS | 6.x、7.x |
| Debian | 7.x 及以上 |
| Ubuntu | 12.04 及以上 |

> ⚠️ 需要服务器支持 TUN/TAP，OpenVZ 虚拟化可能不支持 IPSec。

---

## 🚀 安装方式

### 方法一：一键命令安装（推荐）

SSH 登录你的 Linux 服务器，执行以下命令：

```bash
curl -O https://raw.githubusercontent.com/menqian53-del/TG-TuuT1688/master/l2tp_install.sh && chmod +x l2tp_install.sh && bash l2tp_install.sh
```

### 方法二：手动上传安装

1. 下载 `l2tp_install.sh` 上传到服务器
2. SSH 登录服务器，执行：

```bash
chmod +x l2tp_install.sh
bash l2tp_install.sh
```

---

## 📋 安装过程详解

执行脚本后，会依次进行以下步骤：

### 1. 系统检测

脚本启动后会自动检测：
- 是否为 root 用户
- TUN/TAP 是否可用
- SELinux 状态（自动关闭）
- 操作系统版本
- 服务器硬件信息（CPU、内存、IP 等）

### 2. 参数配置

系统检测通过后，会让你输入以下配置（全部可以回车使用默认值）：

| 配置项 | 说明 | 默认值 |
|--------|------|--------|
| IP 范围 | VPN 客户端分配的内网 IP 段 | `192.168.18` |
| 预共享密钥 (PSK) | IPSec 连接的共享密钥 | `qiaozhi` |
| 用户名 | VPN 登录用户名 | `qiaozhi` |
| 密码 | VPN 登录密码 | 随机生成 10 位字符 |

示例交互：
```
请输入ip范围:
(默认范围: 192.168.18):

请输入预共享密钥:
(默认预共享密钥: qiaozhi):

请输入用户名:
(默认用户名: qiaozhi):

请输入 qiaozhi's 密码:
(默认密码: aB3xY9kLm2):
```

### 3. 自动安装

按任意键后，脚本会自动完成：
- 安装依赖包（gcc、make、flex、bison 等）
- 编译安装 libreswan
- 配置 IPSec（`/etc/ipsec.conf`、`/etc/ipsec.secrets`）
- 配置 L2TP（`/etc/xl2tpd/xl2tpd.conf`、`/etc/ppp/options.xl2tpd`）
- 设置 PPP 用户认证（`/etc/ppp/chap-secrets`）
- 配置防火墙规则（开放 UDP 500、4500、1701 端口）
- 配置内核参数（开启 IP 转发）
- 设置开机自启

### 4. 安装完成

安装完成后会显示：

```
###############################################################
# L2TP VPN Auto Installer                                     #
# Linux 一键安装L2TP脚本 (汉化定制版)                         #
#                                                             #
# Telegram：@TuuT1688 千门                                   #
# Telegram：@qiaozh1 乔治                                     #
###############################################################

如果上面没有[FAILED]，您可以连接到您的L2TP

L2TP使用用户名/密码的VPN服务器如下:

服务器 IP : 你的服务器IP
预共享密钥 : qiaozhi
用户名 : qiaozhi
密码 : 你的密码
```

> ⚠️ 重点：如果上面的输出中**没有 `[FAILED]`**，说明安装成功！

---

## 📱 客户端连接教程

### Windows 10/11

1. 打开 **设置** → **网络和 Internet** → **VPN** → **添加 VPN 连接**
2. 填写：
   - VPN 提供商：`Windows（内置）`
   - 连接名称：随意（如 `MyVPN`）
   - 服务器地址：你的服务器 IP
   - VPN 类型：`使用预共享密钥的 L2TP/IPsec`
   - 预共享密钥：你设置的 PSK
   - 用户名：你设置的用户名
   - 密码：你设置的密码
3. 点击 **连接**

### macOS / iOS

1. 打开 **设置** → **VPN** → **添加 VPN 配置**
2. 类型选择 **L2TP**
3. 填写：
   - 服务器：你的服务器 IP
   - 账户：用户名
   - 密码：密码
   - 共享密钥：PSK
4. 保存并连接

### Android

1. 打开 **设置** → **网络和互联网** → **VPN** → **添加 VPN**
2. 类型选择 **L2TP/IPSec PSK**
3. 填写：
   - 名称：随意
   - 服务器地址：你的服务器 IP
   - IPSec 预共享密钥：PSK
   - 用户名：用户名
   - 密码：密码
4. 保存并连接

---

## 🔧 用户管理命令

安装完成后，脚本会被复制到 `/usr/bin/l2tp`，之后可以用 `l2tp` 命令管理用户：

### 添加用户

```bash
l2tp -a
# 或者
l2tp --add
```

按提示输入用户名和密码（密码可留空使用随机生成的）。

### 删除用户

```bash
l2tp -d
# 或者
l2tp --del
```

输入要删除的用户名即可。

### 查看所有用户

```bash
l2tp -l
# 或者
l2tp --list
```

显示所有用户名和密码。

### 修改用户密码

```bash
l2tp -m
# 或者
l2tp --mod
```

输入用户名后设置新密码。

### 查看帮助

```bash
l2tp -h
# 或者
l2tp --help
```

---

## 🔍 常用运维命令

| 操作 | 命令 |
|------|------|
| 检查 IPSec 状态 | `ipsec verify` |
| 查看 IPSec 连接 | `ipsec status` |
| 启动 IPSec 服务 | `systemctl start ipsec` 或 `/etc/init.d/ipsec start` |
| 重启 IPSec 服务 | `systemctl restart ipsec` 或 `/etc/init.d/ipsec restart` |
| 启动 L2TP 服务 | `systemctl start xl2tpd` 或 `/etc/init.d/xl2tpd start` |
| 重启 L2TP 服务 | `systemctl restart xl2tpd` 或 `/etc/init.d/xl2tpd restart` |
| 查看防火墙规则 | `firewall-cmd --list-all` 或 `iptables -L -n` |
| 查看 VPN 用户 | `cat /etc/ppp/chap-secrets` |
| 查看 IPSec 配置 | `cat /etc/ipsec.conf` |
| 查看 L2TP 配置 | `cat /etc/xl2tpd/xl2tpd.conf` |
| 查看 PPP 配置 | `cat /etc/ppp/options.xl2tpd` |

---

## ❓ 常见问题

### Q: 安装后连接不上？

1. 确认云服务商的**安全组/防火墙**已开放 UDP 端口：`500`、`4500`、`1701`
2. 在服务器上执行 `ipsec verify`，检查是否有 `[FAILED]` 项
3. 确认 `net.ipv4.ip_forward=1`：`sysctl net.ipv4.ip_forward`

### Q: Windows 报错 809？

Windows 默认不支持 L2TP/IPSec 的预共享密钥方式，需要修改注册表：

```reg
reg add "HKLM\SYSTEM\CurrentControlSet\Services\PolicyAgent" /v AssumeUDPEncapsulationContextOnSendRule /t REG_DWORD /d 2 /f
```

修改后**重启电脑**生效。

### Q: 如何查看当前连接的用户？

```bash
ipsec status
```

### Q: 如何完全卸载？

```bash
# CentOS
yum remove -y libreswan xl2tpd ppp
rm -rf /etc/ipsec.* /etc/xl2tpd/ /etc/ppp/chap-secrets /etc/firewalld/services/xl2tpd.xml /usr/bin/l2tp

# Debian/Ubuntu
apt-get remove -y xl2tpd ppp
rm -rf /etc/ipsec.* /etc/xl2tpd/ /etc/ppp/chap-secrets /usr/bin/l2tp
```

---

## 📄 文件说明

安装后脚本会修改/创建以下文件：

| 文件路径 | 说明 |
|----------|------|
| `/etc/ipsec.conf` | IPSec 主配置文件 |
| `/etc/ipsec.secrets` | IPSec 密钥文件 |
| `/etc/xl2tpd/xl2tpd.conf` | L2TP 服务配置 |
| `/etc/ppp/options.xl2tpd` | PPP 拨号选项 |
| `/etc/ppp/chap-secrets` | VPN 用户名密码 |
| `/etc/sysctl.conf` | 内核参数（IP 转发等） |
| `/usr/bin/l2tp` | 用户管理快捷命令 |

---

## 📞 联系方式

- **Telegram**：@TuuT1688 千门
- **Telegram**：@qiaozh1 乔治

---

## ⚠️ 免责声明

本脚本仅供学习交流使用，请遵守当地法律法规。使用者需自行承担使用本脚本的一切风险和后果。
