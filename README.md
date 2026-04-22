# CI Blueprints

Central reusable GitHub Actions workflows used as pipeline blueprints for every project provisioned by the GITOPS Dashboard.

## Usage

Each provisioned project gets a thin wrapper at `.github/workflows/ci.yml`:

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  ci:
    uses: puniiizilla/ci-blueprints/.github/workflows/node.yml@main
    secrets: inherit
```

Swap `node.yml` for `python.yml`, `rust.yml`, or `go.yml` depending on project language.

## Blueprints

| File | Stages |
|---|---|
| `node.yml`   | lint → typecheck → test → build → docker (gated by Dockerfile) |
| `python.yml` | ruff → pyright → pytest → build → docker |
| `rust.yml`   | fmt → clippy → test → build → docker |
| `go.yml`     | vet → test → build → docker |

## Docker

Every blueprint pushes `ghcr.io/<owner>/<repo>:<sha>` and `:latest` when:
- `Dockerfile` exists at the repo root
- The run is a `push` to `main`

Uses auto-injected `GITHUB_TOKEN` — no extra secrets required.

## Updating

Edit the workflow here and every downstream project picks up the change on its next run (via `@main` ref).
