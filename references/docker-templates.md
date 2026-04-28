# Docker Compose Templates

## Frontend Templates

### Nuxt 3/4

```yaml
services:
  my-nuxt:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/my-nuxt-app:/app
      - my-nuxt-nm:/app/node_modules
      - ~/.local/share/pnpm/store/v3:/root/.local/share/pnpm/store/v3
    ports:
      - "3001:3001"
    command: >
      sh -c "npm install -g pnpm &&
             pnpm install &&
             PORT=3001 pnpm dev --host 0.0.0.0 --port 3001"
    environment:
      - NODE_ENV=development
    restart: unless-stopped

volumes:
  my-nuxt-nm:
```

### Next.js

```yaml
services:
  my-next:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/my-next-app:/app
      - my-next-nm:/app/node_modules
      - my-next-next:/app/.next
    ports:
      - "3001:3001"
    command: >
      sh -c "npm install &&
             PORT=3001 npm run dev -- -H 0.0.0.0 -p 3001"
    environment:
      - NODE_ENV=development
    restart: unless-stopped

volumes:
  my-next-nm:
  my-next-next:
```

### Vue + Vite / React + Vite

```yaml
services:
  my-app:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/my-app:/app
      - my-app-nm:/app/node_modules
    ports:
      - "5173:5173"
    command: >
      sh -c "npm install -g pnpm &&
             pnpm install &&
             pnpm dev --host 0.0.0.0 --port 5173"
    environment:
      - NODE_ENV=development
    restart: unless-stopped

volumes:
  my-app-nm:
```

## Backend Templates

### Python FastAPI

```yaml
services:
  my-api:
    image: python:3.12-slim
    working_dir: /app
    volumes:
      - /WorkSpace/my-api:/app
    ports:
      - "8000:8000"
    command: >
      sh -c "pip install -r requirements.txt &&
             uvicorn main:app --host 0.0.0.0 --port 8000 --reload"
    restart: unless-stopped
```

### Python Django

```yaml
services:
  my-django:
    image: python:3.12-slim
    working_dir: /app
    volumes:
      - /WorkSpace/my-django:/app
    ports:
      - "8000:8000"
    command: >
      sh -c "pip install -r requirements.txt &&
             python manage.py runserver 0.0.0.0:8000"
    restart: unless-stopped
```

### Go

```yaml
services:
  my-go:
    image: golang:1.22
    working_dir: /app
    volumes:
      - /WorkSpace/my-go-app:/app
      - my-go-mod:/go/pkg/mod
      - my-go-cache:/root/.cache/go-build
    ports:
      - "8080:8080"
    command: >
      sh -c "go mod download && go run ."
    restart: unless-stopped

volumes:
  my-go-mod:
  my-go-cache:
```

### Rust

```yaml
services:
  my-rust:
    image: rust:1.77
    working_dir: /app
    volumes:
      - /WorkSpace/my-rust-app:/app
      - my-rust-cargo-registry:/usr/local/cargo/registry
      - my-rust-cargo-git:/usr/local/cargo/git
    ports:
      - "8080:8080"
    command: >
      sh -c "cargo install cargo-watch && cargo watch -x run"
    restart: unless-stopped

volumes:
  my-rust-cargo-registry:
  my-rust-cargo-git:
```

## Static & Docs Templates

### Hugo

```yaml
services:
  my-hugo:
    image: klakegg/hugo:ext-alpine
    working_dir: /src
    volumes:
      - /WorkSpace/my-hugo-site:/src
    ports:
      - "1313:1313"
    command: server --bind 0.0.0.0 --port 1313
    restart: unless-stopped
```

### VitePress

```yaml
services:
  my-docs:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/my-docs:/app
      - my-docs-nm:/app/node_modules
    ports:
      - "5173:5173"
    command: >
      sh -c "npm install -g pnpm &&
             pnpm install &&
             pnpm docs:dev --host 0.0.0.0 --port 5173"
    restart: unless-stopped

volumes:
  my-docs-nm:
```

### Static HTML (nginx)

```yaml
services:
  my-static:
    image: nginx:alpine
    volumes:
      - /WorkSpace/my-site:/usr/share/nginx/html:ro
    ports:
      - "8080:80"
    restart: unless-stopped
```

## Database Services

### PostgreSQL + Redis (与应用组合)

```yaml
services:
  my-app:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/my-app:/app
      - my-app-nm:/app/node_modules
    ports:
      - "3000:3000"
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
    environment:
      - DATABASE_URL=postgresql://devbox:devbox@postgres:5432/devbox
      - REDIS_URL=redis://redis:6379
    restart: unless-stopped

  postgres:
    image: postgres:16-alpine
    environment:
      - POSTGRES_USER=devbox
      - POSTGRES_PASSWORD=devbox
      - POSTGRES_DB=devbox
    ports:
      - "5432:5432"
    volumes:
      - pg-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U devbox"]
      interval: 5s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    restart: unless-stopped

volumes:
  my-app-nm:
  pg-data:
```

### MySQL + MinIO (与应用组合)

```yaml
services:
  my-app:
    image: python:3.12-slim
    working_dir: /app
    volumes:
      - /WorkSpace/my-app:/app
    ports:
      - "8000:8000"
    depends_on:
      mysql:
        condition: service_healthy
    environment:
      - DATABASE_URL=mysql://devbox:devbox@mysql:3306/devbox
      - MINIO_ENDPOINT=http://minio:9000
    restart: unless-stopped

  mysql:
    image: mysql:8-alpine
    environment:
      - MYSQL_ROOT_PASSWORD=devbox
      - MYSQL_DATABASE=devbox
      - MYSQL_USER=devbox
      - MYSQL_PASSWORD=devbox
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  minio:
    image: minio/minio
    command: server /data --console-address ":9001"
    environment:
      - MINIO_ROOT_USER=devbox
      - MINIO_ROOT_PASSWORD=devbox123
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - minio-data:/data
    restart: unless-stopped

volumes:
  mysql-data:
  minio-data:
```

## Multi-Project Compose

```yaml
services:
  cloudflared:
    image: cloudflare/cloudflared:latest
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=<your-token>
    restart: unless-stopped
    network_mode: host

  project-a:
    image: node:24-bookworm
    working_dir: /app
    volumes:
      - /WorkSpace/project-a:/app
      - project-a-nm:/app/node_modules
    ports:
      - "3001:3001"
    command: sh -c "npm install -g pnpm && pnpm install && pnpm dev --host 0.0.0.0 --port 3001"
    restart: unless-stopped

  project-b:
    image: python:3.12-slim
    working_dir: /app
    volumes:
      - /WorkSpace/project-b:/app
    ports:
      - "8000:8000"
    command: sh -c "pip install -r requirements.txt && uvicorn main:app --host 0.0.0.0 --port 8000 --reload"
    restart: unless-stopped

volumes:
  project-a-nm:
```
