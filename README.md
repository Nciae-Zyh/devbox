# 🧊 Devbox

**一键为任意项目创建 Docker 开发环境。** 自动检测项目类型、语言版本、包管理器，生成 `docker-compose.dev.yml`。

## ✨ 特性

- 🔍 **智能检测** — 自动识别 20+ 项目类型（Nuxt / Next / Vue / React / Python / Go / Rust / Java / Ruby / PHP / C# / Swift…）
- 📦 **版本对齐** — 自动匹配本地 Node / Python / Go / Rust / Java / Ruby / PHP / .NET / Swift 版本到 Docker 镜像
- 🔗 **pnpm 硬链接** — 自动挂载宿主机 pnpm store，容器内安装无需重复下载
- 🚀 **端口管理** — 自动检测端口冲突，智能分配空闲端口
- 🗄️ **数据库支持** — 一键附加 PostgreSQL / MySQL / Redis / MongoDB / MinIO

## 快速开始

```bash
# 自动检测项目类型
devbox /path/to/your/project

# 指定端口 + 数据库
devbox /path/to/your/project 8000 --with postgres,redis

# 强制指定框架
devbox /path/to/your/project --framework java-spring
```

然后：
```bash
cd /path/to/your/project
docker compose -f docker-compose.dev.yml up -d
```

## 支持的项目类型

| 类型 | 检测标志 | 默认端口 | Docker 镜像 |
|------|---------|---------|------------|
| Nuxt 3/4 | `nuxt.config.*` | 3000 | `node:{ver}-bookworm` |
| Next.js | `next.config.*` | 3000 | `node:{ver}-bookworm` |
| Vue + Vite | `vite.config.*` + vue | 5173 | `node:{ver}-bookworm` |
| React + Vite | `vite.config.*` + react | 5173 | `node:{ver}-bookworm` |
| Svelte | `svelte.config.*` | 5173 | `node:{ver}-bookworm` |
| VitePress | `docs/.vitepress/` | 5173 | `node:{ver}-bookworm` |
| Node.js 后端 | `package.json` | 3000 | `node:{ver}-bookworm` |
| Python FastAPI | `requirements.txt` + fastapi | 8000 | `python:{ver}-slim` |
| Python Django | `manage.py` | 8000 | `python:{ver}-slim` |
| Python Flask | `requirements.txt` + flask | 5000 | `python:{ver}-slim` |
| Go | `go.mod` | 8080 | `golang:{ver}` |
| Rust | `Cargo.toml` | 8080 | `rust:{ver}` |
| Java (Spring Boot) | `pom.xml` + spring-boot | 8080 | `eclipse-temurin:{ver}-jdk` |
| Java (Maven) | `pom.xml` | 8080 | `eclipse-temurin:{ver}-jdk` |
| Java (Gradle) | `build.gradle` / `.kts` | 8080 | `eclipse-temurin:{ver}-jdk` |
| Kotlin | `*.kt` | 8080 | `eclipse-temurin:21-jdk` |
| Ruby on Rails | `Gemfile` + rails | 3000 | `ruby:{ver}-slim` |
| Ruby Sinatra | `Gemfile` | 4567 | `ruby:{ver}-slim` |
| PHP Laravel | `composer.json` + laravel | 5000 | `php:{ver}-cli` |
| PHP | `composer.json` | 8000 | `php:{ver}-cli` |
| C# / .NET | `*.csproj` / `*.sln` | 5000 | `dotnet/sdk:{ver}` |
| Swift | `Package.swift` | 8080 | `swift:{ver}` |
| Hugo | `hugo.toml` | 1313 | `klakegg/hugo:ext-alpine` |
| 静态 HTML | `*.html` | 80 | `nginx:alpine` |

## 版本自动检测

Devbox 读取你本地的编译器/运行时版本，匹配对应 Docker 镜像：

| 语言 | 检测命令 | 镜像示例 |
|------|---------|---------|
| Node.js | `node --version` | `node:22-bookworm` |
| Python | `python3 --version` | `python:3.12-slim` |
| Go | `go version` | `golang:1.22` |
| Rust | `rustc --version` | `rust:1.77` |
| Java | `java -version` | `eclipse-temurin:21-jdk-jammy` |
| Ruby | `ruby --version` | `ruby:3.3-slim` |
| PHP | `php --version` | `php:8.3-cli` |
| .NET | `dotnet --version` | `dotnet/sdk:8.0` |
| Swift | `swift --version` | `swift:5.10` |

## 附加服务

```bash
devbox ./my-app --with postgres,redis
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

## 作为 OpenClaw Skill

```bash
clawhub install Nciae-Zyh/devbox
```

## License

MIT
