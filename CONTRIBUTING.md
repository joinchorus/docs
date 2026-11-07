# Contributing to the Chorus docs

This repository is the source of [docs.joinchorus.app](https://docs.joinchorus.app): the REST API reference, architecture diagrams and RFCs for [Chorus](https://github.com/joinchorus/chorus). Changes to the application itself belong in [joinchorus/chorus](https://github.com/joinchorus/chorus) and follow the [organization contributing guide](https://github.com/joinchorus/.github/blob/main/CONTRIBUTING.md).

## Run it locally

The portal is static HTML; there is no build step.

```bash
git clone https://github.com/YOUR_USERNAME/docs.git
cd docs
npm run serve        # npx serve public, prints the local address (default http://localhost:3000)
```

Requirements: Node.js 22 or newer (only for `npx serve`).

## Where things live

| Path | Content |
| :--- | :--- |
| `public/index.html` | The portal: API reference, diagrams, search |
| `public/openapi.yaml` | OpenAPI description (covers fewer paths than the portal page) |
| `rfcs/` | Architecture RFCs, e.g. `RFC-001-git-persistence.md` |
| `*.md` | Event architecture, performance, deployment, release notes |

## Keep the reference accurate

- The API is defined by [`internal/http/router.go`](https://github.com/joinchorus/chorus/blob/main/internal/http/router.go) in the app repo. When you document or change an endpoint, check its method, path, request and response against the code.
- Describe what exists today. Mark planned behaviour as planned.
- New architectural proposals go in `rfcs/` as `RFC-NNN-short-title.md` and are discussed in the pull request.

## Pull requests

1. Branch from `main` (`docs/...`, `fix/...`).
2. Use [Conventional Commits](https://www.conventionalcommits.org/) (`docs: document report endpoint`).
3. Open a pull request using the template. CI checks that `public/index.html` is present; a maintainer review is required.
4. **Merging to `main` deploys to docs.joinchorus.app**, so preview your change locally before asking for review.

Security issues: please do not open a public issue. Use [private vulnerability reporting](https://github.com/joinchorus/docs/security/advisories/new) or see [SECURITY.md](SECURITY.md).
