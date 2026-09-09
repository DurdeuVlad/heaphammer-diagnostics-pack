# Decision.md

Durable decision log. Newest first. Status values: `decided`, `open`, `superseded`.

## Implementation addendum — 2026-09-09 (Fabric runtime compatibility)

- The first disposable-server boot failed before any mod could load because
  the scaffold's Fabric Loader 0.16.9 is below HeapHammer 1.0.0's declared
  minimum of 0.19.5. The same log showed that HeapHammer and Spark require
  Fabric API modules that were not present in the five standalone mod pins.
- The pack therefore targets Fabric Loader 0.19.5 and pins Fabric API
  0.116.17+1.21.1 as a platform dependency. Fabric API is not a sixth
  user-facing feature mod and no requested mod was substituted or removed;
  this is the minimum dependency correction required for the declared five
  mods to load on Fabric 1.21.1.

## Implementation addendum — 2026-09-09

- The live Modrinth API and packwiz both returned no project for `heaphammer`.
  The exact Fabric 1.21.1 `heaphammer-1.21.1-1.0.0.jar` release asset is
  therefore pinned with packwiz's native GitHub source support instead of
  substituting a different mod or version. The four optimizer/profiler pins
  remain Modrinth-sourced.

## Open follow-up: swap HeapHammer to its Modrinth source once approved

- **Status:** open, blocked on external moderation
- **Context:** HeapHammer was submitted to Modrinth on 2026-09-09 and is
  "Under review" — that's why it 404s from the public API today (see
  addendum above). This is expected, not a bug in either project.
- **Action once approved:** re-pin with
  `packwiz modrinth add heaphammer` (removing the GitHub-source pin first,
  `packwiz remove heaphammer` if packwiz requires that), confirm the
  resolved version matches the same `1.21.1-1.0.0` release, `packwiz refresh`,
  and update README.md's mod table/build commands back to a plain Modrinth
  add. Not urgent — the GitHub-source pin works correctly in the meantime and
  is not a degraded fallback, just a different (equally valid) packwiz
  source type.
- **Owner:** dwurdy — check HeapHammer's Modrinth project status
  periodically; not something this repo can detect on its own.

---

## D-005: Distribution format is packwiz, not a raw CurseForge/Modrinth manifest.zip

- **Status:** decided
- **Context:** Modpacks can ship as a CurseForge `manifest.json` export, a
  Modrinth `.mrpack`, or a packwiz `pack.toml` project that generates both.
- **Alternatives considered:** hand-maintained manifest.json per platform
  (rejected — two sources of truth, easy to drift); MultiMC instance export
  only (rejected — not portable to a dedicated server install).
- **Rationale:** packwiz stores the pack as git-friendly TOML, matches how
  the sibling HeapHammer repo is already version-controlled, and exports to
  both CurseForge and Modrinth formats from one source.
- **Consequences:** contributors need the `packwiz` CLI; there is a small
  learning curve versus editing a manifest.json by hand.
- **Revisit if:** packwiz stops being maintained, or CurseForge/Modrinth
  publishing workflows diverge enough that a single source stops being
  practical.

## D-004: Version target for v0.1.0 is Fabric 1.21.1 only

- **Status:** decided
- **Context:** HeapHammer itself ships 8 version/loader combinations (Forge
  1.7.10/1.12.2; Fabric 1.16.5–1.21.4; Fabric+NeoForge 1.21.1). A diagnostics
  pack could target any subset.
- **Alternatives considered:** matching all 8 HeapHammer targets at launch
  (rejected — Fabric has by far the deepest current optimizer ecosystem;
  spreading v0.1.0 across 8 targets means 8x the compatibility maintenance
  before the concept is even validated); NeoForge 1.21.1 as the primary
  target instead of Fabric (rejected — Fabric's Lithium/FerriteCore/Krypton
  are the canonical, most-downloaded versions of these optimizers; NeoForge
  equivalents are secondary ports).
