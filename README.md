<p align="center">
  <img src="./assets/readme/hero.svg" width="100%" alt="Chorus Docs: REST API reference, architecture diagrams and RFCs, shown with endpoint cards such as GET /healthz and POST /api/v1/threads/{id}/messages.">
</p>

<p align="center">
  <a href="https://docs.joinchorus.app"><b>Read the docs</b></a> ·
  <a href="https://chat.joinchorus.app">Live app</a> ·
  <a href="https://github.com/joinchorus/chorus">App repository</a> ·
  <a href="https://joinchorus.app">Website</a>
</p>

<p align="center">
  <a href="https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml"><img src="https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml/badge.svg" alt="CI/CD status"></a>
  <a href="./LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="MIT license"></a>
  <img src="https://img.shields.io/badge/Chorus-v0.1.0--alpha-58a6ff.svg" alt="Chorus v0.1.0-alpha, early development">
</p>

This repository is the source of the technical documentation portal for [Chorus](https://joinchorus.app), an anonymous discussion platform that stores every thread and message as a commit in a Git repository. The portal is a static site served at [docs.joinchorus.app](https://docs.joinchorus.app).

> [!NOTE]
> Chorus is in early development (`v0.1.0-alpha`). The REST API documented here is unstable and may change before `v1.0.0`.

## What the portal documents

The REST API lives under `/api/v1` (also reachable as `/api/v0.1`). The portal documents seven endpoints:

| Method | Path | Purpose |
| :-- | :-- | :-- |
| `GET` | `/healthz` | Health check probe |
| `POST` | `/api/v1/identities` | Register a session identity |
| `GET` | `/api/v1/threads` | List threads |
| `POST` | `/api/v1/threads` | Create a thread |
| `POST` | `/api/v1/threads/{id}/messages` | Post a message to a thread |
| `POST` | `/api/v1/threads/{id}/messages/{msg_id}/translate` | Translate a message |
| `POST` | `/api/v1/moderation/reports/{id}/action` | Act on a moderation report |

Each endpoint block lists purpose, parameters, status codes, an example `curl` request and an example JSON response. The same portal also covers:

- **Architecture:** an 8-layer component diagram, from browser client to the native Git repository, and a 9-stage write lifecycle diagram.
- **Git persistence:** the storage engine specification, with the formal write-up in [RFC-001](./rfcs/RFC-001-git-persistence.md).
- **Operations:** deployment configuration, contributing guidelines and a roadmap.
- **Search:** press `/` or `Cmd/Ctrl+K` to filter the portal.

A machine-readable [OpenAPI file](./public/openapi.yaml) is also in `public/`. Note that it describes a smaller set of paths than the portal page.

## Run it locally

The site is plain static files in `public/`, so there is nothing to build.

```bash
git clone https://github.com/joinchorus/docs.git
cd docs
npm run serve    # runs `npx serve public`, then open http://localhost:3000
```

## Repository map

| Path | Contents |
| :-- | :-- |
| [`public/`](./public) | The deployed site: `index.html`, `openapi.yaml`, favicon, sitemap |
| [`rfcs/`](./rfcs) | Architecture RFCs ([RFC-001: Git persistence](./rfcs/RFC-001-git-persistence.md)) |
| [`event_architecture.md`](./event_architecture.md) | Immutable event architecture and auditability |
| [`PERFORMANCE.md`](./PERFORMANCE.md) | Benchmarks and load testing report |
| [`DEPLOYMENT.md`](./DEPLOYMENT.md) | Production deployment and reliability guide |
| [`release_notes_v0.1.0-alpha.md`](./release_notes_v0.1.0-alpha.md) | Release notes for `v0.1.0-alpha` |

## Deployment

A push to `main` runs [`.github/workflows/ci-cd.yml`](./.github/workflows/ci-cd.yml), which checks that `public/index.html` exists and deploys `public/` to Firebase Hosting (project `chorus-chat-app`, target `chorus-docs`). Pull requests run the check only.

## Contributing and security

Corrections and improvements are welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md) before opening a pull request. To report a vulnerability, read [SECURITY.md](./SECURITY.md) and use [private vulnerability reporting](https://github.com/joinchorus/docs/security/advisories/new) instead of a public issue.

## License

[MIT](./LICENSE)
