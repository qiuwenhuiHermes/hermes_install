# Hermes WebUI 启动说明

## 方法一：正常安装（需要 sudo 权限）

```bash
npm install -g hermes-web-ui
hermes-web-ui start
```

## 方法二：无 sudo 权限安装

如果遇到权限问题，可以配置 npm 使用用户目录：

```bash
# 1. 配置 npm 使用用户目录
npm config set prefix '~/.npm-global'
npm config set cache '~/.npm-cache'

# 2. 安装
npm install -g hermes-web-ui

# 3. 启动
~/.npm-global/bin/hermes-web-ui start
```

## 访问地址

启动成功后会显示访问地址和 token：

```
http://localhost:8648/#/?token=<your-token>
```

## 日志位置

```
~/.hermes-web-ui/server.log
```

## 常见问题

### EACCES 权限错误

如果遇到 `EACCES: permission denied` 错误，有两种解决方案：

**方案 1：修复 npm 缓存权限（需要 sudo）**
```bash
sudo chown -R 501:20 "~/.npm"
```

**方案 2：使用用户目录（推荐，无需 sudo）**
```bash
npm config set prefix '~/.npm-global'
npm config set cache '~/.npm-cache'
```

### 缓存问题

如果遇到缓存相关错误：
```bash
npm cache clean --force
```

## 停止服务

查找并停止进程：
```bash
ps aux | grep hermes-web-ui
kill <PID>
```
