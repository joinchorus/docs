# Chorus — Technical Documentation Portal

[![Docs CI/CD](https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml/badge.svg)](https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Official technical documentation portal, REST API reference, RFCs, and deployment guides for Chorus ([docs.joinchorus.app](https://docs.joinchorus.app)).

---

## 🔗 Navigation

- 🌐 **Marketing Website:** [joinchorus.app](https://joinchorus.app)
- 💬 **Launch Application:** [chat.joinchorus.app](https://chat.joinchorus.app)
- 💻 **Application Repository:** [joinchorus/chorus](https://github.com/joinchorus/chorus)

---

## 📚 What's Inside

- **13 REST API Endpoint Specifications:** Purpose, HTTP Method, Route, Request/Response payloads, curls, and status codes.
- **8-Layer SVG Architecture Diagram:** End-to-end component decomposition.
- **9-Stage Request Lifecycle SVG Diagram:** Step-by-step request execution flow.
- **RFC Documents (`/rfcs`):** Architectural RFCs including `RFC-001-git-persistence.md`.
- **Command Palette Search:** Support for `/` and `Cmd/Ctrl+K` instant filtering.

---

## 🛠️ Local Development

```bash
# Clone the repository
git clone https://github.com/joinchorus/docs.git
cd docs

# Serve locally
npm run serve
# Available on http://localhost:3000
```

---

## 📦 Deployment

Deploys independently to **Firebase Hosting** targeting `docs.joinchorus.app` via GitHub Actions (`.github/workflows/ci-cd.yml`).

---

## 📄 License

Distributed under the MIT License. See [LICENSE](LICENSE) for details.