- **Rationale:** 1.21.1 is HeapHammer's own primary dev target
  (`gradle.properties: minecraft_version=1.21.1`), so it needs no extra
  cross-version validation work on the HeapHammer side.
- **Consequences:** server admins on other versions/loaders aren't served by
  v0.1.0.
- **Revisit if:** v0.1.0 gets real usage and a NeoForge 1.21.1 variant
  becomes worth the added maintenance (the HeapHammer jar already supports
  it).

## D-003: Optimizer set is Lithium + FerriteCore + Krypton only — Starlight and C2ME excluded

- **Status:** decided
- **Context:** Fabric's optimizer ecosystem also includes Starlight/Moonrise
  (lighting engine rewrite) and C2ME (parallel chunk loading), both
  legitimate, widely-used mods.
- **Alternatives considered:** including Starlight and C2ME for a more
  "complete" optimization story (rejected for v0.1.0).
- **Rationale:** Starlight and C2ME touch more invasive internals (lighting,
  chunk-loading concurrency) than Lithium/FerriteCore/Krypton's
  behavior-preserving rewrites. Since this pack's core value is HeapHammer's
  memory readings, an optimizer bug that muddies those readings would
  undermine the pack's own reason to exist.
- **Consequences:** the pack doesn't offer the maximum possible TPS/worldgen
  speed improvement available on Fabric.
- **Revisit if:** Starlight/Moonrise or C2ME build a longer track record of
  stability with HeapHammer specifically (tested empirically, not assumed).

## D-002: Pack scope is diagnostics-only — no admin/QoL/builder mods

- **Status:** decided
- **Context:** Comparable existing packs ("Server Essentials",
  "Server Essential Mods") bundle optimizers alongside permission mods
  (LuckPerms), teleport commands, builder tools (WorldEdit, Litematica), and
  QoL content.
- **Alternatives considered:** building a broader "everyday server toolkit"
  pack that also happens to include HeapHammer (rejected).
- **Rationale:** broadening scope dilutes the pitch ("run a soak test with a
  profiler underneath") into "yet another general server pack," which is a
  crowded space this project has no differentiation in. The differentiated
  niche is diagnostics, not administration.
- **Consequences:** admins wanting permissions/builder tools need a separate
  pack or manual install alongside this one.
- **Revisit if:** user feedback specifically asks for it and it doesn't
  compromise the diagnostics framing.

## D-001: Pack concept is "profiler + tested optimizers", not a leak-bait/repro pack

- **Status:** decided (supersedes an earlier direction)
- **Context:** The original idea explored was a pack that intentionally
  bundles known leak-prone mod categories (minimap/land-claim, entity
  tracking, tech mods with poor cleanup) to demonstrate HeapHammer catching
  real leaks.
- **Alternatives considered:** the leak-bait/repro pack (superseded).
- **Rationale:** the user explicitly redirected: "we pack heaphammer with
  profilers and tested optimisers" instead. A repro pack requires ongoing
  curation of which third-party mods currently leak (a moving target as
  those mods get fixed or replace their maintainers); a profiler+optimizer
  pack is stable and positions HeapHammer as part of a "server health"
  toolkit instead of a bug museum.
- **Consequences:** none — this is the current, active direction.
- **Revisit if:** not currently anticipated.

## Open questions (not yet decided)

- Final license posture for the pack as a whole (HeapHammer itself is
  LGPL-3.0; Spark, Lithium, FerriteCore, and Krypton each carry their own
  licenses as bundled third-party mods — the pack's own packwiz metadata
  license has not been chosen yet).
- Whether this pack becomes eligible for/joins the CurseForge Server
  Affiliation Program (that program currently only supports Modpacks, not
  Mods — this pack being a modpack is what would make HeapHammer-adjacent
  affiliation possible, but no decision to opt in has been made).
- Exact packwiz mod-source pins (CurseForge project IDs / Modrinth slugs and
  versions) are not yet recorded — deferred to the build/scaffold step.
