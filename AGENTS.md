# AGENTS.md

Entry point for any AI agent (or human) picking up this repo cold.

## Current status (as of 2026-09-09)

- **M1 is complete** (1.21.1 verified). **M3 is complete** (6 Fabric
  versions: 1.16.5, 1.18.2, 1.19.2, 1.20.1, 1.21.1, 1.21.4 — all
  hash-consistency verified; server-boot verification pending for 5 new
  versions). **M2 is in progress** (1.21.1 export artifacts produced; 5
  new version exports and platform submission pending).
- The pack is now multi-version: each version lives in
  `packs/<version>/` with its own `pack.toml`, `index.toml`, and
  `mods/`. Root-level docs (this file, Decision.md, README.md, etc.) are
  repo documentation, not modpack content.
- Repo: https://github.com/DurdeuVlad/heaphammer-diagnostics-pack (private).
- Sole maintainer: dwurdy (Vlad Durdeu).

## Read in this order

1. [Business.md](Business.md) — the problem, the audience, the usage flow,
   and why this pack exists at all (there's a validated market gap behind
   it, not just a hunch — see its "Market check" note).
2. [Decision.md](Decision.md) — every consequential choice already made
   (distribution format, version target, mod set, scope boundary) with the
   reasoning behind each. **Do not silently re-derive or override any of
   these** — if one looks wrong once you're implementing, add a new dated
   entry explaining why, don't just change the behavior.
3. [Milestones.md](Milestones.md) — what M1, M2, and M3 actually are.
4. [Testing.md](Testing.md) — the concrete, run-it-yourself steps and pass/
   fail conditions for M1, plus the M3 per-version mod matrix and
   acceptance checklist.
5. [README.md](README.md) — the user-facing quick-start (what a server
   admin actually types).
6. [Collaboration.md](Collaboration.md) — ownership and contribution norms
   (short; this is solo-owned).

## What you're being asked to do

M1 and M3 are complete (hash-consistency verified for all 6 packs). The
next step is server-boot verification for the 5 new versions (1.16.5,
1.18.2, 1.19.2, 1.20.1, 1.21.4), then M2 publication. Do not start M2
publication or the open HeapHammer Modrinth-source follow-up unless
explicitly asked.

## Hard rules

- **Empirical verification only.** Never report a step "done" without having
  actually run the command and read its output. This applies especially to
  the server-boot and soak-test steps in Testing.md — a config that *should*
  work is not evidence it *does* work.
- **Don't silently substitute mods.** The optimizer set (Lithium, FerriteCore,
  Krypton — not Starlight, not C2ME) was a deliberate, reasoned decision
  (Decision.md D-003). The version target was D-004 (1.21.1 only) but is now
  superseded by the M3 expansion to 6 Fabric versions (1.16.5–1.21.4). If
  you hit a blocker with one of them, record a new Decision.md entry with
  the same rigor as the existing ones — don't quietly swap in an
  alternative.
- **Don't expand scope.** No admin/permission/builder/QoL mods (Decision.md
  D-002). If a slug correction or install-method choice comes up during
  implementation, log it per Testing.md's instructions rather than leaving
  it implicit in a commit message only.
- **This is a separate repo from HeapHammer** (`E:\Github2\HeapHammer` on the
  maintainer's machine, sibling project). Do not modify HeapHammer's source
  to make this pack work — if the HeapHammer jar itself needs a fix, that's
  a HeapHammer-repo task, file it there.
- **AI disclosure:** if you are an AI agent making commits here, disclose it
  in commit messages/PR descriptions the same way this repo's own history
  already does (`Co-Authored-By:` trailer). This repo intends to go public
  alongside HeapHammer, which already discloses AI-assisted development in
  its own `AGENTS.md`/`CONTRIBUTING.md` — match that posture, don't hide it.

## Git

No branch-protection or PR-only rule has been set on this repo yet (it's a
brand-new private repo with a single commit on `main`). Use your judgment;
if this stops being a solo scratch repo, treat `main` as protected like
HeapHammer does.
