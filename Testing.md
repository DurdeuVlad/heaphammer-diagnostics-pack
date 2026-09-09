# Testing.md

Status: M1 complete (2026-09-09). Steps 1–3 passed empirically on a
disposable Fabric 1.21.1 server; the final soak test produced both a
HeapHammer verdict and a Spark profile URL.

Test strategy for M1 in [Milestones.md](Milestones.md). This project has no
application code to unit-test — "testing" here means proving the pack
resolves, boots, and actually produces the workflow it promises, on a real
server. Do not claim any step below is done without having run the actual
command and read its output; do not infer a PASS from reading the config.

## Pre-requisites

- packwiz CLI installed. The current official build has no `version` command
  or `--version` flag; `packwiz version` and `packwiz --version` were both
  run and rejected as unsupported. CLI availability was instead verified by
  `packwiz --help` and the successful mod-resolution commands below.
- A Java 21 runtime available (Minecraft 1.21.1 requires Java 21) to run a
  throwaway Fabric server for verification — this does not need to be a
  production server, a local disposable install is enough.
- Network access to CurseForge (packwiz resolves mods from there). A
  CurseForge API key may be required for some `packwiz curseforge` operations
  per the packwiz docs; the add commands below were verified to resolve
  without one for these six projects.

## Step 1 — Mod resolution

Run, from the repo root:

```bash
packwiz curseforge add heaphammer
packwiz curseforge add spark
packwiz curseforge add lithium
packwiz curseforge add ferritecore
packwiz curseforge add krypton
packwiz curseforge add fabric-api
```

**Pass condition:** all five requested-mod commands exit 0 and `index.toml`
plus five `mods/*.pw.toml` files are created/updated with real hashes. Any
required platform dependency is pinned as an additional metadata file.

**Observed implementation notes (2026-09-09, M1):** The existing scaffold did
not contain `index.toml`; the current packwiz build also refuses `modrinth
add` and `refresh` until that index exists, so an empty sha256 index was
initialized before the add commands. `packwiz modrinth add heaphammer`
returned `no projects found`; the Modrinth API also returned 404 for
`heaphammer` and no search hits. The exact Fabric 1.21.1 jar was therefore
added with `packwiz github add DurdeuVlad/heaphammer` using a release asset
regex. The four Modrinth commands exited 0; `ferrite-core` resolved as
written (the downloaded filename is `ferritecore-7.0.3-fabric.jar`). The first
boot also proved that the published HeapHammer jar requires Fabric API, so
`packwiz modrinth add fabric-api` was run and resolved to
`fabric-api-0.116.17+1.21.1.jar` as a platform dependency.

