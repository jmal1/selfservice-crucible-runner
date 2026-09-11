# selfservice-crucible-runner

Kali-based Crucible assessment runner extracted from `selfservice-api`.

This repository owns the `crucible-runner` binary, the `actions.sh` library, the curated tool inventory (`runnertools/tools.txt`), and the GHCR image `ghcr.io/jmal1/selfservice-crucible-runner`.

## Why a separate repo

The assessment runner image is large (Kali Rolling + curated tools) and rebuilds on a different cadence than the Alpine API/worker/engine images. Splitting it lets:

- API/engine depend on this module for shared Go contracts (`runner`, `runnertools`)
- CI publish a single SHA-tagged runner image without rebuilding the full API fleet
- Deploy pin the runner digest independently via `crucible-deploy` overlays

During the transition, `selfservice-api` may dual-build the same image briefly so production can keep deploying from the API SHA while consumers migrate to this module and image.

## Layout

| Path | Role |
| --- | --- |
| `cmd/crucible-runner/` | Runner entrypoint |
| `runner/` | Config, executor, callback, sidecar |
| `runnertools/` | Tool manifest + embed (`tools.txt`) |
| `actions.sh` | Action library sourced at `/opt/crucible/lib/actions.sh` |
| `Dockerfile` | Multi-stage Go build + Kali runtime |

## Build

```bash
go test ./...
docker build -t ghcr.io/jmal1/selfservice-crucible-runner:local .
```

## Related

- Engine / API: `jmal1/selfservice-api`
- Deploy pins: `jmal1/crucible-deploy`

<!-- verified-merge noop 2026-09-11T10:48:36.6250431-07:00 -->

