<p align="center">
  <img src="assets/readme/hero.svg" alt="Chorus Technical Documentation Portal — docs.joinchorus.app" width="100%">
</p>

<p align="center">
  <a href="https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml"><img src="https://github.com/joinchorus/docs/actions/workflows/ci-cd.yml/badge.svg" alt="CI/CD Status"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="MIT License"></a>
  <a href="https://docs.joinchorus.app"><img src="https://img.shields.io/badge/Live_Portal-docs.joinchorus.app-388bfd.svg" alt="Live Docs Portal"></a>
</p>

<h3 align="center">Official technical documentation portal, REST API reference, and architecture RFCs.</h3>

---

## 🔗 Ecosystem Links

- 🌐 **Marketing Website:** [joinchorus.app](https://joinchorus.app)
- 💬 **Launch Application:** [chat.joinchorus.app](https://chat.joinchorus.app)
- 💻 **Application Repository:** [joinchorus/chorus](https://github.com/joinchorus/chorus)

---

## 📚 What's Inside

- **13 REST API Endpoint Specifications:** Purpose, HTTP Method, Route, Path/Query parameters, Request/Response payloads, curls, and status codes.
- **8-Layer SVG Architecture Diagram:** End-to-end system decomposition.
- **9-Stage Request Lifecycle SVG Diagram:** Step-by-step request execution flow.
- **RFC Documents (`/rfcs`):** Architectural specifications including `RFC-001-git-persistence.md`.
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
