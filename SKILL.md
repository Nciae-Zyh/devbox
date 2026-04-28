---
name: project-dev
description: "使用 Docker 为任意类型项目创建隔离开发环境并通过 Cloudflare Tunnel 暴露。支持：前端(Nuxt/Next/Vue/React/VitePress)、后端(Python/Go/Rust/Node.js)、全栈、静态站点(Hugo)、数据库服务(PostgreSQL/MySQL/Redis/MongoDB)、通用 Dockerfile 项目。Use when: 用户要运行任何项目的容器化开发环境、通过 Cloudflare Tunnel 暴露开发服务器到公网、为项目创建实时预览、需要数据库等依赖服务。自动生成 docker-compose.dev.yml，自动检测项目类型、语言版本、包管理器、端口冲突。"
---

# Project Dev Environment

为任意项目创建隔离 Docker 开发环境，暴露 via Cloudflare Tunnel。

## Architecture

```
宿主机（Agent 编辑代码）
    ↓ volume mount
Docker 容器（dev server / app）
    ↓ localhost:PORT
Cloudflare Tunnel（可选）
    ↓
公网 URL（project.your-domain.com）
```

## Quick Start

### 1. 自动检测并创建环境

```bash
scripts/create-dev-env.sh <项目路径> [端口]
```

脚本自动检测项目类型、语言版本、包管理器，生成 `docker-compose.dev.yml`。

### 2. 启动

```bash
cd /path/to/project
docker compose -f docker-compose.dev.yml up -d
```

### 3. 查看日志

```bash
docker compose -f docker-compose.dev.yml logs -f
```

## 支持的项目类型

| 类型 | 检测标志 | 默认端口 | 说明 |
|------|---------|---------|------|
| **Nuxt 3/4** | `nuxt.config.*` | 3000 | `pnpm dev`，支持 pnpm 硬链接 |
| **Next.js** | `next.config.*` | 3000 | `npm run dev`，保留 `.next` 缓存 |
| **Vue + Vite** | `vite.config.*` + vue | 5173 | `pnpm dev` |
| **React + Vite** | `vite.config.*` + react | 5173 | `pnpm dev` |
| **VitePress** | `docs/.vitepress/` | 5173 | 静态文档站点 |
| **Hugo** | `hugo.toml`/`config.toml` | 1313 | Go 静态站点生成器 |
| **Python FastAPI** | `requirements.txt` + fastapi | 8000 | `uvicorn` |
| **Python Django** | `manage.py` | 8000 | `python manage.py runserver` |
| **Python Flask** | `requirements.txt` + flask | 5000 | `flask run` |
| **Go** | `go.mod` | 8080 | `go run .` |
| **Rust** | `Cargo.toml` | 8080 | `cargo watch -x run` |
| **Node.js 后端** | `package.json` (无前端框架) | 3000 | `node server.js` / `npm start` |
| **通用 Dockerfile** | `Dockerfile` | auto | 直接 build & run |
| **静态文件** | 仅 HTML/CSS/JS | 8080 | nginx 服务 |

### 附加服务

通过 `--with-db` 或 `--with` 参数附加数据库：

```bash
scripts/create-dev-env.sh /WorkSpace/my-app 3000 --with postgres,redis
```

| 服务 | 镜像 | 默认端口 | 说明 |
|------|------|---------|------|
| `postgres` | `postgres:16-alpine` | 5432 | PostgreSQL |
| `mysql` | `mysql:8-alpine` | 3306 | MySQL |
| `redis` | `redis:7-alpine` | 6379 | Redis |
| `mongo` | `mongo:7` | 27017 | MongoDB |
| `minio` | `minio/minio` | 9000 | S3 兼容存储 |

## 命令速查

| 命令 | 说明 |
|------|------|
| `scripts/create-dev-env.sh <path> [port]` | 自动检测并创建环境 |
| `scripts/create-dev-env.sh <path> [port] --with postgres,redis` | 附加数据库服务 |
| `scripts/create-dev-env.sh <path> [port] --framework nuxt` | 强制指定框架 |
| `docker compose -f docker-compose.dev.yml up -d` | 启动 |
| `docker compose -f docker-compose.dev.yml down` | 停止 |
| `docker compose -f docker-compose.dev.yml down -v` | 停止并删除 volumes |
| `docker compose -f docker-compose.dev.yml logs -f` | 查看日志 |
| `docker compose -f docker-compose.dev.yml restart` | 重启 |

## 高级用法

### 手动指定框架

当自动检测不准确时，用 `--framework` 强制指定：

```bash
scripts/create-dev-env.sh /WorkSpace/my-app 3000 --framework python-fastapi
```

### 多服务项目

一个 compose 文件中同时运行前后端：

```bash
scripts/create-dev-env.sh /WorkSpace/fullstack-app 3000 --framework next --with postgres,redis
```

### 自定义 Dockerfile

如果项目根目录有 `Dockerfile`，脚本会优先使用它：

```bash
scripts/create-dev-env.sh /WorkSpace/my-app 3000 --use-dockerfile
```

## pnpm 硬链接优化

前端项目使用 pnpm 时，自动挂载宿主机 pnpm store：

```yaml
volumes:
  - ~/.local/share/pnpm/store/v3:/root/.local/share/pnpm/store/v3
```

容器内 `pnpm install` 直接硬链接，无需重复下载。前提是 Node.js 主版本号一致。

## 环境变量

支持 `.env` 文件注入：

```bash
# 自动检测并注入 .env / .env.local / .env.development
scripts/create-dev-env.sh /WorkSpace/my-app 3000 --env-file .env.local
```

## Cloudflare Tunnel

详见 `references/cloudflare-tunnel.md`。

## 模板参考

详见 `references/docker-templates.md`。

## 注意事项

- 容器内 `node_modules` / `vendor` / `target` 不从宿主机挂载，用 named volume
- `.env` 文件用 `env_file` 注入，不要写进 compose
- 端口映射避免冲突（脚本自动检测）
- 脚本自动更新 `.gitignore`，防止 compose 文件泄漏
- 数据库数据用 named volume，`down -v` 清除
