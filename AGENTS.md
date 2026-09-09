# AGENTS.md

Entry point for any AI agent (or human) picking up this repo cold.

## Current status (as of 2026-09-09)

- **M1 is complete.** The five requested mods are pinned in `index.toml` and
  `mods/*.pw.toml`, with Fabric API pinned as a required platform dependency.
  A disposable Fabric 1.21.1 server booted cleanly and the documented
  HeapHammer + Spark soak-test workflow produced a PASS verdict and profile
  URL. M2 publication has not started.
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
3. [Milestones.md](Milestones.md) — what M1 and M2 actually are.
4. [Testing.md](Testing.md) — the concrete, run-it-yourself steps and pass/
   fail conditions for M1, plus the acceptance checklist at the bottom.
5. [README.md](README.md) — the user-facing quick-start (what a server
   admin actually types).
6. [Collaboration.md](Collaboration.md) — ownership and contribution norms
   (short; this is solo-owned).

## What you're being asked to do

M1 is complete. Any follow-up agent should use Testing.md as the empirical
record and must not start M2 publication or the open HeapHammer Modrinth-source
follow-up unless explicitly asked.

## Hard rules

- **Empirical verification only.** Never report a step "done" without having
  actually run the command and read its output. This applies especially to
  the server-boot and soak-test steps in Testing.md — a config that *should*
  work is not evidence it *does* work.
- **Don't silently substitute mods.** The optimizer set (Lithium, FerriteCore,
  Krypton — not Starlight, not C2ME) and the version target (Fabric 1.21.1
  only) were deliberate, reasoned decisions (Decision.md D-003, D-004). If
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