**Observed implementation notes (2026-09-09, M2-2 re-sourcing):** HeapHammer
was subsequently approved on CurseForge
(https://www.curseforge.com/minecraft/mc-mods/heaphammer), unblocking a
CurseForge re-pin. All six mods were re-pinned from their original sources
(HeapHammer: GitHub; the other five: Modrinth) to CurseForge using the
commands above, so `packwiz curseforge export` references every mod by
project/file ID instead of bundling it as a JAR override (CurseForge
moderation rejects packs that bundle CurseForge-available mods as
overrides). The CurseForge file for HeapHammer is named
`heaphammer-1.0.0-MC1.21.1-Fabric-NeoForge.jar` (a Fabric+NeoForge universal
build) rather than the GitHub release's Fabric-only
`heaphammer-1.21.1-1.0.0.jar`; both are the same 1.0.0 / MC 1.21.1 release,
the version target is unchanged, and no requested mod was substituted or
removed. The re-sourcing approach (single CurseForge source for both exports,
rather than dual CurseForge+Modrinth update sections) and its tradeoff
(Modrinth export bundles the five CurseForge-sourced mods as JARs, losing
Modrinth update-tracking for them) is recorded in Decision.md. The open
Decision.md follow-up to re-pin HeapHammer to Modrinth once approved remains
open and is not closed by this re-sourcing.

**Fail condition and what to do:** if a slug in README.md doesn't resolve
(e.g. `ferritecore` vs `ferrite-core` across platforms), that is expected —
correct the slug in both `README.md` and the command actually used, note the
correction in [Decision.md](Decision.md) as a small addendum
(not a full D-entry — a one-line "corrected slug X to Y, source: packwiz
output" is enough), and continue. Do not silently drop a mod because its
slug didn't match on the first try.

## Step 2 — Server boot test

1. Serve the pack with `packwiz serve` and install it into the disposable
   server with `packwiz-installer-bootstrap` using the local `pack.toml` URL.
   This is the standard packwiz server-install path selected for M1; the
   exact commands and observed result are recorded below.
2. Boot a vanilla Fabric 1.21.1 server with the resolved mods installed and
   `eula.txt` accepted.
3. Watch the log to `Done (...)! For help, type "help"` with no mod-loading
   exceptions, no missing-dependency errors, and no crash.

**Pass condition:** clean boot, server reaches the "Done" line, and
`/hh doctor` plus `/spark profiler start` are recognized and produce their
respective mod output.

**Observed implementation notes (2026-09-09):** The selected install path was:

```text
.\.packwiz-tools\bin\packwiz.exe serve --port 19090 --refresh=false
java -jar ..\tools\packwiz-installer-bootstrap.jar -g -s server http://127.0.0.1:19090/pack.toml
```

The installer exited 0 and downloaded all five requested mod JARs plus Fabric
API into `.m1-server/fresh-0195`. The first boot against the scaffold's
Fabric Loader 0.16.9 failed with `Mod resolution failed`: HeapHammer required
Fabric Loader `>=0.19.5` and Fabric API was missing. That exact failure is
recorded in Decision.md; no requested mod was replaced. After pinning Loader
0.19.5 and Fabric API, the clean boot log reported `Loading 49 mods`, listed
HeapHammer, Spark, Lithium, FerriteCore, and Krypton, and reached
`Done (1.282s)! For help, type "help"` with no mod-loading exception. The
server process exited 0 after the verification session.

**Fail condition:** if the server crashes or a mod fails to load, capture
the exact log line and file it as an issue in this repo (not silently patch
around it) — per the mod-set decisions in Decision.md D-003, do not
substitute a different optimizer without going through the same reasoning
recorded there.

## Step 3 — Soak-test workflow verification

As a server operator (op level 2+) or from console, in order:

```
/hh doctor
/spark profiler start
/hh run chunks --iterations=5 --batch=10 --radius=8 --strategy=spiral
/spark profiler stop
/hh report show last
```

**Pass condition:**
- `/hh doctor` reports server readiness with no blocking errors.
- The stress cycle completes (HeapHammer's own tick-budget guarantees this
  won't hang or lag the server — see the HeapHammer repo's `AGENTS.md` §3.3
  if this is violated, since that would be a HeapHammer bug, not a pack
  bug).
- `/hh report show last` prints a verdict (PASS / PASS (plateau) /
  SUSPICIOUS) with a slope and R² value.
- `/spark profiler stop` produces a `spark.lucko.me` URL with a flame graph
  covering the same time window as the HeapHammer run.

**This is the actual product being shipped** — a verdict *and* a profile for
the same window, in one session, with no mods outside this pack installed.
If either half is missing (no verdict, or no profile), M1 is not done
regardless of whether Steps 1–2 passed.

**Observed implementation notes (2026-09-09):** From the server console, the
commands were run in the documented order (console syntax omits the leading
`/`). `/hh doctor` reported `Server status: READY` and 841 loaded chunks. The
chunk run completed as `hh-20260909-084845-5612`; `/hh report show last`
reported `Status: COMPLETED | Verdict: PASS`, slope `-2.29 MB/cycle`, and
`R² = 0.02`. Spark reported `Profiler stopped & upload complete!` and emitted
the profile URL [https://spark.lucko.me/1P5tmAZSNn](https://spark.lucko.me/1P5tmAZSNn),
which returned HTTP 200 when checked from the same network.

## What is explicitly NOT required for M1

- A deliberately-seeded leak scenario (that's the leak-bait pack idea that
  was considered and rejected — see Decision.md D-001). A PASS verdict on a
  clean install is sufficient acceptance evidence; you do not need to prove
  HeapHammer catches a leak here, since that's HeapHammer's own test
  responsibility, already covered in its own repo (86 automated tests, per
  its published description).
- CurseForge/Modrinth publication (that's M2, separate milestone).

## M1 acceptance checklist

- [x] Step 1: all 5 requested mods resolved via packwiz, `index.toml`
      committed; Fabric API is pinned as the required platform dependency
- [x] Step 2: clean server boot on Fabric 1.21.1, no mod-loading errors
- [x] Step 3: soak-test workflow produces both a HeapHammer verdict and a
      Spark profile URL for the same run
- [x] Any slug corrections or install-method decisions made along the way
      are recorded in Decision.md or this file, not left implicit
- [x] README.md updated if any command in it needed correcting
