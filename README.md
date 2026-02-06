<div align="center">

# ⚡ KiroSwitch Manager

### Kiro IDE 多账号管理 & API 代理工具

[![Release](https://img.shields.io/github/v/release/zeoak9297/KiroSwitchManager?style=for-the-badge&color=blue&label=最新版本)](https://github.com/zeoak9297/KiroSwitchManager/releases/latest)
[![Downloads](https://img.shields.io/github/downloads/zeoak9297/KiroSwitchManager/total?style=for-the-badge&color=green&label=总下载量)](https://github.com/zeoak9297/KiroSwitchManager/releases)
[![Platform](https://img.shields.io/badge/平台-Windows%20|%20macOS%20|%20Linux-blueviolet?style=for-the-badge)](https://github.com/zeoak9297/KiroSwitchManager/releases/latest)
[![Go](https://img.shields.io/badge/Go-1.24-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://go.dev/)
[![Vue](https://img.shields.io/badge/Vue-3-4FC08D?style=for-the-badge&logo=vue.js&logoColor=white)](https://vuejs.org/)

<br/>

<img src="https://img.shields.io/badge/Wails-v2-red?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Ik0xMiAyTDIgN2wxMCA1IDEwLTV6TTIgMTdsMTAgNSAxMC01TTIgMTJsMTAgNSAxMC01Ii8+PC9zdmc+"/>

**一站式管理你的 Kiro IDE 账号，内置 Claude/OpenAI 兼容代理服务器**

[📦 立即下载](#-安装) · [✨ 功能特性](#-功能特性) · [🚀 快速开始](#-快速开始) · [📖 使用指南](#-使用指南)

</div>

---

## ✨ 功能特性

<table>
<tr>
<td width="50%">

### 🔄 多账号管理
- 支持 **Social**（Google/GitHub）、**AWS Builder ID**、**Enterprise IDC** 三种认证
- 一键切换 Kiro 当前使用的账号
- 自动刷新 Token，可配置刷新间隔
- 实时显示账号使用额度
- 自动检测账号封禁状态

</td>
<td width="50%">

### 🌐 API 代理服务器
- 兼容 **Claude API** 和 **OpenAI API** 格式
- 多账号负载均衡，自动轮询
- Token 桶限流（全局 + 单账号）
- 熔断器模式，自动隔离故障账号
- 支持流式 & 非流式响应

</td>
</tr>
<tr>
<td width="50%">

### 🔑 机器码重置
- **软重置**：注入自定义 Machine ID，无需管理员权限
- **硬重置**：修改系统注册表/配置（Windows 需管理员）
- 自动管理 Kiro 进程生命周期
- 支持备份和恢复原始 Machine ID

</td>
<td width="50%">

### 🎨 更多特性
- **模型锁定**：锁定 Sonnet / Opus / Haiku 模型
- **6 套主题**：深色、浅色、海洋蓝、梦幻紫、清新绿、玫瑰粉
- **自动更新检查**
- **无边框窗口**，支持拖拽

</td>
</tr>
</table>

---

## 📦 安装

前往 [Releases](https://github.com/zeoak9297/KiroSwitchManager/releases/latest) 下载对应平台的安装包：

| 平台 | 文件 | 架构 |
|:---:|:---:|:---:|
| 🪟 Windows | `kiroswitch-manager-windows-amd64-*.zip` | x64 |
| 🍎 macOS | `kiroswitch-manager-macos-universal-*.tar.gz` | Universal (Intel + Apple Silicon) |
| 🐧 Linux | `kiroswitch-manager-linux-amd64-*.tar.gz` | x64 |

> **Windows 用户**：解压后直接运行 `.exe` 文件即可
>
> **macOS 用户**：解压后将 `.app` 拖入 Applications 文件夹，首次运行如遇安全提示，前往 系统设置 → 隐私与安全性 → 允许运行
>
> **Linux 用户**：解压后赋予执行权限 `chmod +x kiroswitch-manager-*` 后运行

---

## 🚀 快速开始

1. 下载并启动 KiroSwitch Manager
2. 点击 **导入账号**，选择认证方式（推荐 Social 登录）
3. 完成授权后，账号自动出现在列表中
4. 点击账号即可一键切换

---

## 📖 使用指南

### 代理服务器

启动代理后，可直接对接支持 Claude/OpenAI API 的第三方工具：

```bash
# Claude API 格式
curl http://localhost:8080/v1/messages \
  -H "x-api-key: YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "max_tokens": 1024,
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

```bash
# OpenAI API 格式
curl http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

> API Key 在代理设置面板中查看，首次启动时自动生成。

### 模型锁定

支持锁定以下模型，防止 Kiro 自动切换：

| 模型 | 倍率 |
|:---:|:---:|
| Claude Sonnet 4.5 | 1.3x |
| Claude Sonnet 4 | 1.3x |
| Claude Haiku 4.5 | 0.4x |
| Claude Opus 4.5 | 2.2x |

---

## 🛠️ 技术栈

<div align="center">

| | 技术 | 版本 |
|:---:|:---:|:---:|
| 🖥️ | **Wails** | v2 |
| ⚙️ | **Go** | 1.24 |
| 🎨 | **Vue** | 3 |
| ⚡ | **Vite** | 3 |

</div>

---

## ❓ 常见问题

<details>
<summary><b>macOS 提示"无法验证开发者"怎么办？</b></summary>
<br/>
前往 系统设置 → 隐私与安全性 → 找到 KiroSwitch Manager → 点击"仍要打开"
</details>

<details>
<summary><b>代理服务器连接不上？</b></summary>
<br/>
检查防火墙是否放行了代理端口（默认 8080），确认 API Key 是否正确填写。
</details>

<details>
<summary><b>账号显示"已封禁"？</b></summary>
<br/>
该账号的凭证已失效或被限制，可尝试删除后重新导入，或使用机器码重置功能。
</details>

---

## 📄 许可证

Copyright © 2026 pahhcn. All rights reserved.

本软件仅供个人学习和研究使用。

---

<div align="center">

**如果觉得有用，请给个 ⭐ Star 支持一下！**

[![Star History Chart](https://api.star-history.com/svg?repos=zeoak9297/KiroSwitchManager&type=Date)](https://star-history.com/#zeoak9297/KiroSwitchManager&Date)

</div>
