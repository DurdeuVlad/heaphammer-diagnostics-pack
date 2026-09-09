# Testing.md

Status: M1 implementation in progress. Step 1 has passed empirically; server
boot and the soak-test workflow are not yet verified.

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
- Network access to Modrinth (packwiz resolves mods from there).

## Step 1 — Mod resolution

Run, from the repo root:

```bash
packwiz github add DurdeuVlad/heaphammer --regex 'heaphammer-1\.21\.1-1\.0\.0\.jar'
packwiz modrinth add spark
packwiz modrinth add lithium
packwiz modrinth add ferrite-core
packwiz modrinth add krypton
```

**Pass condition:** all five commands exit 0 and `index.toml` plus five
`mods/*.pw.toml` files are created/updated with real hashes.

**Observed implementation notes (2026-09-09):** The existing scaffold did
not contain `index.toml`; the current packwiz build also refuses `modrinth
add` and `refresh` until that index exists, so an empty sha256 index was
initialized before the add commands. `packwiz modrinth add heaphammer`
returned `no projects found`; the Modrinth API also returned 404 for
`heaphammer` and no search hits. The exact Fabric 1.21.1 jar was therefore
added with `packwiz github add DurdeuVlad/heaphammer` using the release asset
regex above. The four Modrinth commands exited 0; `ferrite-core` resolved as
written (the downloaded filename is `ferritecore-7.0.3-fabric.jar`).

**Fail condition and what to do:** if a slug in README.md doesn't resolve
(e.g. `ferrite-core` might actually be `ferritecore` on Modrinth, or a mod
requires picking a specific game-version/loader match), that is expected —
these slugs were not verified against the live Modrinth API when the docs
were written. Correct the slug in both `README.md` and the command actually
used, note the correction in [Decision.md](Decision.md) as a small addendum
(not a full D-entry — a one-line "corrected slug X to Y, source: packwiz
output" is enough), and continue. Do not silently drop a mod because its
slug didn't match on the first try.

## Step 2 — Server boot test

1. Serve the pack with `packwiz serve` and install it into the disposable
   server with `packwiz-installer-bootstrap` using the local `pack.toml` URL.
   This is the standard packwiz server-install path selected for M1; record
   the exact command and observed result below once done.
2. Boot a vanilla Fabric 1.21.1 server with the resolved mods installed and
   `eula.txt` accepted.
3. Watch the log to `Done (...)! For help, type "help"` with no mod-loading
   exceptions, no missing-dependency errors, and no crash.

**Pass condition:** clean boot, server reaches the "Done" line, `/hh` and
`/spark` commands are recognized (tab-complete or a plain `/hh` with no
"unknown command" response confirms both mods loaded).

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
the same window, in one session, with no other mod installed. If either
half is missing (no verdict, or no profile), M1 is not done regardless of
whether Steps 1–2 passed.

## What is explicitly NOT required for M1

- A deliberately-seeded leak scenario (that's the leak-bait pack idea that
  was considered and rejected — see Decision.md D-001). A PASS verdict on a
  clean install is sufficient acceptance evidence; you do not need to prove
  HeapHammer catches a leak here, since that's HeapHammer's own test
  responsibility, already covered in its own repo (86 automated tests, per
  its published description).
- CurseForge/Modrinth publication (that's M2, separate milestone).

## M1 acceptance checklist

- [ ] Step 1: all 5 mods resolved via packwiz, `index.toml` committed
- [ ] Step 2: clean server boot on Fabric 1.21.1, no mod-loading errors
- [ ] Step 3: soak-test workflow produces both a HeapHammer verdict and a
      Spark profile URL for the same run
- [ ] Any slug corrections or install-method decisions made along the way
      are recorded in Decision.md or this file, not left implicit
- [ ] README.md updated if any command in it needed correcting
