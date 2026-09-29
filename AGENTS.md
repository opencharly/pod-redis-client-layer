# AGENTS.md — pod-redis-client-layer

Standalone candy repo for the `redis-client-layer` candy — a `redis-cli` client
image that holds a keepalive service for cross-pod redis probes. The entire candy
lives in `charly.yml` at the repo root. There is no source tree.

Canonical files:

- `charly.yml` — the `redis-client-layer:` candy entity (description, `distro`,
  `service`, `plan`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:redis` — the family redis candy: the distro package
  divergence (`valkey-compat-redis` on Fedora) and the client binaries. Load
  before editing, building, or troubleshooting this candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, service declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-internals:git-workflow` — before any git/PR action.

The candy carries no `skill:` entity of its own; the family skill
`/charly-infrastructure:redis` covers the surface. The gap is routed to the named
skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when that lands, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert the `redis-cli` binary, the
  `valkey-compat-redis` package, `redis-cli --version`, the `ss` and `pgrep`
  tools, and the running `keep-alive` service.

## Modify this repo

- Edit the `redis-client-layer:` candy entity in `charly.yml`. The `keep-alive`
  service and the installed `redis-cli` are the product.
- A `package:` check must query the real installed name on Fedora 43
  (`valkey-compat-redis`), not the `redis` request name.
- Keep this image service-less for redis itself — it is a client only.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
