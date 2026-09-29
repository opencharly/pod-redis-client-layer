# pod-redis-client-layer

The `redis-client-layer` candy of the OpenCharly candy library, as a standalone
repo (the candy de-submodule cutover, kind-prefixed naming). It installs
`redis-cli` for cross-pod redis access.

## What it provides

Provides `/usr/bin/redis-cli` so a harness can run
`redis-cli -h charly-redis SET ...` and verify cross-pod connectivity. No redis
service is started in this image — it acts purely as a client — but a
`keep-alive` supervisord service (`sleep infinity`, `restart: always`) holds the
pod at steady state.

| Property | Value |
|---|---|
| Service | `keep-alive` (`/usr/bin/sleep infinity`, `restart: always`, priority 10) |
| Client | `/usr/bin/redis-cli` (package `valkey-compat-redis` on Fedora 43) |
| Tools | `ss` (iproute), `pgrep` (procps-ng) |
| Distros | `fedora` |

## How to use it

Compose it as the client side of a cross-pod redis test:

```yaml
my-redis-client:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/pod-redis-client-layer:<tag>'
```

```bash
charly box build my-redis-client
charly start my-redis-client
charly shell my-redis-client -c "redis-cli -h charly-redis SET bench:hello world"
```

Pair it with `pod-redis-server-layer` (which binds `redis-server` to `0.0.0.0`)
on the shared `charly` network.

## Layout

- `charly.yml` — the `redis-client-layer:` candy entity (description, `distro`,
  `service`, `plan`). No `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- `/charly-infrastructure:redis` — the family redis candy and its owning skill.
- `/charly-image:layer` — the candy authoring reference.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
