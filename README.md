# .github

[![CI](https://github.com/h13/.github/actions/workflows/ci.yml/badge.svg)](https://github.com/h13/.github/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

Default community health files, reusable workflows, and shared config for h13 repositories.

## Reusable Workflows

Call from any repo via `uses: h13/.github/.github/workflows/<name>@<sha>`:

| Workflow           | Stack     | Inputs                                                                                | Steps                                       |
| ------------------ | --------- | ------------------------------------------------------------------------------------- | ------------------------------------------- |
| `ci-node.yml`      | Node.js   | `node-version`                                                                        | npm ci (cached) → npm run lint → npm test   |
| `ci-go.yml`        | Go        | —                                                                                     | go test -race → golangci-lint               |
| `ci-php.yml`       | PHP       | `php-version`, `php-extensions`, `working-directory`, `composer-args`, `test-command` | composer install (cached) → composer test   |
| `ci-terraform.yml` | Terraform | `terraform-version`                                                                   | terraform fmt/init/validate → tflint        |
| `ci-markdown.yml`  | Markdown  | `glob`                                                                                | markdownlint-cli2 (org config auto-applied) |

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
    uses: h13/.github/.github/workflows/ci-node.yml@c62d0eebefffc0d3ca156453f0fb7cd3dcb94f4c # main
```

## Versioning

- All workflow references use **SHA pinning** (no `@v1` tags)
- SHA pins are auto-updated by **Renovate** (Dependabot is not used)

## Composite Actions

| Action                     | Description                                     |
| -------------------------- | ----------------------------------------------- |
| `actions/apply-org-config` | Download org config if no local override exists |

## Automation

| Workflow         | Schedule           | Description                                                                    |
| ---------------- | ------------------ | ------------------------------------------------------------------------------ |
| Compliance Audit | Monthly (1st)      | Checks all repos for renovate.json, CI, branch protection, LICENSE, CODEOWNERS |
| Repo Sync        | On push to `sync/` | Syncs PR/issue templates to all non-archived repos (dynamic)                   |

## Community Health Files

Org-wide defaults (applied to all repos without their own):

| File              | Purpose                        |
| ----------------- | ------------------------------ |
| `SECURITY.md`     | Vulnerability reporting policy |
| `CONTRIBUTING.md` | Contribution guidelines        |
| `CODEOWNERS`      | Default code review ownership  |
| `LICENSE`         | MIT license                    |

## Dependency Policy

All repositories use **Renovate** exclusively for dependency management. Dependabot is not used.
Shared presets are maintained in [h13/renovate-config](https://github.com/h13/renovate-config).

## Ecosystem

```text
h13/dotfiles                      ← Dev environment + repo-init
  └─ repo-init --stack=node --github
       ├─ generates → renovate.json ──→ h13/renovate-config
       ├─ generates → .github/workflows/ci.yml (SHA-pinned)
       └─ configures → GitHub settings (branch protection, labels, alerts)

h13/.github                       ← Reusable Workflows + shared config ★
  ├─ ci-{node,go,php,terraform,markdown}.yml (reusable, SHA-pinned)
  ├─ compliance-audit.yml (monthly)
  ├─ repo-sync.yml (dynamic sync to all repos)
  └─ apply-org-config action

h13/renovate-config               ← Shared Renovate presets
  ├─ default.json (config:best-practices)
  ├─ node.json / go.json / php.json / terraform.json
  └─ Stack-specific dependency rules
```
