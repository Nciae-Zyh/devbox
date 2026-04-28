---
name: project-dev
description: "使用 Docker 为任意类型项目创建隔离开发环境。支持 20+ 项目类型：前端(Nuxt/Next/Vue/React/Svelte/VitePress)、后端(Python/Go/Rust/Java/Node.js/Kotlin/Ruby/PHP/C#/.Swift)、静态站点(Hugo/HTML)。自动检测项目类型、语言版本、包管理器、端口冲突。支持附加数据库(PostgreSQL/MySQL/Redis/MongoDB/MinIO)。Use when: 用户要运行任何项目的容器化开发环境、需要数据库等依赖服务、为项目创建实时预览。"
---

# 🧊 Devbox — One-Command Docker Dev Environment

为任意项目创建隔离 Docker 开发环境。

## Quick Start

```bash
# 自动检测
devbox /path/to/project

# 指定端口 + 附加数据库
devbox /path/to/project 8000 --with postgres,redis

# 强制指定框架
devbox /path/to/project --framework java-spring
```

## 支持的项目类型

| 类型 | 检测标志 | 默认端口 | Docker 镜像 |
|------|---------|---------|------------|
| **Nuxt 3/4** | `nuxt.config.*` | 3000 | `node:{ver}-bookworm` |
| **Next.js** | `next.config.*` | 3000 | `node:{ver}-bookworm` |
| **Vue + Vite** | `vite.config.*` + vue | 5173 | `node:{ver}-bookworm` |
| **React + Vite** | `vite.config.*` + react | 5173 | `node:{ver}-bookworm` |
| **Svelte** | `svelte.config.*` | 5173 | `node:{ver}-bookworm` |
| **VitePress** | `docs/.vitepress/` | 5173 | `node:{ver}-bookworm` |
| **Node.js 后端** | `package.json` | 3000 | `node:{ver}-bookworm` |
| **Python FastAPI** | `requirements.txt` + fastapi | 8000 | `python:{ver}-slim` |
| **Python Django** | `manage.py` | 8000 | `python:{ver}-slim` |
| **Python Flask** | `requirements.txt` + flask | 5000 | `python:{ver}-slim` |
| **Go** | `go.mod` | 8080 | `golang:{ver}` |
| **Rust** | `Cargo.toml` | 8080 | `rust:{ver}` |
| **Java (Spring Boot)** | `pom.xml` + spring-boot | 8080 | `eclipse-temurin:{ver}-jdk-jammy` |
| **Java (Maven)** | `pom.xml` | 8080 | `eclipse-temurin:{ver}-jdk-jammy` |
| **Java (Gradle)** | `build.gradle` / `.kts` | 8080 | `eclipse-temurin:{ver}-jdk-jammy` |
| **Kotlin** | `*.kt` (无 build.gradle) | 8080 | `eclipse-temurin:21-jdk-jammy` |
| **Ruby on Rails** | `Gemfile` + rails | 3000 | `ruby:{ver}-slim` |
| **Ruby Sinatra** | `Gemfile` | 4567 | `ruby:{ver}-slim` |
| **PHP Laravel** | `composer.json` + laravel | 5000 | `php:{ver}-cli` |
| **PHP** | `composer.json` | 8000 | `php:{ver}-cli` |
| **C# / .NET** | `*.csproj` / `*.sln` | 5000 | `dotnet/sdk:{ver}` |
| **Swift** | `Package.swift` | 8080 | `swift:{ver}` |
| **Hugo** | `hugo.toml` | 1313 | `klakegg/hugo:ext-alpine` |
| **静态 HTML** | `*.html` | 80 | `nginx:alpine` |

## 附加服务

```bash
devbox ./app --with postgres,redis
```

| 服务 | 镜像 | 端口 |
|------|------|------|
| `postgres` / `pg` | `postgres:16-alpine` | 5432 |
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

## 版本检测

Devbox 自动检测本地语言版本并匹配 Docker 镜像：

| 语言 | 检测方式 | Docker 镜像 |
|------|---------|------------|
| Node.js | `node --version` | `node:{major}-bookworm` |
| Python | `python3 --version` | `python:{major.minor}-slim` |
| Go | `go version` | `golang:{major.minor}` |
| Rust | `rustc --version` | `rust:{major.minor}` |
| Java | `java -version` | `eclipse-temurin:{major}-jdk-jammy` |
| Ruby | `ruby --version` | `ruby:{major.minor}-slim` |
| PHP | `php --version` | `php:{major.minor}-cli` |
| .NET | `dotnet --version` | `dotnet/sdk:{major.minor}` |
| Swift | `swift --version` | `swift:{major.minor}` |

## pnpm 硬链接

前端项目使用 pnpm 时，自动挂载宿主机 pnpm store，容器内安装直接硬链接，无需重复下载。

## Cloudflare Tunnel

详见 `references/cloudflare-tunnel.md`。

## 模板参考

详见 `references/docker-templates.md`。
