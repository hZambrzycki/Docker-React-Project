# 🚢 Docker React Project

A production-friendly **React** setup containerized with **Docker**. It includes a separate Dockerfile for development (hot reload) and a production image to serve static assets.

> Goal: run the same app locally and in production with minimal friction.

---

## ✨ Features

- **Dev container**: fast iteration with hot reload (`Dockerfile.dev` + `docker-compose.yml`).
- **Production image**: builds optimized static files and serves them (commonly via Nginx).
- **Clean context**: `.dockerignore` to keep images small and builds fast.
- **Zero global installs**: Node/Nginx live inside containers.

---

## 🗂️ Project layout

├─ Dockerfile
├─ Dockerfile.dev
├─ docker-compose.yml
├─ .dockerignore
├─ package.json
├─ public/
└─ src/

> Languages (as detected by GitHub): JavaScript ~39.5%, HTML ~36.6%, CSS ~19.8%, Dockerfile ~4.1%.

## 🚀 Quick start

### 1) Run in development (with Compose)
```bash
docker compose up --build
```
# Then open http://localhost:3000  (or the port declared in docker-compose.yml)

Typical dev pattern:

Mount local source as a volume

Use a Node base image

Start the dev server (Vite/CRA) with hot reload

2) Build a production image
# Build
```
docker build -t react-app:prod .
```
# Run
```
docker run --rm -p 8080:80 react-app:prod
```
# Visit http://localhost:8080

The production Dockerfile is set up to build static assets (npm ci && npm run build) and serve them (commonly with Nginx).
Prefer multi-stage builds to keep the final image small.

🛠️ Scripts

Common NPM scripts:
```
npm run start     # local dev (outside Docker)
npm run build     # production build
npm run preview   # (if using Vite) serve build locally
```
⚙️ Environment variables

React only exposes variables prefixed with REACT_APP_ (CRA) or VITE_ (Vite) at build time.
For runtime configuration, consider:

Injecting via Nginx env-subst

Fetching a /config.json generated at container start

🧪 CI/CD (optional)

Example GitHub Actions outline:

Lint & Test

Build production image

Push to registry

##(Optionally) Deploy

name: ci
on: [push, pull_request]
jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/setup-buildx-action@v3
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v6
        with:
          context: .
          push: false
          tags: ghcr.io/<owner>/<repo>:latest

