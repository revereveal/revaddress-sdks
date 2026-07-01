# Spec source & publish architecture

## Canonical contract origin

`spec/openapi.yaml` is a **point-in-time synced snapshot** of the RevAddress USPS v3 API contract
(title *"RevAddress USPS v3 API"*, OpenAPI 3.0.3).

- **Canonical origin:** `jtalk22/revasser-operating-system` → `services/usps-api-worker/openapi.yaml`
  (the hosted worker's source of truth). This copy is a consumer-facing reference — it is **not**
  auto-synced, so on any drift the worker's version is authoritative. Treat this as a snapshot.
- `spec/usps-raw/` holds the upstream USPS OpenAPI fragments the contract is built on
  (`addresses-v3r2_4.yaml`, `enhanced-addresses-v3r2.yaml`, `subscriptions-addresses_1.yaml`).

## Per-package publish architecture

| Package | Registry | Publish path |
|---------|----------|--------------|
| `packages/python` | PyPI `usps-v3` | tag `py-v*` → GitHub Action → PyPI (repo secret `PYPI_API_TOKEN`) |
| `packages/node` | npm `usps-v3` | tag `node-v*` → GitHub Action → npm (repo secret `NPM_TOKEN`) |
| `packages/php` | Packagist `revaddress/usps-v3-php` | public root-`composer.json` repo `revereveal/usps-v3-php` (see below) |

### PHP / Packagist — the root-`composer.json` mirror (do not skip)

Public **Packagist.org does not serve a package whose `composer.json` sits in a subdirectory** —
subdirectory packages are a *Private Packagist* feature only. So `packages/php` cannot be the
Packagist source directly.

**Current mechanism (2026-07-01):** the existing public repo `revereveal/usps-v3-php` — which has
`composer.json` at its ROOT and is what Packagist `revaddress/usps-v3-php` already points at — serves
as the mirror. Its content is identical to `packages/php` (this monorepo is its origin via subtree
merge). To keep them in sync on future releases, `git subtree split --prefix=packages/php` (or
`splitsh-lite`) and force-push the result to `revereveal/usps-v3-php`. This monorepo stays canonical;
`usps-v3-php` is the derived Packagist mirror. (Standard monorepo-component pattern — e.g. Symfony.)

### Moving PyPI/npm publishing here — token-based, no portal re-registration

The original repos publish via **repo-secret tokens** (`PYPI_API_TOKEN`, `NPM_TOKEN`), NOT OIDC
trusted-publishing. To move publishing to this monorepo:

1. Add `PYPI_API_TOKEN` and `NPM_TOKEN` as Actions secrets on `revereveal/revaddress-sdks` (same
   token values, or regenerate on pypi.org / npmjs.com).
2. Add tag-triggered release workflows (`py-v*` → build `packages/python` → `twine upload`;
   `node-v*` → build `packages/node` → `npm publish`).
3. Then the Python/Node originals (`usps-v3`, `usps-v3-node`) can be tombstoned + archived — they are
   no longer the publish source.

Until steps 1–2 are done, `usps-v3` and `usps-v3-node` stay live as the publish sources (nothing
broke); only their GitHub links moved to this monorepo.
