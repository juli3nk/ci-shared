# ci-shared

Reusable GitHub Actions workflows for CI/CD, powered by [Dagger](https://dagger.io).

## Workflows

| Workflow | Purpose |
|---|---|
| `ci.yaml` | Main orchestration for this repository only. |
| `changes.yaml` | Detect changed files and expose outputs for downstream jobs. |
| `git-checks.yaml` | Commit linting and secret scanning. |
| `lint-generic.yaml` | Markdown and JSON linting. |
| `go-lint.yaml` | Go linting and formatting. |
| `go-build.yaml` | Go snapshot build with GoReleaser. |
| `go-deps.yaml` | Go module verification, vulnerability scan, license check. |
| `release.yaml` | Semantic release. |
| `go-publish.yaml` | Publish a Go release with GoReleaser (binaries only). |
| `go-publish-image.yaml` | Publish a Go release with GoReleaser (binaries + container images). |
| `_go-publish.yaml` | Internal shared logic. Do not use directly. |

## Usage

Import the reusable workflows in your own repository:

```yaml
jobs:
  checks:
    uses: juli3nk/ci-shared/.github/workflows/go-lint.yaml@main
    with:
      go_version: "1.26.7"
```

### Publishing

Choose the wrapper that matches your project:

| Workflow | Use case | Required permissions |
|---|---|---|
| `go-publish.yaml` | Go binaries and archives only | `contents: read` |
| `go-publish-image.yaml` | Go binaries + container images | `contents: read`, `packages: write` |

```yaml
jobs:
  release:
    uses: juli3nk/ci-shared/.github/workflows/release.yaml@main

  publish:
    needs: release
    permissions:
      contents: read
      packages: write
    uses: juli3nk/ci-shared/.github/workflows/go-publish-image.yaml@main
    with:
      released: ${{ needs.release.outputs.released }}
      tag: ${{ needs.release.outputs.tag }}
```

## Toolchains

Dagger modules are declared in `dagger.json` and pulled from `github.com/juli3nk/daggerverse`.
