# Hermes Agent 安装指南

本指南记录了 Hermes Agent 在 macOS 系统上的完整安装过程和常见问题解决方案。

## 📋 安装摘要

**安装日期：** 2026-04-18  
**Hermes Agent 版本：** v0.10.0  
**Python 版本：** 3.11.15  
**操作系统：** macOS (Darwin 23.6.0)

## ✅ 安装成功的组件

| 组件 | 版本 | 说明 |
|------|------|------|
| Hermes Agent | v0.10.0 | AI 助手核心程序 |
| Python | 3.11.15 | 通过 uv 管理 |
| uv | 最新 | Python 包管理器 |
| OpenAI SDK | 2.24.0 | AI 模型接口 |
| ripgrep | 15.1.0 | 文本搜索工具 |
| ffmpeg | 8.1 | 多媒体处理工具 |

## 🚀 快速开始

### 启动 Hermes Agent

```bash
# 方法 1：使用全局别名（推荐）
source ~/.zshrc
hermes

# 方法 2：直接进入项目目录
cd ~/.hermes/hermes-agent
uv run hermes
```

### 常用命令

#### 基础命令

```bash
# 查看版本信息
hermes --version

# 查看帮助信息
hermes --help

# 启动交互式聊天
hermes chat

# 配置模型和提供商
hermes model

# 查看系统状态
hermes status
```

#### 认证相关

```bash
# 登录推理服务提供商
hermes login

# 登出提供商
hermes logout

# 管理认证凭据
hermes auth
```

#### 配置管理

```bash
# 交互式设置向导
hermes setup

# 查看和编辑配置
hermes config

# 配置文件诊断
hermes doctor

# 导出设置摘要（用于支持/调试）
hermes dump
```

#### 功能模块

```bash
# 技能管理
hermes skills

# 插件管理
hermes plugins

# 记忆管理
hermes memory

# 工具管理
hermes tools

# MCP 管理
hermes mcp
```

#### 会话管理

```bash
# 恢复之前的会话
hermes --resume SESSION

# 继续会话
hermes --continue [SESSION_NAME]

# 会话管理
hermes sessions
```

#### 高级功能

```bash
# WhatsApp 集成设置
hermes whatsapp

# Cron 任务管理
hermes cron

# Webhook 管理
hermes webhook

# 备份 Hermes 数据
hermes backup

# 恢复备份
hermes import

# 调试工具
hermes debug

# 更新 Hermes
hermes update
```

## 🔧 安装步骤

### 1. 系统要求

- macOS 操作系统
- Homebrew（用于安装系统依赖）
- Git
- 网络连接（如需代理，确保 Clash Verge 等代理工具正常运行）

### 2. 安装 uv 包管理器

```bash
# 创建本地 bin 目录
mkdir -p ~/.local/bin

# 从官方源安装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 添加到 PATH（如未添加）
export PATH="$HOME/.local/bin:$PATH"
```

### 3. 克隆 Hermes Agent 仓库

**重要：** 使用完整克隆，不要使用 `--depth 1`，否则会缺少 `pyproject.toml` 等关键文件。

```bash
# 使用 HTTP/1.1 避免 HTTP/2 连接问题
git -c http.version=HTTP/1.1 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

### 4. 安装 Python 依赖

```bash
cd ~/.hermes/hermes-agent
uv sync
```

### 5. 安装系统依赖

```bash
# 安装 ripgrep 和 ffmpeg
brew install ripgrep ffmpeg
```

### 6. 配置全局别名（可选）

```bash
# 添加到 ~/.zshrc
echo 'alias hermes="cd ~/.hermes/hermes-agent && uv run hermes"' >> ~/.zshrc

# 重新加载配置
source ~/.zshrc
```

### 7. 配置 Git 代理（如需要）

如果你的网络需要代理访问 GitHub：

```bash
# 配置 git 全局代理（根据 Clash Verge 实际端口调整）
git config --global http.proxy http://127.0.0.1:7897
git config --global https.proxy http://127.0.0.1:7897

# 查看 Clash Verge 配置文件获取实际端口
cat ~/Library/Application\ Support/io.github.clash-verge-rev.clash-verge-rev/clash-verge.yaml | grep mixed-port
```

## ⚠️ 常见问题与解决方案

### 问题 1：uv 包管理器安装失败

**错误信息：**
```
curl: (16) Error in the HTTP2 framing layer
✗ uv installed but not found on PATH
```

**解决方案：** 手动从 uv 官方源安装
```bash
mkdir -p ~/.local/bin
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"
```

### 问题 2：Git 克隆失败 - HTTP2 问题

**错误信息：**
```
fatal: unable to access 'https://github.com/nousresearch/hermes-agent.git/': Error in the HTTP2 framing layer
```

**解决方案：** 强制使用 HTTP/1.1
```bash
git -c http.version=HTTP/1.1 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

### 问题 3：网络传输中断

**错误信息：**
```
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
```

**解决方案：** 增加缓冲区大小并使用完整克隆
```bash
git -c http.version=HTTP/1.1 -c http.postBuffer=524288000 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

### 问题 4：模块导入错误

**错误信息：**
```
ModuleNotFoundError: No module named 'hermes_cli'
```

**解决方案：** 使用完整克隆（不要使用 `--depth 1`）
```bash
# 错误做法
git clone --depth 1 https://github.com/nousresearch/hermes-agent.git

# 正确做法
git -c http.version=HTTP/1.1 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

### 问题 5：uv run 命令失败

**错误信息：**
```
error: Failed to spawn: `hermes`
  Caused by: No such file or directory (os error 2)
```

**解决方案：** 在正确的目录中运行或使用全局别名
```bash
# 方法 1：进入项目目录
cd ~/.hermes/hermes-agent
uv run hermes

# 方法 2：使用别名（推荐）
hermes
```

## 📚 相关资源

- [Hermes Agent 官方仓库](https://github.com/nousresearch/hermes-agent)
- [uv 官方文档](https://github.com/astral-sh/uv)
- [Clash Verge 文档](https://github.com/clash-verge-rev/clash-verge-rev)

## 📝 更新日志

### 2026-04-18
- ✅ 成功安装 Hermes Agent v0.10.0
- ✅ 解决 HTTP/2 连接问题
- ✅ 解决模块导入错误
- ✅ 配置全局别名
- ✅ 配置 Git 代理
- ✅ 创建完整的安装文档

## 🤝 贡献

如果你在安装过程中遇到其他问题，欢迎提交 Issue 或 Pull Request 到本仓库。

## 📄 许可证

本安装指南遵循 MIT 许可证。Hermes Agent 的许可证请参考其官方仓库。
