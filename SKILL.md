---
name: "Devbox"
slug: docker-devbox
version: 1.2.0
homepage: https://github.com/Nciae-Zyh/devbox
changelog: "Fix packaging: include devbox.sh script, remove tunnel references, declare all binaries"
metadata: {"clawdbot":{"emoji":"🧊","requires":{"bins":["docker"]},"os":["linux","darwin"],"files":["scripts/*","references/*"]}}
description: "One-command Docker dev environment for any project. Auto-detects 20+ types (Nuxt/Next/Vite/Python/Go/Rust/Java/Ruby/PHP/C#/Swift/Hugo), matches local language versions to Docker images, manages port conflicts, and attaches database services (PostgreSQL/MySQL/Redis/MongoDB/MinIO). The bundled scripts/devbox.sh script generates docker-compose.dev.yml. Use when: setting up containerized dev environments, running projects in isolated Docker containers, or needing database dependencies."
---

# Devbox — One-Command Docker Dev Environment

Create isolated Docker dev environments for any project with a single command.

## How It Works

The `scripts/devbox.sh` script is bundled with this skill. It:
1. Auto-detects your project type (framework, language, package manager)
2. Reads your local language version (Node, Python, Go, Rust, Java, etc.)
3. Generates a `docker-compose.dev.yml` with the matching Docker image
4. Optionally attaches database services (PostgreSQL, MySQL, Redis, MongoDB, MinIO)

## Usage

After installing this skill, run the bundled script directly:

```bash
# Find where the skill is installed
SKILL_DIR=$(clawhub list --json 2>/dev/null | python3 -c "import sys,json; print([s['folder'] for s in json.load(sys.stdin) if s.get('slug')=='docker-devbox'][0])" 2>/dev/null || echo "skills/docker-devbox")

# Run devbox
bash "$SKILL_DIR/scripts/devbox.sh" /path/to/project

# Or copy to PATH for convenience
cp "$SKILL_DIR/scripts/devbox.sh" /usr/local/bin/devbox
chmod +x /usr/local/bin/devbox
devbox /path/to/project
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

## Dependencies

**Required:**
- `docker` + `docker compose` — the only hard requirement

**Optional (for version detection):**
- `node` — Node.js version matching
- `python3` — Python version matching
- `go` — Go version matching
- `rustc` — Rust version matching
- `java` — Java version matching
- `ruby` — Ruby version matching
- `php` — PHP version matching
- `dotnet` — .NET version matching
- `swift` — Swift version matching
- `pnpm` / `yarn` / `bun` — Node.js package manager detection

Only the runtimes installed on your host are used. If a language is not installed, the script falls back to a sensible default version.

## Included Files

```
scripts/devbox.sh          — Main script (auto-detect + generate compose)
scripts/create-dev-env.sh  — Backward-compatible wrapper
references/docker-templates.md — Ready-to-use compose templates
```

## Docker Templates

See `references/docker-templates.md` for ready-to-use compose templates for each project type.
