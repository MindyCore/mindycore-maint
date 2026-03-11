# mindycore-maint

Static maintenance page for **mindycore.com** — served via Docker and deployed through a Cloudflare Tunnel.

---

## Overview

| Property | Value |
|----------|-------|
| Target site | `mindycore.com` |
| Maintenance URL | `https://coremaint.mindycore.com` |
| Server port | `3006` (bound to `127.0.0.1`) |
| Image | `ghcr.io/mindycore/mindycore-maint` |
| Registry | GitHub Container Registry (GHCR) |

---

## How it works

1. Container runs nginx serving a single static HTML page on port `80` (mapped to `3006` on the host).
2. The Cloudflare Tunnel `mindycore` exposes `localhost:3006` as `https://coremaint.mindycore.com`.
3. A Cloudflare **Redirect Rule** (`mindycore-maintenance-mode`) in the `mindycore.com` zone redirects all traffic from `mindycore.com/*` → `https://coremaint.mindycore.com` (HTTP 302).

---

## Toggling maintenance mode

### Enable
1. Go to Cloudflare → `mindycore.com` zone → **Rules** → **Redirect Rules**.
2. Enable the rule named **`mindycore-maintenance-mode`**.

### Disable
1. Go to Cloudflare → `mindycore.com` zone → **Rules** → **Redirect Rules**.
2. Disable (or delete) the rule named **`mindycore-maintenance-mode`**.

---

## Deployment

The container is managed in [MindyCore/Mindy_devops](https://github.com/MindyCore/Mindy_devops) under `services/mindycore-maint/`.

```bash
# On the server
cd ~/services/mindycore_maintainance
docker compose pull
docker compose up -d
```

---

## CI/CD

On every push to `main` or on manual dispatch, the GitHub Actions workflow:
1. Builds the Docker image.
2. Tags it with the version from `VERSION` file and with `latest`.
3. Pushes to `ghcr.io/mindycore/mindycore-maint`.

To release a new version:
```bash
echo "1.1.0" > VERSION
git add VERSION && git commit -m "chore: bump version to 1.1.0" && git push origin main
```

---

## Local development

```bash
docker build -t mindycore-maint .
docker run -p 3006:80 mindycore-maint
# Open http://localhost:3006
```
