# Business.md

Status: draft

## Problem

Diagnosing a Minecraft server memory leak today means running the server for
hours under real player load, watching RAM climb, and guessing which of
potentially dozens of installed mods is responsible. Admin-facing threads and
issue trackers show this pattern repeatedly: server operators concluding
"it's probably just mods in general" after days of manual monitoring, because
no single mod's install README tells them how to isolate the cause.

[HeapHammer](https://github.com/dwurdy/HeapHammer) (sibling project,
`E:\Github2\HeapHammer`) already collapses that multi-hour blind loop into a
~2 minute on-demand stress cycle with a PASS/PASS (plateau)/SUSPICIOUS
verdict. What it doesn't give an admin is the *why* behind a SUSPICIOUS
verdict — that's a profiler's job — or confidence that the performance mods
they're already running (or should be running) aren't themselves the noise
in the reading.

## Who this is for

Self-hosted / small-to-mid Minecraft server admins who:
- run modded servers and want to rule out (or catch) a memory leak before it
  crashes their server during peak hours, and
- already reach for optimization mods (Lithium, FerriteCore, Krypton have
  60M+ / 70M+ downloads individually) but have no bundled, one-install way to
  combine them with a profiler and an active leak test.

## What it is

A Fabric 1.21.1 Minecraft modpack, distributed via packwiz (`pack.toml`),
bundling:
- **HeapHammer** — the leak/regression detector (the reason this pack exists)
- **Spark** — profiler, so a SUSPICIOUS verdict comes with a flame graph and
  heap summary instead of just a verdict string
- **Lithium, FerriteCore, Krypton** — widely-used, behavior-preserving
  optimizers, included so the pack also serves as a "these are safe to run
  together" baseline verified against HeapHammer's own readings

Market check (2026-09-09 research pass): no existing modpack on
CurseForge/Modrinth combines a profiler + tested optimizers + an active leak
detector. Comparable packs are either FPS/client-focused ("Forge's
Optimization Collection") or admin/QoL bundles that include optimizers but
never a profiler or leak-detection tool ("Server Essential Mods"). This gap
is the pack's reason to exist, not a hypothesis — see
[Decision.md](Decision.md) for the version-target and scope decisions this
led to.

## Usage flow (happy path)

1. Admin installs the pack on a Fabric 1.21.1 server.
2. Admin runs `/hh doctor` to confirm server readiness.
3. Admin starts `/spark profiler start`, then runs a HeapHammer stress cycle
   (`/hh run chunks --iterations=5 ...`), then `/spark profiler stop`.
4. Admin reads HeapHammer's verdict (`/hh report show last`) alongside
   Spark's flame graph/heap summary for the same window.
5. If SUSPICIOUS, admin now has both "memory kept climbing" and "here's what
   was running when it did" in one soak-test session, without having to
   install and correlate multiple standalone mods.

## Failure paths / non-goals

- Not a general-purpose "everyday server" pack (no admin/permission mods, no
  builder tools, no QoL content) — scope creep into that space is explicitly
  rejected; see Decision.md.
- Not a leak-bait/repro pack (i.e. does not intentionally bundle
  leak-prone mods to demonstrate HeapHammer catching them) — that idea was
  considered and dropped in favor of this profiler+optimizer framing.
- Does not currently support Forge or NeoForge, even though HeapHammer's own
  jar does — deferred, not out of scope permanently.

## Assumptions

- The user (dwurdy) is the sole maintainer of both HeapHammer and this pack.
- CurseForge/Modrinth publication is the intended distribution channel,
  mirroring how HeapHammer itself was published (see the HeapHammer repo).

## Open questions

- Exact success metric for "the pack is working" (e.g. install count, or
  qualitative — "someone used it to find a real leak") is not yet defined.
  Record this in Milestones.md once a release exists to measure.
