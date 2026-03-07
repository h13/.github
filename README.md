# .github

Default community health files, reusable workflows, and shared linter config for h13 repositories.

## Reusable Workflows

Call from any repo's `ci.yml` via `uses: h13/.github/.github/workflows/<name>@v1`:

| Workflow | Stack | Inputs | Description |
|---|---|---|---|
| `ci-node.yml` | Node.js | `node-version` | npm ci + npm test |
| `ci-php.yml` | PHP | `php-version`, `php-extensions`, `working-directory`, `composer-args`, `test-command` | composer install + test |
| `ci-go.yml` | Go | — | go test + golangci-lint |
| `ci-markdown.yml` | Markdown | `glob` | markdownlint-cli2 (org config auto-applied) |

Example caller (`ci.yml` in your repo):

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

Reusable workflows follow semver via git tags (`v1`, `v1.0.0`, etc.).

- **Non-breaking changes** (bug fixes, dependency updates): `v1` tag moves forward
- **Breaking changes** (removed inputs, changed behavior): new major `v2`
- Renovate auto-detects new versions and creates PRs in caller repos

## Composite Actions

| Action | Description |
|---|---|
| `actions/apply-org-config` | Download org config file if no local override exists |

Usage: `uses: h13/.github/actions/apply-org-config@v1`

## Shared Linter Config

- `.markdownlint-cli2.yaml` — Org-wide markdownlint defaults (MD013, MD024, MD060)
  - Auto-applied by `ci-markdown.yml` via `apply-org-config` action
  - Repos can override by adding their own `.markdownlint-cli2.yaml`

## Automation

| Workflow | Schedule | Description |
|---|---|---|
| Compliance Audit | Monthly (1st) | Checks all repos for renovate.json, CI, branch protection |
| Repo Sync | On push to `sync/` | Syncs org-wide files (PR template, etc.) to all repos |

Repo Sync requires `REPO_SYNC_TOKEN` secret (PAT with `repo` scope).

## Workflow Templates

Available via **Actions > New workflow > By h13** (one-time copy, for repos needing customization):

| Template | Stack | Description |
|---|---|---|
| CI (Node.js) | Node.js | npm ci + npm test |
| CI (PHP) | PHP | composer install + composer test |
| CI (Go) | Go | go test + golangci-lint |
| CI (Markdown) | Markdown | markdownlint-cli2 |

## Community Health Files

- `SECURITY.md` — Vulnerability reporting policy (applies to all repos)

## Ecosystem

3つのインフラリポが連携して h13 リポジトリの標準化を実現する。

```text
h13/dotfiles                      ← 開発環境 + 初期化スクリプト
  └─ scripts/repo-init.sh
       ├─ generates → renovate.json ──references──→ h13/renovate-config
       └─ generates → .github/workflows/ci.yml     (calls h13/.github reusable workflows)

h13/.github          ← Reusable Workflows + 共有設定 ★ this repo
  ├─ .github/workflows/ci-{node,php,go,markdown}.yml  (reusable, versioned)
  ├─ actions/apply-org-config/                          (composite action)
  ├─ .markdownlint-cli2.yaml                           (org-wide linter config)
  ├─ compliance-audit.yml                              (monthly audit)
  ├─ repo-sync.yml + sync/                             (file sync to all repos)
  ├─ workflow-templates/ (one-time copy 用)
  └─ SECURITY.md (全リポに適用)

h13/renovate-config  ← Renovate 共有プリセット
  ├─ default.json    (全リポ共通)
  └─ terraform.json  (Terraform あり向け)
```

**SHA ピンは reusable workflow 内で一元管理**。Renovate の `helpers:pinGithubActionDigests` が自動更新。
各リポの caller は `@v1` タグを参照するだけ。

| リポ | 役割 |
|---|---|
| [h13/dotfiles](https://github.com/h13/dotfiles) | 開発環境 + `repo-init.sh` |
| [h13/renovate-config](https://github.com/h13/renovate-config) | Renovate 共有プリセット |
| [h13/.github](https://github.com/h13/.github) | Reusable Workflows + 共有設定 + Automation |
