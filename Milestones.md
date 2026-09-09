# Milestones.md

Status: M1 complete; M3 complete (all 6 packs boot-verified under Java 21,
v0.1.0 tagged and released); M2 in progress (export artifacts produced
for all 6 packs; CurseForge/Modrinth platform submission pending).

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
- **Dependencies:** M1 complete; M3 complete (for all 6 versions).
- **Risks:** moderation review timelines are outside this project's control
  (observed with the HeapHammer mod submissions).
- **Evidence of done:** live, approved project pages on both platforms.
- **Owner:** dwurdy.
- **Status:** in progress (all 6 mods re-sourced to CurseForge; 12 export
  artifacts produced and boot-verified for all 6 versions; repo public;
  v0.1.0 GitHub release published; CurseForge/Modrinth platform
  submission pending — see PUBLISH-PROMPT.md for browser handoff).

## M3: Multi-version expansion (1.16.5 – 1.21.4)

- **Outcome:** The pack supports 6 Fabric MC versions (1.16.5, 1.18.2,
  1.19.2, 1.20.1, 1.21.1, 1.21.4), each with the full 5-mod set + Fabric
  API, in per-version subdirectories under `packs/`.
- **In scope:** restructure repo to per-version subdirectories; pin all 6
  mods for each version via `packwiz curseforge add`; hash-consistency
  verification for all 6 packs; update docs.
- **Out of scope:** Forge versions (1.7.10, 1.12.2) — optimizer mods are
  Fabric-only; server-boot verification for the 5 new versions (next step
  before M2 publication).
- **Dependencies:** M1 complete; HeapHammer branches for all 6 versions
  (confirmed: `ver/1.16.5`, `ver/1.18.2`, `ver/1.19.2`, `ver/1.20.1`,
  `master` for 1.21.1, `ver/1.21.4`).
- **Risks:** mod version availability gaps (mitigated — all 5 mods + Fabric
  API confirmed available for all 6 versions); Java runtime mismatch for
  1.16.5 (HeapHammer requires Java 17, not Java 8).
- **Evidence of done:** all 6 packs have 6 mods, all hashes match, all
  index hashes match pack.toml. See
  [Testing.md](Testing.md#m3-multi-version-expansion--per-version-mod-matrix)
  for the per-version mod matrix and verification checklist.
- **Owner:** dwurdy.
- **Status:** complete (all 6 packs boot-verified under Java 21;
  v0.1.0 tagged at commit `0f21d31` and released with 12 artifacts;
  repo public at https://github.com/DurdeuVlad/heaphammer-diagnostics-pack;
  release at https://github.com/DurdeuVlad/heaphammer-diagnostics-pack/releases/tag/v0.1.0).

## Deferred (explicitly out of current milestones)

- NeoForge 1.21.1 variant (Decision.md D-004).
- Starlight/Moonrise and C2ME inclusion (Decision.md D-003).
- Any admin/QoL/builder-tool expansion (Decision.md D-002).
- Forge versions 1.7.10 and 1.12.2 (optimizer mods are Fabric-only).
