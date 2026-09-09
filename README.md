# HeapHammer Diagnostics Pack

A Fabric 1.21.1 Minecraft modpack for server admins: a one-install soak-test
toolkit combining [HeapHammer](https://github.com/dwurdy/HeapHammer)
(on-demand memory-leak detector) with a profiler and a small set of
tested-safe optimizers, so a SUSPICIOUS verdict comes with a flame graph
attached instead of just a string.

See [Business.md](Business.md) for the problem this solves and who it's for,
[Decision.md](Decision.md) for why this mod set and version target were
chosen, and [Milestones.md](Milestones.md) for what "done" looks like.

## What's bundled

| Mod | Role |
|---|---|
| [HeapHammer](https://www.curseforge.com/minecraft/mc-mods/heaphammer) | leak/regression detector — the reason this pack exists |
| [Spark](https://www.curseforge.com/minecraft/mc-mods/spark) | profiler — CPU/tick/heap visibility during a HeapHammer run |
| [Lithium](https://www.curseforge.com/minecraft/mc-mods/lithium) | optimizer — vanilla logic rewrite, no behavior change |
| [FerriteCore](https://www.curseforge.com/minecraft/mc-mods/ferrite-core) | optimizer — memory dedup |
| [Krypton](https://www.curseforge.com/minecraft/mc-mods/krypton) | optimizer — network stack |

Target: **Fabric 1.21.1** only, for now (see Decision.md D-004). The pack
pins Fabric Loader 0.19.5 and Fabric API as required platform dependencies;
Fabric API is not an additional user-facing feature mod.

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

The five requested mod pins and the required Fabric API platform pin are
committed in `index.toml` and `mods/*.pw.toml`. To recreate or update them
from the repo root:

```bash
# install packwiz, then from this directory. All six pins are CurseForge-sourced
# so the CurseForge export references every mod by project/file ID (CurseForge
# moderation rejects packs that bundle CurseForge-available mods as overrides).
# The resolved CurseForge project/file IDs are recorded in each mods/*.pw.toml.
packwiz curseforge add heaphammer
packwiz curseforge add spark
packwiz curseforge add lithium
packwiz curseforge add ferritecore
packwiz curseforge add krypton
packwiz curseforge add fabric-api
```

These commands generate/update `index.toml` and per-mod `.pw.toml` files with
real, verifiable hashes and version pins — nothing here fabricates those by
hand. HeapHammer is not yet approved on Modrinth (see Decision.md's open
follow-up); the CurseForge pin is the single source for both exports until
that lands.

## Status

M1 complete: the five requested mods are pinned, the pack boots on a real
Fabric 1.21.1 server, and the documented soak-test workflow produced both a
HeapHammer verdict and a Spark profile. See
[Milestones.md](Milestones.md#m1-v010--fabric-1211-pack-builds-and-installs).
