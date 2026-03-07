# .github

Default community health files and reusable workflow templates for h13 repositories.

## Workflow Templates

Available via **Actions > New workflow > By h13**:

| Template | Stack | Description |
|---|---|---|
| CI (Node.js) | Node.js | npm ci + npm test |
| CI (PHP) | PHP | composer install + composer test |
| CI (Go) | Go | go test + golangci-lint |
| CI (Markdown) | Markdown | markdownlint-cli2 |

All templates use SHA-pinned GitHub Actions.

## Community Health Files

- `SECURITY.md` — Vulnerability reporting policy (applies to all repos)

## Ecosystem

3つのインフラリポが連携して h13 リポジトリの標準化を実現する。

```text
h13/dotfiles                      ← 開発環境 + 初期化スクリプト
  └─ scripts/repo-init.sh
       ├─ generates → renovate.json ──references──→ h13/renovate-config
       └─ generates → .github/workflows/ci.yml     (mirrors h13/.github templates)

h13/.github          ← CI テンプレート + コミュニティヘルスファイル ★ this repo
  ├─ workflow-templates/ci-{node,php,go,markdown}.yml
  └─ SECURITY.md (全リポに適用)

h13/renovate-config  ← Renovate 共有プリセット
  ├─ default.json    (全リポ共通)
  └─ terraform.json  (Terraform あり向け)
```

**SHA ピンの同期**: `repo-init.sh` と `workflow-templates/` の Actions SHA は同じ値を使う。
Renovate の `helpers:pinGithubActionDigests` が以降の更新を自動化する。

| リポ | 役割 |
|---|---|
| [h13/dotfiles](https://github.com/h13/dotfiles) | 開発環境 + `repo-init.sh` |
| [h13/renovate-config](https://github.com/h13/renovate-config) | Renovate 共有プリセット |
| [h13/.github](https://github.com/h13/.github) | CI テンプレート + SECURITY.md |
