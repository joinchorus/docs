# Chorus Production Deployment & Reliability Guide

This guide provides reproducible, production-ready instructions for deploying **Chorus** across its standardized domain architecture.

---

## 1. Domain & Routing Architecture

Chorus uses a single-responsibility subdomain layout. Each subdomain has exactly one purpose:

| Domain | Purpose | Description & Routing |
| :--- | :--- | :--- |
| **`https://joinchorus.app`** | Public Website | Landing page, philosophy, features, privacy, roadmap, open source, and GitHub link. Does **not** contain application logic. CTA "Start Conversation" redirects to `https://chat.joinchorus.app`. |
| **`https://chat.joinchorus.app`** | Application & API | The actual Chorus application. Serves the React SPA and same-origin REST API (`/api/v0.1/*`). No separate API subdomain, no CORS, no visible Firebase URLs. |
| **`https://docs.joinchorus.app`** | Documentation | Technical guides, architecture specs, OpenAPI documentation, deployment guides, security, performance, and contribution guidelines. |

> [!IMPORTANT]
> **No Additional Subdomains**: Do not create `api.joinchorus.app`, `assets.joinchorus.app`, `cdn.joinchorus.app`, or `status.joinchorus.app`. All app logic and API endpoints run under `chat.joinchorus.app`.

---

## 2. Single-Origin Application Execution

For `https://chat.joinchorus.app`:
- **Go Backend Server (`cmd/server/main.go`)**: Serves both `/api/v0.1/*` REST endpoints and static SPA frontend assets (`web/dist`).
- **Single-Origin Deployment**: Completely eliminates CORS headers, proxy domain mismatches, and static host routing errors.
- **SPA Fallback Routing**: Any non-API route (`/thread/:id`, `/new`) is handled by client-side React routing via `web/dist/index.html`.
- **API Endpoints**: `/api/v0.1/threads`, `/api/v0.1/threads/:id`, `/api/v0.1/threads/:id/messages`, `/api/v0.1/moderation/login`, `/healthz`.

---

## 3. Production Reliability & Hardening Architecture

### A. HTTP Security Headers & CSP
All responses automatically enforce OWASP-compliant security headers (`internal/http/middleware/security.go`):
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Referrer-Policy: strict-origin-when-cross-origin`
- `Permissions-Policy: camera=(), microphone=(), geolocation=(), payment=()`
- `Cross-Origin-Opener-Policy: same-origin`
- `Cross-Origin-Resource-Policy: same-origin`
- `Strict-Transport-Security: max-age=31536000; includeSubDomains; preload` (on HTTPS)
- **Content Security Policy (CSP)**: Compatible with Markdown rendering, KaTeX math expressions, Web Workers, and Monaco Editor. Obsolete `X-XSS-Protection` headers are omitted in favor of modern CSP protection.

### B. Moderator Cookie Security
Moderator sessions use `HttpOnly`, `SameSite=Lax`, `Path=/` cookies (`chorus_admin_session`) set via `POST /api/v0.1/moderation/login`. Tokens are never stored in browser `localStorage`.
- **Cross-Subdomain Migration Path**: For future cross-subdomain admin sharing between `joinchorus.app` and `chat.joinchorus.app`, update cookie configuration to set `Domain=.joinchorus.app`.

### C. Request ID Correlation Logging
Every HTTP request is assigned a correlation ID (`X-Request-ID`), attached to:
- HTTP Response Header (`X-Request-ID`)
- Request Context (`httputil.GetRequestID(ctx)`)
- Structured `log/slog` logs (`request_id=req_<hex>`)
- Panic recovery logs and API error payloads

### D. Graceful Shutdown Lifecycle
Upon receiving `SIGINT` or `SIGTERM`, the server initiates a 10-second graceful shutdown:
1. Stops accepting new HTTP connections (`srv.Shutdown(ctx)`).
2. Drains active HTTP requests, ensuring in-progress Git commits finish cleanly.
3. Flushes logs and exits with code `0`.

---

## 4. Environment Configuration

| Variable | Type | Default | Description |
| :--- | :--- | :--- | :--- |
| `PORT` | `string` | `8085` | HTTP listening port for Go backend. |
| `ENV` | `string` | `production` | Deployment environment (`production` or `development`). |
| `DATA_DIR` | `string` | `./data/repository` | Path to persistent Git repository directory. |
| `CHORUS_ADMIN_TOKEN` | `string` | `secret` | Secret token required for moderation login/endpoints. |
| `READ_TIMEOUT` | `duration` | `5s` | Server HTTP read timeout. |
| `WRITE_TIMEOUT`| `duration` | `10s` | Server HTTP write timeout. |
| `IDLE_TIMEOUT` | `duration` | `120s` | Server HTTP keep-alive idle timeout. |

---

## 5. Reverse Proxy Setup (Caddy / Nginx)

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

---

## 6. Docker Container Deployment

To run Chorus on a VPS, Cloud Run, or standalone server:

```bash
# 1. Build Production Container
docker build -t chorus:latest .

# 2. Run Container
docker run -d \
  -p 8085:8080 \
  -e ENV=production \
  -e CHORUS_ADMIN_TOKEN=your-secure-admin-secret \
  -v chorus-data:/app/data/repository \
  --name chorus-app \
  chorus:latest

# 3. Verify Health & Request ID
curl -i http://localhost:8085/healthz
```
