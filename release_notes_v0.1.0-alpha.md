# Chorus v0.1.0-alpha Release Notes

Chorus is an anonymous discussion platform with Git-backed append-only persistence. Every thread assigns temporary identities. Conversations matter more than users.

---

## Status & Stability Warning

> **Status: Experimental (`v0.1.0-alpha`)**
> - The HTTP REST API is under active development and considered **unstable**.
> - The underlying Git repository storage schema (`boards/general/threads/thd_xxxxx/`) may change before `v1.0.0`.
> - This release is intended for developer evaluation, self-hosting experimentation, and community feedback.

---

## Key Features in v0.1.0-alpha

1. **Git-Backed Persistence Engine (`internal/gitstore`)**:
   - Stores threads, message logs, identities, reports, and moderation events inside a local Git repository.
   - Append-only NDJSON log format (`messages.ndjson`).
   - Every write operation creates a standardized local Git commit (`thread: create`, `message: append`, `report: create`, `moderation: action`).
   - Thread summary indexing (`boards/general/index.json`) for fast reads (~15 µs).
   - Automatic startup recovery (`RecoverAndVerify`) rebuilding runtime indexes directly from Git data.

2. **On-Demand Translation Subsystem (`internal/translation`)**:
   - Provider interface supporting **Google Translate**, **LibreTranslate**, and **DeepL**.
   - Messages are translated strictly when requested by the user.
   - Disk-cached translation records (`<thread_dir>/translations/<msg_id>_<target_lang>.json`) for instant sub-millisecond lookups.

3. **Backend GeoIP Country Detection (`internal/geoip`)**:
   - Zero frontend trust: country codes (ISO 3166-1 alpha-2) resolved 100% on the backend from client IP headers (`CF-IPCountry`, `X-Forwarded-For`, `X-Real-IP`, `RemoteAddr`).
   - Provider abstraction supporting Cloudflare headers and local development resolvers.
   - Respects user privacy preferences (`show_country: false` returns `country: null`).

4. **Message Reporting & Human Moderation Queue (`internal/reporting`, `internal/moderation`)**:
   - Immutable report events (`rpt_<hex>`) supporting policy reasons (`spam`, `harassment`, `illegal`, `violence`, `copyright`, `other`).
   - Human-driven moderation queue (`GET /api/v1/moderation/reports`). Zero automated content deletion algorithms.
   - Immutable moderation action events recording status transitions (`pending`, `reviewed`, `dismissed`, `removed`).

5. **Go Backend Core (`cmd/server`, `internal/`)**:
   - Zero external runtime dependencies built on Go 1.22+ `net/http` router.
   - Clean Architecture with explicit dependency injection and Single Source of Truth (SSOT).
   - Health check endpoint (`GET /healthz`), structured JSON logging (`log/slog`), and graceful shutdown listeners.
   - Pagination support (`limit`, `offset`) on list endpoints.

6. **React Frontend (`web/`)**:
   - Built with React 19, TypeScript, Vite, and TanStack Query.
   - Minimalist technical aesthetic (system typography, light-mode, monospace IDs).
   - Interactive Translate toggles and report reason selection modal.

7. **Deployment & Operations**:
   - Single-origin production setup ([chat.joinchorus.app](https://chat.joinchorus.app)).
   - Public marketing site ([joinchorus.app](https://joinchorus.app)) & Technical Docs ([docs.joinchorus.app](https://docs.joinchorus.app)).
   - Production Docker Compose manifest with persistent volumes and healthchecks.
   - Reverse proxy templates for **Caddy** (automatic TLS) and **Nginx** (rate limiting).
   - Comprehensive deployment guide ([DEPLOYMENT.md](DEPLOYMENT.md)) and OpenAPI 3.0 specification (`openapi.yaml`).

---

## Known Limitations

1. **Synchronous Git CLI Overhead**:
   - Each write operation invokes external `git add` and `git commit` processes (`exec.Command`), introducing a ~50ms baseline latency floor per write.
2. **Mutex Contention Under Extreme Load**:
   - Storage writes serialize behind a `sync.RWMutex`, capping write throughput under heavy concurrent loads.

---

## Roadmap

- **Server-Sent Events (SSE) / Polling**: Real-time thread updates for active client sessions.
- **Frontend Moderation Interface**: Dedicated web interface for reviewing the moderation queue.
- **Full Event Sourcing**: Migrating repository data completely to append-only event streams.
- **Background Git Commit Queue**: Decoupling file writes from Git process execution via an asynchronous batch commit queue to eliminate the ~50ms write latency floor.
- **Performance Optimizations**: Native C bindings (`libgit2`) to bypass `exec.Command` process overhead.

---

## Community & Governance

- [SECURITY.md](SECURITY.md) - Responsible vulnerability disclosure policy
- [CONTRIBUTING.md](CONTRIBUTING.md) - Contribution guidelines
- [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md) - Contributor Covenant v2.1
- [SUPPORT.md](SUPPORT.md) - Support guide
- [LICENSE](LICENSE) - MIT License
