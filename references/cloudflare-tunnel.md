# Cloudflare Tunnel Configuration

## Option 1: Docker 内运行 cloudflared（推荐）

在 docker-compose 中同时运行项目和 cloudflared：

```yaml
services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=<从 Cloudflare 获取>
    restart: unless-stopped
    network_mode: host

  your-project:
    image: node:24-bookworm
    # ... 项目配置
    ports:
      - "3001:3001"
```

### 获取 Tunnel Token

1. 登录 https://one.dash.cloudflare.com
2. Networks → Tunnels → Create a tunnel
3. 选择 Docker 环境
4. 复制 token

### 配置路由

在 Cloudflare 控制台中为每个项目添加：
- Service: `http://localhost:3001`
- Hostname: `project-a.your-domain.com`

## Option 2: 宿主机运行 cloudflared

```bash
# 安装
brew install cloudflare/cloudflare/cloudflared  # macOS

# 登录
cloudflared tunnel login

# 创建 tunnel
cloudflared tunnel create my-dev-tunnel

# 配置 config.yml
cat > ~/.cloudflared/config.yml << EOF
tunnel: <tunnel-id>
credentials-file: ~/.cloudflared/<tunnel-id>.json

ingress:
  - hostname: project-a.your-domain.com
    service: http://localhost:3001
  - hostname: project-b.your-domain.com
    service: http://localhost:8000
  - service: http_status:404
EOF

# 启动
cloudflared tunnel run my-dev-tunnel
```

## Option 3: 一个 Tunnel Token 管理多项目

在 Cloudflare 控制台中，一个 tunnel 可以配置多个 ingress 规则：

```
project-a.your.com → http://localhost:3001
project-b.your.com → http://localhost:8000
project-c.your.com → http://localhost:5173
*.your.com         → http_status:404
```

## DNS 配置

每个子域名需要 CNAME 到 tunnel：
```
project-a.your.com  CNAME  <tunnel-id>.cfargotunnel.com
```

Cloudflare 控制台自动处理，无需手动添加。

## 安全建议

- 启用 Cloudflare Access 保护开发环境
- 设置 IP 白名单或邮箱验证
- 使用 `--no-autoupdate` 避免意外更新
- 定期轮换 tunnel token
