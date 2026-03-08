# .github

[![CI](https://github.com/h13/.github/actions/workflows/ci.yml/badge.svg)](https://github.com/h13/.github/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Default community health files, reusable workflows, and shared config for h13 repositories.

## Reusable Workflows

Call from any repo via `uses: h13/.github/.github/workflows/<name>@v1`:

| Workflow | Stack | Inputs | Steps |
|---|---|---|---|
| `ci-node.yml` | Node.js | `node-version` | npm ci (cached) → npm test |
| `ci-go.yml` | Go | — | go test -race → golangci-lint |
| `ci-php.yml` | PHP | `php-version`, `php-extensions`, `working-directory`, `composer-args`, `test-command` | composer install (cached) → composer test |
| `ci-terraform.yml` | Terraform | `terraform-version` | terraform fmt/init/validate → tflint |
| `ci-markdown.yml` | Markdown | `glob` | markdownlint-cli2 (org config auto-applied) |

All workflows enforce `permissions: { contents: read }` (least-privilege).

### Example

```yaml
name: CI
on:
  push:
    branches: [main]
  pull_request:
jobs:
  ci:
    uses: h13/.github/.github/workflows/ci-node.yml@v1
```

## Versioning

- **Non-breaking** (fixes, dependency updates): `v1` tag moves forward
- **Breaking** (removed inputs, changed behavior): new major `v2`
- SHA pins inside workflows are auto-updated by Renovate

## Composite Actions

| Action | Description |
|---|---|
| `actions/apply-org-config` | Download org config if no local override exists |

## Automation

| Workflow | Schedule | Description |
|---|---|---|
| Compliance Audit | Monthly (1st) | Checks all repos for renovate.json, CI, branch protection |
| Repo Sync | On push to `sync/` | Syncs PR template to all repos |

## Ecosystem

```text
h13/dotfiles                      ← Dev environment + repo-init
  └─ repo-init --stack=node --github
       ├─ generates → renovate.json ──→ h13/renovate-config
       ├─ generates → .github/workflows/ci.yml (calls @v1)
       └─ configures → GitHub settings (branch protection, labels, alerts)

h13/.github                       ← Reusable Workflows + shared config ★
  ├─ ci-{node,go,php,terraform,markdown}.yml (reusable, SHA-pinned)
  ├─ compliance-audit.yml (monthly)
  ├─ repo-sync.yml (PR template sync)
  └─ apply-org-config action

h13/renovate-config               ← Shared Renovate presets
  ├─ default.json (config:best-practices)
  ├─ node.json / go.json / php.json / terraform.json
  └─ Stack-specific dependency rules
```
