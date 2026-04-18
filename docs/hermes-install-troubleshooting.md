# Hermes Agent 安装问题排查与解决方案

## 安装命令
```bash
curl -fsSL https://hermes-agent.nousresearch.com/install.sh | bash
```

## 遇到的问题与解决方案

### 1. uv 包管理器安装失败

**问题：**
```
curl: (16) Error in the HTTP2 framing layer
✗ uv installed but not found on PATH
```

**原因：** 安装脚本在下载 uv 时遇到 HTTP2 协议问题

**解决方案：** 手动从 uv 官方源安装
```bash
# 创建本地 bin 目录
mkdir -p ~/.local/bin

# 从官方源安装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 添加到 PATH
export PATH="$HOME/.local/bin:$PATH"

# 验证安装
which uv
```

### 2. Git 克隆失败 - HTTP2 问题

**问题：**
```
fatal: unable to access 'https://github.com/nousresearch/hermes-agent.git/': Error in the HTTP2 framing layer
```

**原因：** Git 默认使用 HTTP/2，在某些网络环境下会出现协议层错误

**解决方案：** 强制使用 HTTP/1.1
```bash
git -c http.version=HTTP/1.1 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

### 3. 网络传输中断

**问题：**
```
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
error: 13312 bytes of body are still expected
fetch-pack: unexpected disconnect while reading sideband packet
fatal: early EOF
```

**原因：** 网络不稳定或仓库较大导致传输中断

**尝试的解决方案：**

1. **增加 Git 缓冲区大小**
```bash
git -c http.postBuffer=524288000 clone https://github.com/nousresearch/hermes-agent.git
```

2. **使用浅克隆（推荐）**
```bash
git -c http.version=HTTP/1.1 clone --depth 1 https://github.com/nousresearch/hermes-agent.git
```

### 4. 模块导入错误

**问题：**
```
ModuleNotFoundError: No module named 'hermes_cli'
```

**原因：** 使用浅克隆（--depth 1）导致 pyproject.toml 等关键配置文件缺失

**解决方案：** 使用完整克隆而不使用浅克隆
```bash
git -c http.version=HTTP/1.1 clone https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent
```

## 系统要求检查清单

- [x] macOS 操作系统
- [x] uv 包管理器（需要手动安装）
- [x] Python 3.11（通过 uv 安装）
- [x] Git
- [x] Node.js
- [x] ripgrep（通过 Homebrew 安装）
- [x] ffmpeg（通过 Homebrew 安装）

## 完整安装步骤（推荐）

```bash
# 1. 安装 uv
mkdir -p ~/.local/bin
curl -LsSf https://astral.sh/uv/install.sh | sh
export PATH="$HOME/.local/bin:$PATH"

# 2. 克隆 Hermes（使用 HTTP/1.1 和浅克隆）
git -c http.version=HTTP/1.1 clone --depth 1 https://github.com/nousresearch/hermes-agent.git ~/.hermes/hermes-agent

# 3. 进入目录并安装依赖
cd ~/.hermes/hermes-agent
uv sync

# 4. 可选：安装 ripgrep 和 ffmpeg
brew install ripgrep ffmpeg
```

## 持久化 PATH 配置

将以下内容添加到 `~/.zshrc` 或 `~/.bashrc`：
```bash
export PATH="$HOME/.local/bin:$PATH"
```

然后执行：
```bash
source ~/.zshrc  # 或 source ~/.bashrc
```

## 注意事项

1. **网络问题：** 如果遇到 GitHub 访问问题，可能需要配置代理或使用镜像源
2. **HTTP/2 问题：** 在某些网络环境下，强制使用 HTTP/1.1 可以解决连接问题
3. **浅克隆：** 如果不需要完整历史记录，使用 `--depth 1` 可以显著减少下载时间和数据量
4. **Homebrew：** 系统检测到 Homebrew 未安装，需要手动安装以获取 ripgrep 和 ffmpeg

## 安装日期
- 初次尝试：2026-04-17
- 成功安装：2026-04-18

## 最终安装版本
- Hermes Agent: v0.10.0
- Python: 3.11.15
- OpenAI SDK: 2.24.0

## 成功安装的关键步骤
1. 使用 `git -c http.version=HTTP/1.1 clone --depth 1` 避免 HTTP/2 问题
2. 使用浅克隆减少下载时间
3. 通过 `uv sync` 安装 Python 依赖
4. 通过 Homebrew 安装系统依赖（ripgrep、ffmpeg）
