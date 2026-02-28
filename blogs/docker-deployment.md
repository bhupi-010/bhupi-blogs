---
title: "Docker + Prisma: Complete Deployment Guide"
description: "Step-by-step guide to setting up Docker containers for your Node.js app with Prisma ORM, PostgreSQL, and production-ready configuration."
date: "2026-02-25"
tags: ["docker", "prisma", "devops", "postgresql"]
cover: "https://images.unsplash.com/photo-1605745341112-85968b19335b?w=1200&h=630&fit=crop"
---

# Docker + Prisma: Complete Deployment Guide

Containerizing your application with Docker ensures consistent environments from development to production. In this guide, we'll set up a production-ready Docker configuration for a Node.js application using Prisma ORM and PostgreSQL.

## Prerequisites

- Docker and Docker Compose installed
- A Node.js project with Prisma configured
- Basic understanding of containers

## Project Structure

```
my-app/
├── prisma/
│   └── schema.prisma
├── src/
│   └── index.ts
├── Dockerfile
├── docker-compose.yml
├── .dockerignore
└── package.json
```

## The Dockerfile

We'll use a multi-stage build to keep the final image small:

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder

WORKDIR /app
COPY package*.json ./
COPY prisma ./prisma/

RUN npm ci
RUN npx prisma generate

COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine AS runner

WORKDIR /app

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 appuser

COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/package*.json ./

USER appuser

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

## Docker Compose

```yaml
version: '3.8'

services:
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myuser -d mydb"]
      interval: 5s
      timeout: 5s
      retries: 5

  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      DATABASE_URL: postgresql://myuser:mypassword@db:5432/mydb
      NODE_ENV: production
    depends_on:
      db:
        condition: service_healthy

volumes:
  postgres_data:
```

## Running Migrations

After the containers are up, run migrations:

```bash
# Run migrations inside the container
docker compose exec app npx prisma migrate deploy

# Or seed your database
docker compose exec app npx prisma db seed
```

## Production Tips

1. **Always use `npm ci`** instead of `npm install` in Docker — it's faster and more reliable.
2. **Multi-stage builds** reduce image size by 60-80%.
3. **Health checks** ensure containers are truly ready before accepting traffic.
4. **Use `.dockerignore`** to exclude `node_modules`, `.git`, and other unnecessary files.
5. **Never store secrets** in Dockerfiles — use environment variables or secrets managers.

## Common Pitfalls

- **Prisma Client generation**: Always run `prisma generate` during the build stage
- **Database connection timing**: Use `depends_on` with `condition: service_healthy`
- **Volume permissions**: Run as a non-root user for security

## Conclusion

Docker and Prisma work beautifully together when configured properly. This setup gives you a reproducible, secure, and efficient deployment pipeline.

The key is to **keep images small**, **use health checks**, and **manage secrets properly**. Happy containerizing! 🐳
