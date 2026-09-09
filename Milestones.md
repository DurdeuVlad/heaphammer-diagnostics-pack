# Milestones.md

Status: M1 complete; M2 has not started.

## M1: v0.1.0 — Fabric 1.21.1 pack builds and installs

- **Outcome:** A packwiz-defined pack (`pack.toml` + `index.toml`) containing
  HeapHammer, Spark, Lithium, FerriteCore, and Krypton installs cleanly on a
  vanilla Fabric 1.21.1 server and boots without mod-loading errors.
- **In scope:** packwiz project scaffold, pinned mod versions, a README
  documenting the soak-test workflow (`/hh doctor` → `/spark profiler start`
  → `/hh run ...` → `/spark profiler stop` → `/hh report show last`).
- **Out of scope:** CurseForge/Modrinth publication (M2); NeoForge variant;
  Starlight/C2ME.
- **Dependencies:** HeapHammer 1.0.0 Fabric-1.21.1 jar (already built and
  published, per the sibling HeapHammer repo).
- **Risks:** packwiz mod-pin metadata (hashes/version IDs) going stale if the
  bundled mods release updates before this milestone ships.
- **Evidence of done:** a server operator can follow the README end-to-end
  on a clean Fabric 1.21.1 server and get a HeapHammer verdict with a
  correlated Spark profile, with no manual mod hunting. See
  [Testing.md](Testing.md#m1-acceptance-checklist) for the exact checklist
  and commands that prove this.
- **Owner:** dwurdy (sole maintainer).
- **Status:** complete (verified 2026-09-09; see Testing.md).

## M2: Publish to CurseForge and Modrinth

- **Outcome:** The pack is a public, moderator-approved project on both
  platforms, discoverable alongside the HeapHammer mod itself.
- **In scope:** project pages (description, license, images), moderation
  review, cross-linking from the HeapHammer mod pages.
- **Out of scope:** CurseForge Server Affiliation Program enrollment (open
  question in Decision.md — not committed to yet).
- **Dependencies:** M1 complete.
- **Risks:** moderation review timelines are outside this project's control
  (observed with the HeapHammer mod submissions).
- **Evidence of done:** live, approved project pages on both platforms.
- **Owner:** dwurdy.
- **Status:** not started.

## Deferred (explicitly out of current milestones)

- NeoForge 1.21.1 variant (Decision.md D-004).
- Starlight/Moonrise and C2ME inclusion (Decision.md D-003).
- Any admin/QoL/builder-tool expansion (Decision.md D-002).
