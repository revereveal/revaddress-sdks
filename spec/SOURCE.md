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
| `packages/python` | PyPI `usps-v3` | tag `py-v*` → GitHub Action → PyPI (trusted publisher on **this** repo) |
| `packages/node` | npm `usps-v3` | tag `node-v*` → GitHub Action → npm (trusted publisher on **this** repo) |
| `packages/php` | Packagist `revaddress/usps-v3-php` | **split mirror** (see below) |

### PHP / Packagist — the split-repo requirement (do not skip)

Public **Packagist.org does not serve a package whose `composer.json` sits in a subdirectory** —
subdirectory packages are a *Private Packagist* feature only. So `packages/php` cannot be the
Packagist source directly.

**Mechanism:** `git subtree split --prefix=packages/php` (or `splitsh-lite`) pushes `packages/php/`
to a dedicated **read-only mirror repo** whose `composer.json` is at its ROOT, and Packagist points
at the mirror. This monorepo stays canonical; the mirror is regenerated on each release. (Standard
monorepo-component pattern — e.g. Symfony's component split repos.)

### Trusted-publisher note (PyPI + npm)

PyPI and npm trusted publishing is keyed to `org/repo/workflow/environment`. Moving the publish
source to this repo requires updating the trusted-publisher config on PyPI (`usps-v3`) and npm
(`usps-v3`) to `revereveal/revaddress-sdks` + the release workflow **before** the first tagged
release from here — otherwise the publish is rejected.
