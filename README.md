# lms-api-gateway

> Single entry point: authentication, routing and rate limiting

Part of the **LMS Library** distributed system — team `lms-library`, Grupo 2.
Governance and documentation live in [`library-docs`](https://github.com/code-corhuila/library-docs).

Implements `ADR-007-gateway-auth-and-rate-limiting.md`: a cheap, stateless
"Authorization header present or not" filter on every protected route (fast 401, never a JWT
parse — real validation stays inside each service, unchanged from `ADR-003`), plus real rate
limiting (`limit_req`: 5 req/min/IP on login, 60 req/min/IP on the rest of the API, both returning
`429` with `Retry-After: 60`).

## Structure

```
deploy/
├── nginx.conf   → routing, the ADR-007 auth-header filter, and the two rate-limit zones
├── Dockerfile   → nginx:1.27-alpine, no build stage (pure configuration, no app code)
└── compose.yml  → standalone preview — see lms-pod/compose.yml locally for the full assembly
```

## Migration scope

**Comes from** `lms-library` → `infra/nginx/nginx.conf`.

Routes `/api/v1/auth/login` → access, `/api/v1/students` → membership, `/api/v1/books` →
catalog, `/api/v1/loans` → circulation (all four backends now exist — the monolith's original
file predates Circulation and Access being extracted, so this is a real update, not a straight
copy). Also fronts `shell` (`lms-front`'s own container) for everything else, so the browser only
ever talks to one origin.

Convention already in use: each domain adds its own `location` block as it comes online.

**Known, not-yet-closed overlap:** `lms-front`'s own `deploy/nginx.conf` still has a local-only
`/api/v1/*` stand-in proxy (labeled "`lms-api-gateway` doesn't exist yet" in its own comments).
Per `ADR-006`'s cut-over criterion, that stand-in stays in place — and is what's actually used
when a portal is previewed on its own dev server — until whatever assembles the full system
(`lms-infra`, or locally, `lms-pod/compose.yml`) actually routes browser traffic through this
gateway instead of directly to `shell`. Removing it before that happens would be premature.

The full map lives in `library-docs`.

---

## Branching

Three permanent branches. **None of them accepts a direct commit** — you enter through a child
branch and leave through a Pull Request.

```
develop  <--PR--  feat/... fix/... chore/...
qa       <--PR--  qa/...
main     <--PR--  release/...  hotfix/...
```

Promotion happens **by re-application** (`git cherry-pick -x`), never by merging one permanent
branch into another: `merge develop -> qa` and `merge qa -> main` do not exist in this model.

`main` requires **1 approval from `ariel5253`**. On `develop` and `qa` the team sets its own review
rule.

Full policy: `00-governance/branching-policy.md` in `library-docs`.
