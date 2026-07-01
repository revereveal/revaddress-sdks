# RevAddress USPS v3 SDKs

Idiomatic client SDKs for the modern **USPS v3 REST API** — Python, Node.js, and PHP — plus the shared OpenAPI contract they implement. One history-preserving monorepo, one release surface.

> These SDKs replace the dead USPS Web Tools wrappers (410 Gone since Web Tools shut down 2026-01-25). Same shape, new v3 OAuth + token-refresh plumbing. MIT-licensed.

| SDK | Package | Install | Source |
|-----|---------|---------|--------|
| **Python** | [`usps-v3`](https://pypi.org/project/usps-v3/) · PyPI | `pip install usps-v3` | [`packages/python`](packages/python) |
| **Node.js / TS** | [`usps-v3`](https://www.npmjs.com/package/usps-v3) · npm | `npm install usps-v3` | [`packages/node`](packages/node) |
| **PHP** | [`revaddress/usps-v3-php`](https://packagist.org/packages/revaddress/usps-v3-php) · Packagist | `composer require revaddress/usps-v3-php` | [`packages/php`](packages/php) |

## The shared contract

All three SDKs implement the same surface — the **RevAddress USPS v3 API** — captured in [`spec/openapi.yaml`](spec/openapi.yaml). Endpoints span address validation, city/state lookup, batch, rates, international prices, labels (+ download / void), tracking, pickup, locations, and service standards. See [`spec/`](spec/) and [`spec/SOURCE.md`](spec/SOURCE.md) for the contract origin and the per-package publish architecture.

## Layout

```
packages/python/   usps-v3  (PyPI)     — httpx, 9 modules, 67 tests
packages/node/     usps-v3  (npm)      — zero-dep, TypeScript-first, 12 modules, 58 tests
packages/php/      revaddress/usps-v3-php (Packagist) — zero-dep, PSR-4, 59 tests
spec/              OpenAPI contract + raw USPS fragments
```

Each package keeps its own README, tests, CI leg, and independent version (tag prefixes `py-v*` / `node-v*` / `php-v*`).

## Don't want to manage USPS credentials?

[RevAddress](https://revaddress.com) handles OAuth, rate limits, and token refresh — get an API key and validate addresses in ~60 seconds.

## License

MIT — see [LICENSE](LICENSE). Consolidated from three separate repos (history preserved via subtree merge, 2026-07-01).
