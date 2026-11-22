# Xerenge — update & build notes

## Remotes

| Remote | URL | Purpose |
|---|---|---|
| `origin` | `https://github.com/kiequoo/Xerenge.git` | arm64 fork |
| `upstream` | `https://github.com/shipa-2/Xerenge.git` | upstream |

The arm64 fork carries two patches on top of upstream:
- **`scripts/platform.sh`**: detect `aarch64`/`arm64` on Linux and set `XR_PRESET=linux-arm64`
- **`scripts/build.sh`**: set `-march=armv8-a` when `XR_PRESET` is `linux-arm64` (same as the existing `mac-arm64` branch)

The `rexglue/CMakePresets.json` and the SDK already define `linux-arm64-*` presets; these two script changes are all that's needed.

## Pulling a new upstream release

```bash
git fetch upstream
git pull --rebase upstream main
git push origin main --force-with-lease
```

## Building on the Odin 3 (aarch64, 192.168.68.30 / .31 — IP can change)

The build container on the Odin 3 for red_two is `redtwo-build` (Fedora 41, clang 19).
Xerenge requires clang 20 (see `rexglue/CMakePresets.json` `linux-base`), so a separate
container is needed.

### Sync source

```bash
rsync -az --exclude='rexglue-sdk/' --exclude='rexglue/build/' --exclude='rexglue/generated/' --exclude='.git/' \
  /home/kiequoo/dev/Xerenge/ armada@192.168.68.31:~/Xerenge/
```

### Setup (first time)

```bash
ssh armada@192.168.68.31 "cd ~/Xerenge && ./scripts/setup-deps.sh && ./scripts/setup-sdk.sh"
```

### Build

```bash
ssh armada@192.168.68.31 "cd ~/Xerenge && ./scripts/build.sh --game <path-to-game>"
```

Binary lands at `~/Xerenge/rexglue/build/burnout` on the Odin 3.
