# 🧊 Devbox

**一键为任意项目创建 Docker 开发环境。** 自动检测项目类型、语言版本、包管理器，生成 `docker-compose.dev.yml`，支持 Cloudflare Tunnel 公网暴露。

## ✨ 特性

- 🔍 **智能检测** — 自动识别 Nuxt / Next / Vue / React / Python / Go / Rust / Hugo 等项目类型
- 📦 **版本对齐** — 自动匹配本地 Node / Python / Go / Rust 版本到 Docker 镜像
- 🔗 **pnpm 硬链接** — 自动挂载宿主机 pnpm store，容器内安装无需重复下载
- 🚀 **端口管理** — 自动检测端口冲突，智能分配空闲端口
- 🗄️ **数据库支持** — 一键附加 PostgreSQL / MySQL / Redis / MongoDB / MinIO
- 🌐 **Tunnel 集成** — Cloudflare Tunnel 公网暴露方案

## 快速开始

```bash
# 自动检测项目类型并创建环境
devbox /path/to/your/project

# 指定端口
devbox /path/to/your/project 3001

# 附加数据库
devbox /path/to/your/project 3000 --with postgres,redis

# 强制指定框架
devbox /path/to/your/project 8000 --framework python-fastapi
```

然后：

```bash
cd /path/to/your/project
docker compose -f docker-compose.dev.yml up -d
# 访问 http://localhost:3000
```

## 支持的项目类型

| 类型 | 检测标志 | 默认端口 |
|------|---------|---------|
| Nuxt 3/4 | `nuxt.config.*` | 3000 |
| Next.js | `next.config.*` | 3000 |
| Vue + Vite | `vite.config.*` + vue | 5173 |
| React + Vite | `vite.config.*` + react | 5173 |
| VitePress | `docs/.vitepress/` | 5173 |
| Hugo | `hugo.toml` | 1313 |
| Python FastAPI | `requirements.txt` + fastapi | 8000 |
| Python Django | `manage.py` | 8000 |
| Python Flask | `requirements.txt` + flask | 5000 |
| Go | `go.mod` | 8080 |
| Rust | `Cargo.toml` | 8080 |
| Node.js 后端 | `package.json` | 3000 |
| 静态 HTML | `*.html` | 8080 |
| 通用 Dockerfile | `Dockerfile` | 自动 |

## 附加服务

```bash
# PostgreSQL
devbox ./my-app --with postgres

# MySQL + Redis
devbox ./my-app --with mysql,redis

# MongoDB + MinIO
devbox ./my-app --with mongo,minio
```

| 服务 | 镜像 | 端口 |
|------|------|------|
| `postgres` | `postgres:16-alpine` | 5432 |
| `mysql` | `mysql:8-alpine` | 3306 |
| `redis` | `redis:7-alpine` | 6379 |
| `mongo` | `mongo:7` | 27017 |
| `minio` | `minio/minio` | 9000/9001 |

## 命令行参数

```
devbox <project-path> [port] [options]

Options:
  --framework <type>    强制指定框架（覆盖自动检测）
  --with <services>     附加服务（逗号分隔）
  --env-file <path>     环境变量文件
  --use-dockerfile      使用项目自带 Dockerfile
  -h, --help            帮助信息
```

## 作为 OpenClaw Skill 使用

Devbox 也是一个 [OpenClaw](https://github.com/openclaw/openclaw) Skill，安装后 AI 助手可以自动为你的项目创建 Docker 开发环境。

```bash
# 从 GitHub 安装
clawhub install Nciae-Zyh/devbox
```

## 目录结构

```
devbox/
├── SKILL.md                    # OpenClaw Skill 定义
├── scripts/
│   ├── devbox                  # 主脚本
│   └── create-dev-env.sh       # 兼容旧版调用
└── references/
    ├── docker-templates.md     # 各类项目模板
    └── cloudflare-tunnel.md    # Tunnel 配置指南
```

## License

MIT
