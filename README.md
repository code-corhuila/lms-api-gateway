# lms-api-gateway

> Single entry point: authentication, routing and rate limiting

Part of the **LMS Library** distributed system — team `lms-library`, Grupo 2.
Governance and documentation live in [`library-docs`](https://github.com/code-corhuila/library-docs).

## Migration scope

**Comes from** `lms-library` → `infra/nginx/nginx.conf`.

It already routes `/api/v1/auth` → access, `/api/v1/students` → membership, `/api/v1/books` → catalog.
**`/api/v1/loans` → circulation does not exist yet** — the web UI for loans has no backend behind it.

Convention already in use: each domain adds its own `location` block as it comes online.

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
