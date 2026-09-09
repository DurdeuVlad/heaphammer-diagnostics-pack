# Contributing to HeapHammer Diagnostics Pack

Thank you for your interest in contributing to the **HeapHammer Diagnostics
Pack**!

This is a curated Fabric modpack that bundles HeapHammer (a deterministic
memory-leak/regression detector), Spark (CPU/tick/heap profiling), and a small
optimizer set (Lithium, FerriteCore, Krypton) with Fabric API as a required
platform dependency. The pack targets six Minecraft versions: 1.16.5, 1.18.2,
1.19.2, 1.20.1, 1.21.1, and 1.21.4.

Because this pack is a thin distribution layer over other people's mods, the
contribution surface is different from a code project. Most contributions will
be metadata fixes, version additions, documentation improvements, or
verification evidence.

---

## Table of Contents

1. [The Most Valuable Contribution: A Great Issue](#1-the-most-valuable-contribution-a-great-issue)
2. [Scope Discipline](#2-scope-discipline)
3. [Mod Version Bumps & New Minecraft Versions](#3-mod-version-bumps--new-minecraft-versions)
4. [Empirical Verification Requirements](#4-empirical-verification-requirements)
5. [Pull Request Checklist](#5-pull-request-checklist)
6. [AI-Assisted Contributions & Disclosure Policy](#6-ai-assisted-contributions--disclosure-policy)
7. [Code of Conduct & License](#7-code-of-conduct--license)

---

## 1. The Most Valuable Contribution: A Great Issue

A thorough, reproducible issue report is the most valuable contribution you
can make. It helps the maintainer reproduce the problem and determine whether
it is a pack metadata issue, a mod compatibility issue, or an upstream mod bug.

### What Makes a Great Issue

- **Descriptive title**: State the affected Minecraft version and the symptom
  (e.g. `[1.20.1] HeapHammer fails to initialize: NoClassDefFoundError`).
- **Environment**: Minecraft version, Fabric Loader version, Java runtime
  (vendor + version), server type (dedicated/integrated).
- **Pack version**: The `version` field from `packs/<version>/pack.toml`.
- **Reproduction steps**: Exact commands to install and boot the pack.
- **Observed vs expected**: What happened, what you expected.
- **Logs**: Attach the relevant server log excerpt (at minimum from mod
  loading to the `Done` line or the crash).

---

## 2. Scope Discipline

The mod set is deliberately small and was chosen for specific reasons recorded
in [Decision.md](Decision.md):

- **HeapHammer** — the diagnostic tool this pack exists to distribute.
- **Spark** — profiling, so a HeapHammer verdict can be correlated with a
  flame graph for the same time window.
- **Lithium, FerriteCore, Krypton** — a minimal optimizer set that reduces
  baseline noise without changing game behavior.
- **Fabric API** — required platform dependency for HeapHammer.

**Do not propose adding** admin/permission mods, builder tools, QoL mods,
Starlight, C2ME, or additional optimizers. If you believe a mod should be
added or removed, open a discussion issue with the same rigor as the existing
Decision.md entries — do not submit a PR that silently swaps a mod.

---

## 3. Mod Version Bumps & New Minecraft Versions

### Bumping a mod version

1. Update the relevant `packs/<version>/mods/<mod>.pw.toml` with the new
   file hash and CurseForge file ID.
2. Run `packwiz refresh --pack-file packs/<version>/pack.toml --meta-folder-base packs/<version>` to update `index.toml`.
3. Restore `license = "LGPL-3.0-only"` in `pack.toml` if `packwiz refresh`
   removed it.
4. Boot-test the affected version (see §4).

### Adding a new Minecraft version

1. Create `packs/<new-version>/` with `pack.toml`, `index.toml`, and `mods/`.
2. Pin all six mods at versions compatible with the target Minecraft version.
3. Run `packwiz refresh` and restore the license field.
4. Boot-test the new version.
5. Update `Testing.md` with the verification receipt.
6. Update `README.md` if the supported version list changes.

---

## 4. Empirical Verification Requirements

**Never claim a pack version works without booting a server and reading the
log.** A pack export that resolves is not evidence the server boots.

The minimum verification is:

1. Install the pack via `packwiz-installer-bootstrap` against a local
   `packwiz serve` instance.
2. Boot a Fabric dedicated server with Java 21 (HeapHammer requires JAVA_21
   mixin compatibility on all branches).
3. Confirm the log shows all six mods loading with no fatal errors.
4. Confirm the server reaches `Done (...)! For help, type "help"`.
5. Record the Java version, Fabric Loader version, mod versions, and the
   `Done` timestamp in `Testing.md`.

See `.verify/Verify-Version.ps1` for the automated verification script used
by the maintainer.

---

## 5. Pull Request Checklist

- [ ] My change follows the scope discipline in §2.
- [ ] I have run `packwiz refresh` and restored the `license` field if it was
      removed.
- [ ] I have boot-tested any affected Minecraft version with Java 21 and
      attached the log excerpt showing the `Done` line.
- [ ] I have updated `Testing.md` with the verification receipt.
- [ ] I have updated `README.md` if the supported version list or commands
      changed.
- [ ] All commit messages follow [Conventional Commits](https://www.conventionalcommits.org/).

---

## 6. AI-Assisted Contributions & Disclosure Policy

This project embraces AI-assisted engineering. If your contribution was
drafted, generated, or assisted by an AI agent, you **must** disclose it in
the PR description:

```markdown
> **AI Disclosure**: This contribution was developed with AI pair-programming
> assistance (Tool/Agent: ________). All changes have been reviewed and
> empirically verified by booting the affected server version(s).
```

The human contributor remains 100% responsible for every line of metadata,
documentation, and configuration submitted. "The AI wrote it" is never an
excuse for a broken pack or a scope violation.

---

## 7. Code of Conduct & License

- By contributing, you agree to abide by the [Code of Conduct](CODE_OF_CONDUCT.md).
- The pack metadata is licensed under the GNU Lesser General Public License
  v3.0 (see [LICENSE](LICENSE)). The bundled mods retain their own licenses;
  this pack does not relicense or repackage mod binaries.
- HeapHammer is licensed under LGPL-3.0 by its author (dwurdy).
- Spark is licensed under the Apache License 2.0.
- Lithium is licensed under LGPL-3.0.
- FerriteCore is licensed under LGPL-3.0.
- Krypton is licensed under the Apache License 2.0.
- Fabric API is licensed under Apache 2.0.
