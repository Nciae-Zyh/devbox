---
name: "Devbox"
slug: docker-devbox
version: 1.1.0
homepage: https://github.com/Nciae-Zyh/devbox
changelog: "Fix security review: include devbox script, declare all binaries, English docs, proper install spec"
metadata: {"clawdbot":{"emoji":"🧊","requires":{"bins":["docker"]},"os":["linux","darwin"],"primaryEnv":"","files":["scripts/*","references/*"]}}
description: "One-command Docker dev environment for any project. Auto-detects 20+ types (Nuxt/Next/Vite/Python/Go/Rust/Java/Ruby/PHP/C#/Swift/Hugo), matches local language versions to Docker images, manages port conflicts, and attaches database services (PostgreSQL/MySQL/Redis/MongoDB/MinIO). The bundled scripts/devbox script generates docker-compose.dev.yml. Use when: setting up containerized dev environments, running projects in isolated Docker containers, needing database dependencies, or exposing dev servers via Cloudflare Tunnel."
---

# Devbox — One-Command Docker Dev Environment

Create isolated Docker dev environments for any project with a single command.

## Installation

The `devbox` script is bundled in this skill at `scripts/devbox`. After installing this skill:

```bash
# Option 1: Run directly via the skill directory
bash $(clawhub list --json 2>/dev/null | grep docker-devbox | jq -r .folder)/scripts/devbox /path/to/project

# Option 2: Copy to PATH (recommended)
cp scripts/devbox /usr/local/bin/devbox
chmod +x /usr/local/bin/devbox
devbox /path/to/project

# Option 3: Use the wrapper script (backward compatible)
bash scripts/create-dev-env.sh /path/to/project 3000
```

## Quick Start

```bash
# Auto-detect project type
devbox /path/to/project

# Specify port + attach databases
devbox /path/to/project 8000 --with postgres,redis

# Force framework
devbox /path/to/project --framework java-spring
```

## Supported Project Types

| Type | Detection | Default Port | Docker Image |
|------|-----------|-------------|--------------|
| **Nuxt 3/4** | `nuxt.config.*` | 3000 | `node:{ver}-bookworm` |
| **Next.js** | `next.config.*` | 3000 | `node:{ver}-bookworm` |
| **Vue + Vite** | `vite.config.*` + vue | 5173 | `node:{ver}-bookworm` |
| **React + Vite** | `vite.config.*` + react | 5173 | `node:{ver}-bookworm` |
| **Svelte** | `svelte.config.*` | 5173 | `node:{ver}-bookworm` |
| **VitePress** | `docs/.vitepress/` | 5173 | `node:{ver}-bookworm` |
| **Node.js backend** | `package.json` | 3000 | `node:{ver}-bookworm` |
| **Python FastAPI** | `requirements.txt` + fastapi | 8000 | `python:{ver}-slim` |
| **Python Django** | `manage.py` | 8000 | `python:{ver}-slim` |
| **Python Flask** | `requirements.txt` + flask | 5000 | `python:{ver}-slim` |
| **Go** | `go.mod` | 8080 | `golang:{ver}` |
| **Rust** | `Cargo.toml` | 8080 | `rust:{ver}` |
| **Java (Spring Boot)** | `pom.xml` + spring-boot | 8080 | `eclipse-temurin:{ver}-jdk` |
| **Java (Maven)** | `pom.xml` | 8080 | `eclipse-temurin:{ver}-jdk` |
| **Java (Gradle)** | `build.gradle` / `.kts` | 8080 | `eclipse-temurin:{ver}-jdk` |
| **Kotlin** | `*.kt` (no build.gradle) | 8080 | `eclipse-temurin:21-jdk` |
| **Ruby on Rails** | `Gemfile` + rails | 3000 | `ruby:{ver}-slim` |
| **Ruby Sinatra** | `Gemfile` | 4567 | `ruby:{ver}-slim` |
| **PHP Laravel** | `composer.json` + laravel | 5000 | `php:{ver}-cli` |
| **PHP** | `composer.json` | 8000 | `php:{ver}-cli` |
| **C# / .NET** | `*.csproj` / `*.sln` | 5000 | `dotnet/sdk:{ver}` |
| **Swift** | `Package.swift` | 8080 | `swift:{ver}` |
| **Hugo** | `hugo.toml` | 1313 | `klakegg/hugo:ext-alpine` |
| **Static HTML** | `*.html` | 80 | `nginx:alpine` |

## Attach Database Services

```bash
devbox ./app --with postgres,redis
```

| Service | Image | Port |
|---------|-------|------|
| `postgres` / `pg` | `postgres:16-alpine` | 5432 |
| `mysql` | `mysql:8-alpine` | 3306 |
| `redis` | `redis:7-alpine` | 6379 |
| `mongo` | `mongo:7` | 27017 |
| `minio` | `minio/minio` | 9000/9001 |

## CLI Options

```
devbox <project-path> [port] [options]

Options:
  --framework <type>    Override auto-detection
  --with <services>     Comma-separated services to attach
  --env-file <path>     Environment variable file
  --use-dockerfile      Use project's existing Dockerfile
  -h, --help            Show help
```

## Version Detection

Devbox reads your local compiler/runtime version and matches the Docker image:

| Language | Detection | Docker Image |
|----------|-----------|--------------|
| Node.js | `node --version` | `node:{major}-bookworm` |
| Python | `python3 --version` | `python:{major.minor}-slim` |
| Go | `go version` | `golang:{major.minor}` |
| Rust | `rustc --version` | `rust:{major.minor}` |
| Java | `java -version` | `eclipse-temurin:{major}-jdk-jammy` |
| Ruby | `ruby --version` | `ruby:{major.minor}-slim` |
| PHP | `php --version` | `php:{major.minor}-cli` |
| .NET | `dotnet --version` | `dotnet/sdk:{major.minor}` |
| Swift | `swift --version` | `swift:{major.minor}` |

**Note:** Language runtimes are optional dependencies — only the ones installed on your host are used for version detection. Docker is the only required binary.

## pnpm Hard-Link Optimization

For pnpm projects, the host pnpm store is mounted into the container for hard-linked installs — no duplicate downloads needed.

## Cloudflare Tunnel

See `references/cloudflare-tunnel.md` for exposing dev servers to the public internet.

## Docker Templates

See `references/docker-templates.md` for ready-to-use compose templates.
