# Contributing to Chorus

Thank you for your interest in contributing to **Chorus**! We welcome contributions from developers of all skill levels.

Full technical documentation and developer guides are available at [docs.joinchorus.app](https://docs.joinchorus.app/contributing).

---

## Getting Started

### Prerequisites
- **Go**: 1.22 or higher
- **Node.js**: 20 or higher
- **Git**: 2.30 or higher

### Local Development Setup

1. **Fork and clone the repository**:
   ```bash
   git clone https://github.com/YOUR_USERNAME/Chorus.git
   cd Chorus
   ```

2. **Run the Go backend server**:
   ```bash
   go run ./cmd/server
   ```

3. **Run the React frontend**:
   ```bash
   cd web
   npm install
   npm run dev
   ```

---

## Architectural Guidelines

When writing code for Chorus, please keep these core principles in mind:

- **Keep Go Idiomatic & Pragmatic**: Avoid overengineering or adding enterprise abstractions for simple features.
- **Single Source of Truth (SSOT)**: Domain models (`internal/domain`, `internal/thread/model.go`) hold JSON tags. Do not create duplicate dto/mapper layers unless there is a concrete reason.
- **Concrete Types & Consumer Interfaces**: Return concrete struct pointers from constructors. Move interfaces to consumer packages.
- **Backend as Single Source of Truth**: The frontend must never generate IDs, timestamps, or country determinations. The backend creates all system values.
- **Frontend Design Aesthetics**: Keep the React UI minimal, calm, and readable (light mode, system typography, monospace IDs). Avoid modern SaaS clichés (gradients, floating cards, glowing effects).

---

## Development Workflow & Testing

Before submitting a Pull Request, verify that all tests pass:

```bash
# 1. Run backend tests with race detection
go test -v -race ./...

# 2. Run Go vet
go vet ./...

# 3. Verify frontend build
cd web && npm run build
```

---

## Submitting Pull Requests

1. Create a feature branch: `git checkout -b feature/my-feature-name`.
2. Keep your commits clean and focused.
3. Push to your fork and submit a Pull Request to the `main` branch.
4. Provide a clear summary in your PR description explaining the motivation and changes made.

Thank you for helping build a calmer internet!
