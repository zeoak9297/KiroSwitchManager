# KiroSwitch Manager

KiroSwitch Manager 是一款跨平台桌面应用，用于管理 Kiro IDE 的多账号切换，并提供兼容 Claude/OpenAI API 格式的本地代理服务器。

基于 [Wails](https://wails.io/)（Go + Vue 3）构建，支持 Windows、macOS 和 Linux。

## 功能特性

### 多账号管理
- 支持三种认证方式：Social（Google/GitHub）、AWS Builder ID、Enterprise IDC
- 一键切换当前 Kiro 使用的账号
- 自动刷新 Token，可配置刷新间隔
- 实时显示账号使用额度（已用/总量）
- 自动检测账号封禁状态

### 本地 API 代理服务器
- 兼容 Claude API 和 OpenAI API 格式，可直接对接第三方工具
- 多账号负载均衡，自动轮询可用账号
- Token 桶限流（全局 + 单账号粒度）
- 熔断器模式（Circuit Breaker），自动隔离故障账号
- 支持流式（Streaming）和非流式响应
- API Key 认证保护
- 实时请求统计（成功/失败/Token 用量）

### 机器码重置
- 软重置：通过修改 Kiro 扩展注入自定义 Machine ID，无需管理员权限
- 硬重置：修改系统注册表/配置文件（Windows 需管理员权限）
- 自动管理 Kiro 进程（重置前关闭，重置后重启）
- 支持备份和恢复原始 Machine ID

### 模型锁定
- 锁定 Kiro 使用的 AI 模型（Sonnet 4.5 / Sonnet 4 / Haiku 4.5 / Opus 4.5）
- 通过文件监控（fsnotify）实时检测设置变更，自动恢复锁定模型

### 其他
- 6 套主题：深色、浅色、海洋蓝、梦幻紫、清新绿、玫瑰粉
- 自动检查更新（GitHub Releases）
- 无边框窗口，支持拖拽
- 调试日志开关

## 技术栈

| 层级 | 技术 |
|------|------|
| 桌面框架 | Wails v2 |
| 后端 | Go 1.24 |
| 前端 | Vue 3 + Vite |
| 构建 | Wails CLI, npm |
| CI/CD | GitHub Actions |

## 项目结构

```
├── main.go                  # 应用入口，Wails 配置
├── app.go                   # App 结构体，暴露给前端的方法
├── wails.json               # Wails 项目配置
├── backend/
│   ├── config/              # 应用配置常量（版本号、加密密钥、IDC 配置）
│   ├── models/              # 数据模型（Account、KiroProxy、Response）
│   ├── services/
│   │   ├── account_service.go        # 账号增删改查、Token 刷新
│   │   ├── token_service.go          # Social Token 刷新
│   │   ├── builderid_token_service.go # Builder ID Token 刷新
│   │   ├── idc_token_service.go      # IDC Token 刷新
│   │   ├── social_auth_service.go    # Google/GitHub OAuth 登录
│   │   ├── builderid_auth_service.go # Builder ID 设备授权登录
│   │   ├── idc_import_service.go     # Enterprise IDC 设备授权导入
│   │   ├── proxy_server_service.go   # API 代理服务器主逻辑
│   │   ├── proxy_client_service.go   # Kiro API 客户端（AWS Event Stream 解析）
│   │   ├── proxy_auth_service.go     # 代理凭据管理
│   │   ├── proxy_convert_service.go  # Claude/OpenAI → Kiro 请求格式转换
│   │   ├── proxy_account_pool.go     # 多账号连接池与负载均衡
│   │   ├── proxy_rate_limiter.go     # Token 桶限流器
│   │   ├── proxy_error_handler.go    # 错误分类与熔断器
│   │   ├── softreset_service.go      # 软重置（Machine ID 注入）
│   │   ├── hardreset_service_*.go    # 硬重置（按平台实现）
│   │   ├── model_lock_service.go     # 模型锁定与文件监控
│   │   ├── settings_service.go       # Kiro 安装路径检测
│   │   ├── process_service.go        # Kiro 进程管理
│   │   └── update_service.go         # GitHub 更新检查
│   └── utils/               # 工具函数（HTTP 客户端、加密、日志、设备信息）
├── frontend/
│   ├── src/
│   │   ├── App.vue           # 主界面（账号列表、设置、代理控制）
│   │   └── components/       # Vue 组件（AccountList、Toast）
│   └── wailsjs/              # Wails 自动生成的 Go 绑定
└── .github/workflows/        # CI/CD（多平台构建 + Release 发布）
```

## 环境要求

- Go 1.23+
- Node.js 20+
- Wails CLI v2（`go install github.com/wailsapp/wails/v2/cmd/wails@latest`）
- 平台依赖：
  - Windows：无额外依赖
  - macOS：Xcode Command Line Tools
  - Linux：`libgtk-3-dev` `libwebkit2gtk-4.1-dev`

## 快速开始

### 安装依赖

```bash
# 安装 Wails CLI
go install github.com/wailsapp/wails/v2/cmd/wails@latest

# 安装前端依赖
cd frontend && npm install && cd ..
```

### 开发模式

```bash
wails dev
```

启动后会自动打开应用窗口，前端热更新地址为 `http://localhost:5173`。

### 构建发布版

```bash
# Windows
wails build -clean -platform windows/amd64 -ldflags "-w -s -H windowsgui"

# macOS (Universal)
wails build -clean -platform darwin/universal -ldflags "-w -s"

# Linux
wails build -clean -platform linux/amd64 -ldflags "-w -s"
```

构建产物位于 `build/bin/` 目录。

## 代理服务器使用

启动代理后，可通过以下方式调用：

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

# OpenAI API 格式
curl http://localhost:8080/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "claude-sonnet-4-20250514",
    "messages": [{"role": "user", "content": "Hello"}]
  }'
```

API Key 在代理设置面板中查看，首次启动时自动生成。

## 数据存储

| 文件 | 路径 | 说明 |
|------|------|------|
| 账号数据 | `~/.kiroswitch-manager/accounts.json` | 所有已导入的账号信息 |
| 代理配置 | `~/.kiroswitch-manager/proxy_config.json` | 代理端口、API Key、限流配置 |
| Kiro 凭据 | `~/.aws/sso/cache/kiro-auth-token.json` | 当前 Kiro 使用的认证凭据 |

## 发布流程

推送 `v*.*.*` 格式的 Git Tag 即可触发 GitHub Actions 自动构建，生成三个平台的安装包并发布到 GitHub Releases。

```bash
git tag v2.0.0
git push origin v2.0.0
```

## 许可证

Copyright © 2026 kiroswitch
