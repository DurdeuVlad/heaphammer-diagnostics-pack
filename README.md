# HeapHammer Diagnostics Pack

A Fabric Minecraft modpack for server admins: a one-install soak-test
toolkit combining [HeapHammer](https://github.com/dwurdy/HeapHammer)
(on-demand memory-leak detector) with a profiler and a small set of
tested-safe optimizers, so a SUSPICIOUS verdict comes with a flame graph
attached instead of just a string.

See [Business.md](Business.md) for the problem this solves and who it's for,
[Decision.md](Decision.md) for why this mod set and version target were
chosen, and [Milestones.md](Milestones.md) for what "done" looks like.

## Supported versions

| MC Version | Fabric Loader | Java | Pack directory |
|---|---|---|---|
| 1.16.5 | 0.15.11 | 17 | `packs/1.16.5/` |
| 1.18.2 | 0.15.11 | 17 | `packs/1.18.2/` |
| 1.19.2 | 0.15.11 | 17 | `packs/1.19.2/` |
| 1.20.1 | 0.15.11 | 17 | `packs/1.20.1/` |
| 1.21.1 | 0.19.5 | 21 | `packs/1.21.1/` |
| 1.21.4 | 0.16.10 | 21 | `packs/1.21.4/` |

Forge versions (1.7.10, 1.12.2) are not supported — the optimizer mods
(Lithium, Krypton, FerriteCore) are Fabric-only (see Decision.md D-003,
D-004 supersession).

## What's bundled

| Mod | Role |
|---|---|
| [HeapHammer](https://www.curseforge.com/minecraft/mc-mods/heaphammer) | leak/regression detector — the reason this pack exists |
| [Spark](https://www.curseforge.com/minecraft/mc-mods/spark) | profiler — CPU/tick/heap visibility during a HeapHammer run |
| [Lithium](https://www.curseforge.com/minecraft/mc-mods/lithium) | optimizer — vanilla logic rewrite, no behavior change |
| [FerriteCore](https://www.curseforge.com/minecraft/mc-mods/ferrite-core) | optimizer — memory dedup |
| [Krypton](https://www.curseforge.com/minecraft/mc-mods/krypton) | optimizer — network stack |

All five mods plus Fabric API are present in every version's pack. Fabric
API is a required platform dependency, not an additional user-facing
feature mod. Per-version mod versions are recorded in each
`packs/<version>/mods/*.pw.toml`.

## Soak-test workflow

```
/hh doctor
/spark profiler start
/hh run chunks --iterations=5 --batch=10 --radius=8 --strategy=spiral
/spark profiler stop
/hh report show last
```

Read the HeapHammer verdict (PASS / PASS (plateau) / SUSPICIOUS) alongside
the Spark flame graph/heap summary for the same window. If you're chasing a
leak after adding a new mod, run the cycle before and after and diff with
`/hh report diff <runA> <runB>`.

## Building the pack

This project uses [packwiz](https://packwiz.infra.link/) (`pack.toml` +
`index.toml`, git-friendly TOML) as the single source that exports to both
CurseForge and Modrinth formats.

Each version's pack lives in `packs/<version>/` with its own `pack.toml`,
`index.toml`, and `mods/` directory. To recreate or update a specific
version's mod pins, from the repo root:

```bash
# install packwiz, then for each version (e.g. 1.20.1):
cd packs/1.20.1
packwiz curseforge add heaphammer
packwiz curseforge add spark
packwiz curseforge add lithium
packwiz curseforge add ferritecore
packwiz curseforge add krypton
packwiz curseforge add fabric-api
```

All six pins are CurseForge-sourced so the CurseForge export references
every mod by project/file ID (CurseForge moderation rejects packs that
bundle CurseForge-available mods as overrides). The resolved CurseForge
project/file IDs are recorded in each `mods/*.pw.toml`. HeapHammer is not
yet approved on Modrinth (see Decision.md's open follow-up); the CurseForge
pin is the single source for both exports until that lands.

## Status

M1 complete (1.21.1 verified): the five requested mods are pinned, the
pack boots on a real Fabric 1.21.1 server, and the documented soak-test
workflow produced both a HeapHammer verdict and a Spark profile. M3
multi-version expansion complete for 5 additional versions (1.16.5,
1.18.2, 1.19.2, 1.20.1, 1.21.4) — hash-consistency verified, server-boot
verification pending. See
[Milestones.md](Milestones.md).
