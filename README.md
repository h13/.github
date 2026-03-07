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

## Related

- [h13/renovate-config](https://github.com/h13/renovate-config) — Shared Renovate presets
