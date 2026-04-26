# Portfolio Application

A containerized, Server-Side Rendered (SSR) portfolio application with a fully automated GitOps deployment pipeline.

Live: [portfolio.pratik-labs.xyz](https://portfolio.pratik-labs.xyz)
Infrastructure: [k8s-homelab-platform](https://github.com/pratikbhattarai76/k8s-homelab-platform)

---

## Architecture

The application is built with Astro (SSR on Node.js), packaged as a Docker image, and deployed to a bare-metal Kubernetes cluster via GitOps.

```
User → Cloudflare → Tunnel → Traefik → Portfolio Pod (Astro SSR / Node.js)
```

- SSR required for the contact form API endpoint (Nodemailer)
- Runs on port 4321 inside the container
- Kubernetes Deployment with Service and Ingress managed by Argo CD

---

## CI/CD Pipeline

Fully automated — pushing code to `main` triggers build, publish, and deployment with zero manual steps.

```
1. Push to main
        ↓
2. GitHub Actions: verify
   - Type check, build, smoke test
        ↓
3. GitHub Actions: build-and-push
   - Build Docker image
   - Push to ghcr.io/pratikbhattarai76/portfolio-app
   - Tags: latest + commit-<sha>
        ↓
4. GitHub Actions: update-deployment
   - Clone k8s-homelab-platform repo
   - Update image tag in manifests/apps/portfolio/deployment.yml
   - Commit and push
        ↓
5. Argo CD detects manifest change → syncs → new version live
```

The `update-deployment` job uses `sed` to replace the image tag in the Kubernetes manifest, then pushes to the homelab repo. Argo CD watches that repo and auto-deploys.

Authentication between repos uses a GitHub PAT with `repo` scope, stored as `PORTFOLIO_K8S_SERVER_DEPLOYMENT` in this repo's secrets.

---

## Technology Stack

**Application:** Astro, TypeScript, Tailwind CSS, Node.js (SSR), Nodemailer (SMTP)

**Delivery:** Docker (multi-stage build), GitHub Actions, GHCR, Argo CD

---

## Local Development

```bash
git clone https://github.com/pratikbhattarai76/portfolio-app.git
cd portfolio-app
npm install
cp .env.example .env
npm run dev
```

---

## Documentation

- **[CI/CD Pipeline](docs/ci-cd.md)** — Workflow details, concurrency, permissions
- **[Architecture](docs/architecture.md)** — Application flow and deployment
- **[Decisions](docs/decisions.md)** — Why Docker, why SSR, why GHCR, why multi-stage

---

## Evolution

This application has been deployed through two infrastructure phases:

**Phase 1 — Docker Compose** ([private-cloud-infrastructure](https://github.com/pratikbhattarai76/private-cloud-infrastructure)): Pull-based deployment using a cron script that checked GHCR for new images every 30 minutes. Routed through Nginx Proxy Manager and Cloudflare Tunnel.

**Phase 2 — Kubernetes (current)** ([k8s-homelab-platform](https://github.com/pratikbhattarai76/k8s-homelab-platform)): Push-based GitOps pipeline. GitHub Actions updates the manifest, Argo CD deploys. Routed through Traefik and Cloudflare Tunnel.

The application code and Dockerfile remained unchanged between phases — only the delivery infrastructure changed, which is the point of containerization.
