# selfservice-crucible-runner

Kali-based Crucible assessment runner extracted from `selfservice-api`.

This repository owns the `crucible-runner` binary, the `actions.sh` library, the curated tool inventory (`runnertools/tools.txt`), and the GHCR image `ghcr.io/jmal1/selfservice-crucible-runner`.

This repo is **public**. Production host topology and Helm overlays stay in private [`jmal1/crucible-deploy`](https://github.com/jmal1/crucible-deploy). Instructor troubleshooting docs use placeholder hostnames only.

## Why a separate repo

The assessment runner image is large (Kali Rolling + curated tools) and rebuilds on a different cadence than the Alpine API/worker/engine images. Splitting it lets:

- API/engine depend on this module for shared Go contracts (`runner`, `runnertools`)
- CI publish a single SHA-tagged runner image without rebuilding the full API fleet
- Deploy pin the runner digest independently via `crucible-deploy` overlays

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

- Engine / API: [`jmal1/selfservice-api`](https://github.com/jmal1/selfservice-api)
- Deploy pins (private): [`jmal1/crucible-deploy`](https://github.com/jmal1/crucible-deploy)

