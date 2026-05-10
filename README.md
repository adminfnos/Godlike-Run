# Godlike 自动续期脚本

# ⭐ 觉得有用？给个 Star 支持一下！
> 注册地址：[https://godlike.host/cart-free](https://godlike.host/cart-free)  
> ⚠️忘记密码？在此重置：[https://panel.godlike.host/auth/password](https://panel.godlike.host/auth/password)

自动为 **Godlike Host** 免费 Minecraft 服务器续期 90 分钟的 GitHub Actions 脚本。支持多账号、Cookie / 密码自动切换、Cookie 自动回写、多种代理协议，并通过 Telegram 发送带截图的成功/失败通知。

## ✨ 功能特性

- ✅ 多账号支持（最多 5 个）
- ✅ 智能登录：优先使用持久化 Cookie，失效自动回退至 Playwright 模拟登录
- ✅ Cookie 自动维护：密码登录成功后自动将 Cookie 加密回写至 GitHub Secrets，下次可直接复用
- ✅ 续期保护：检测服务器是否已过期，过期则跳过续期并通知
- ✅ 支持多种代理协议（VLESS / VMess / Trojan / Shadowsocks / SOCKS5）
- ✅ Telegram 通知带截图（成功 / 失败均有完整页面截图）
- ✅ 工作流日志脱敏，TG 通知显示真实账号/服务器信息
- ✅ 错误快照自动上传至 Artifacts 便于调试


## 📋 前置要求

### 1. GitHub Secrets 配置

在仓库 `Settings` → `Secrets and variables` → `Actions` 中添加以下 Secrets：

| Secret 名称 | 必填 | 说明 | 示例 |
|------------|:---:|------|------|
| `GODLIKE_1` | ✅ | 主账号，格式见下方 | `admin@example.com-----MyPassword` |
| `GODLIKE_2` ~ `GODLIKE_5` | ❌ | 额外账号（有多个账号时填写） | 同上 |
| `TG_BOT_TOKEN` | ❌ | Telegram Bot Token | `1234567890:AAE...` |
| `TG_CHAT_ID` | ❌ | 接收通知的 Chat ID | `123456789` |
| `REPO_TOKEN` | ❌ | 用于自动回写 Cookie 的 GitHub PAT | `ghp_xxxxxxxxxxxx` |
| `PROXY_NODE` | ❌ | 代理节点链接 | 见下方代理格式 |

### 2. GODLIKE 账号格式

每个 Secret 的格式为：`用户名/邮箱-----密码`

```
admin@example.com-----MyP@ssw0rd
myusername-----AnotherPassword
```

首次运行时只需提供用户名和密码。脚本会在登录成功后自动将 Cookie 追加到末尾，变为：
```
admin@example.com-----MyP@ssw0rd-----base64编码的cookie列表
```

后续运行将优先使用 Cookie 登录，减少浏览器开销。

### 3. 代理节点格式（可选）

支持以下代理协议，留空则使用直连：

| 协议 | 示例 |
|------|------|
| VLESS | `vless://uuid@host:port?type=ws&security=tls&sni=example.com` |
| VMess | `vmess://eyJhZGQiOiIxLjIuMy40IiwidiI6IjIiLCJwc...` |
| Trojan | `trojan://password@host:port?type=ws&sni=example.com` |
| Shadowsocks | `ss://YWVzLTI1Ni1nY206cGFzc3dvcmQ=@host:port` |
| SOCKS5 | `socks5://user:pass@host:port` 或 `socks5://host:port` |



## 🚀 使用方法

### 方法 1：定时自动运行

Fork 仓库并配置 Secrets 后，工作流默认每 3 小时运行一次（UTC 时间），自动完成续期。

如果需要修改频率，编辑 `.github/workflows/godlike.yml`：

```yaml
schedule:
  - cron: '0 */3 * * *'      # 每3小时
```
cron-job.org 配置:
```
https://api.github.com/repos/你的用户名/仓库名/dispatches
```
Accept: application/vnd.github.v3+json

Authorization: Bearer <你的TOKEN>

Body 填写:
```
{"event_type": "renew_event"}
```



一行版：
```bash
curl -s -X POST "https://api.github.com/repos/你的用户名/你的仓库名/actions/workflows/Godlike_Renew.yml/dispatches" -H "Accept: application/vnd.github+json" -H "Authorization: Bearer ghp_xxxxxxxxxxxxxxxxxxxx" -d '{"ref":"main"}'
```



**⚠️ 免责声明**：本脚本仅供学习交流使用，使用者需遵守 Godlike Host 的服务条款。因使用本脚本造成的任何问题，作者不承担任何责任。
