# Chorus Production Deployment Guide

This guide provides reproducible, production-ready instructions for deploying **Chorus** across its standardized domain architecture.

---

## 1. Domain & Routing Architecture

Chorus uses a single-responsibility subdomain layout. Each subdomain has exactly one purpose:

| Domain | Purpose | Description & Routing |
| :--- | :--- | :--- |
| **`https://joinchorus.app`** | Public Website | Landing page, philosophy, features, privacy, roadmap, open source, and GitHub link. Does **not** contain application logic. CTA "Start Conversation" redirects to `https://chat.joinchorus.app`. |
| **`https://chat.joinchorus.app`** | Application & API | The actual Chorus application. Serves the React SPA and same-origin REST API (`/api/v1/*`). No separate API subdomain, no CORS, no visible Firebase URLs. |
| **`https://docs.joinchorus.app`** | Documentation | Technical guides, architecture specs, OpenAPI documentation, deployment guides, security, performance, and contribution guidelines. |

> [!IMPORTANT]
> **No Additional Subdomains**: Do not create `api.joinchorus.app`, `assets.joinchorus.app`, `cdn.joinchorus.app`, or `status.joinchorus.app`. All app logic and API endpoints run under `chat.joinchorus.app`.

---

## 2. Single-Origin Application Execution

For `https://chat.joinchorus.app`:
- **Go Backend Server (`cmd/server/main.go`)**: Serves both `/api/v1/*` REST endpoints and static SPA frontend assets (`web/dist`).
- **Single-Origin Deployment**: Completely eliminates CORS headers, proxy domain mismatches, and static host routing errors.
- **SPA Fallback Routing**: Any non-API route (`/thread/:id`, `/new`) is handled by client-side React routing via `web/dist/index.html`.
- **API Endpoints**: `/api/v1/threads`, `/api/v1/threads/:id`, `/api/v1/threads/:id/messages`, `/healthz`.

---

## 3. Environment Configuration

| Variable | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `PORT` | `string` | `8085` | HTTP listening port for Go backend. |
| `ENV` | `string` | `production` | Deployment environment (`production` or `development`). |
| `DATA_DIR` | `string` | `./data/repository` | Path to persistent Git repository directory. |
| `READ_TIMEOUT` | `duration` | `5s` | Server HTTP read timeout. |
| `WRITE_TIMEOUT`| `duration` | `10s` | Server HTTP write timeout. |
| `IDLE_TIMEOUT` | `duration` | `120s` | Server HTTP keep-alive idle timeout. |

---

## 4. Reverse Proxy Setup (Caddy / Nginx)

### Caddy Production Setup (`deploy/Caddyfile`)

```caddy
# Public Website
joinchorus.app {
    root * /var/www/joinchorus.app
    file_server
}

# Application & API (Single Origin)
chat.joinchorus.app {
    reverse_proxy localhost:8085 {
        header_up Host {host}
        header_up X-Real-IP {remote_host}
        header_up X-Forwarded-For {remote_host}
        header_up X-Forwarded-Proto {scheme}
    }
}

# Technical Documentation
docs.joinchorus.app {
    root * /var/www/docs.joinchorus.app
    file_server
}
```

### Nginx Production Setup (`deploy/nginx.conf`)

Review [deploy/nginx.conf](deploy/nginx.conf) for Nginx server blocks for `joinchorus.app`, `chat.joinchorus.app`, and `docs.joinchorus.app` with rate-limiting and IP header forwarding.

---

## 5. Docker Compose Deployment

To run Chorus on a VPS or standalone server:

1. **Clone repository**:
   ```bash
   git clone https://github.com/barissalihbabacan/Chorus.git
   cd Chorus
   ```

2. **Launch stack**:
   ```bash
   docker-compose up -d --build
   ```

3. **Verify Health**:
   ```bash
   curl http://localhost:8085/healthz
   ```

---

## 6. Firebase App Hosting Setup

For `chat.joinchorus.app`, Chorus can be deployed to Firebase App Hosting via `apphosting.yaml`.

- Connect GitHub repository (`barissalihbabacan/Chorus`).
- Map custom domain `chat.joinchorus.app` directly to the Firebase App Hosting backend.
- Firebase URLs are hidden behind custom domain routing — end users only see `chat.joinchorus.app`.
