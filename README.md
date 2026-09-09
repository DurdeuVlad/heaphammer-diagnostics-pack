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
| [HeapHammer](https://github.com/DurdeuVlad/heaphammer) | leak/regression detector — the reason this pack exists |
| [Spark](https://modrinth.com/mod/spark) | profiler — CPU/tick/heap visibility during a HeapHammer run |
| [Lithium](https://modrinth.com/mod/lithium) | optimizer — vanilla logic rewrite, no behavior change |
| [FerriteCore](https://modrinth.com/mod/ferrite-core) | optimizer — memory dedup |
| [Krypton](https://modrinth.com/mod/krypton) | optimizer — network stack |

Target: **Fabric 1.21.1** only, for now (see Decision.md D-004).

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

The five mod pins are committed in `index.toml` and `mods/*.pw.toml`. To
recreate or update them from the repo root:

```bash
# install packwiz, then from this directory. HeapHammer is sourced from its
# GitHub release because no Modrinth project exists for the published jar:
packwiz github add DurdeuVlad/heaphammer --regex 'heaphammer-1\.21\.1-1\.0\.0\.jar'
packwiz modrinth add spark
packwiz modrinth add lithium
packwiz modrinth add ferrite-core
packwiz modrinth add krypton
```

These commands generate/update `index.toml` and per-mod `.pw.toml` files with
real, verifiable hashes and version pins — nothing here fabricates those by
hand.

## Status

M1 implementation in progress: the five mods are pinned; server boot and the
soak-test workflow are still being verified. See
[Milestones.md](Milestones.md#m1-v010--fabric-1211-pack-builds-and-installs).
