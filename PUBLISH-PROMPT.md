# Claude Browser Extension Publication Prompt — HeapHammer Diagnostics Pack

> **IMPORTANT — No publication has been performed yet.** This prompt is a
> handoff document for Claude to use a browser extension to publish the
> HeapHammer Diagnostics Pack on CurseForge and Modrinth. Do not begin
> submission without reviewing this prompt end-to-end and confirming
> every detail with the maintainer.

---

## Context

- **Repository:** https://github.com/DurdeuVlad/heaphammer-diagnostics-pack
- **Maintainer:** dwurdy (Vlad Durdeu), GitHub `@DurdeuVlad`
- **Pack name:** HeapHammer Diagnostics Pack
- **Pack version:** 0.1.0
- **License:** LGPL-3.0-only (pack metadata); bundled mods retain their
  own licenses (HeapHammer LGPL-3.0, Spark Apache-2.0, Lithium LGPL-3.0,
  FerriteCore LGPL-3.0, Krypton Apache-2.0, Fabric API Apache-2.0).
- **Mod loader:** Fabric (all 6 versions)
- **Supported Minecraft versions:** 1.16.5, 1.18.2, 1.19.2, 1.20.1,
  1.21.1, 1.21.4
- **Java requirement:** Java 21 for all versions (HeapHammer mixin
  config requests JAVA_21 on all branches)

## Known caveat — 1.16.5

The published HeapHammer 1.16.5 jar does not bundle `slf4j-api`, which
Minecraft 1.16.5 does not ship on its classpath. The 1.16.5 pack
metadata is correct, but the pack will fail to boot on a stock install
with `NoClassDefFoundError: org/slf4j/LoggerFactory`.

**Recommendation:** Do NOT publish the 1.16.5 pack on either platform
until HeapHammer 1.16.5 ships a jar that bundles slf4j-api. Publish the
other 5 versions (1.18.2, 1.19.2, 1.20.1, 1.21.1, 1.21.4) now. When the
HeapHammer 1.16.5 jar is fixed, publish 1.16.5 as a follow-up release.

If the maintainer explicitly chooses to publish 1.16.5 despite this
caveat, the pack description must disclose the slf4j requirement
prominently.

---

## Artifact map (12 artifacts: 6 versions × 2 platforms)

All artifacts are in the `dist/` directory of the repository on the
maintainer's machine at `E:\Github2\heaphammer-diagnostics-pack\dist\`.
They are NOT committed to git (dist/ is gitignored). The maintainer must
provide the files to Claude or make them accessible.

### CurseForge artifacts (packwiz curseforge export — metadata-only zip)

These are small (~870 bytes) zip files containing pack metadata that
references CurseForge project/file IDs. CurseForge resolves and
downloads the mods server-side.

| MC Version | File | Path |
|---|---|---|
| 1.16.5 | heaphammer-diagnostics-pack-1.16.5-curseforge.zip | dist/heaphammer-diagnostics-pack-1.16.5-curseforge.zip |
| 1.18.2 | heaphammer-diagnostics-pack-1.18.2-curseforge.zip | dist/heaphammer-diagnostics-pack-1.18.2-curseforge.zip |
| 1.19.2 | heaphammer-diagnostics-pack-1.19.2-curseforge.zip | dist/heaphammer-diagnostics-pack-1.19.2-curseforge.zip |
| 1.20.1 | heaphammer-diagnostics-pack-1.20.1-curseforge.zip | dist/heaphammer-diagnostics-pack-1.20.1-curseforge.zip |
| 1.21.1 | heaphammer-diagnostics-pack-1.21.1-curseforge.zip | dist/heaphammer-diagnostics-pack-1.21.1-curseforge.zip |
| 1.21.4 | heaphammer-diagnostics-pack-1.21.4-curseforge.zip | dist/heaphammer-diagnostics-pack-1.21.4-curseforge.zip |

### Modrinth artifacts (packwiz modrinth export — .mrpack with bundled mods)

These are larger (4–7 MB) mrpack files that bundle all 6 mod jars
inside, since HeapHammer is not yet on Modrinth (the other 5 mods are
on Modrinth but the pack bundles them for now per Decision.md).

| MC Version | File | Path |
|---|---|---|
| 1.16.5 | heaphammer-diagnostics-pack-1.16.5.mrpack | dist/heaphammer-diagnostics-pack-1.16.5.mrpack |
| 1.18.2 | heaphammer-diagnostics-pack-1.18.2.mrpack | dist/heaphammer-diagnostics-pack-1.18.2.mrpack |
| 1.19.2 | heaphammer-diagnostics-pack-1.19.2.mrpack | dist/heaphammer-diagnostics-pack-1.19.2.mrpack |
| 1.20.1 | heaphammer-diagnostics-pack-1.20.1.mrpack | dist/heaphammer-diagnostics-pack-1.20.1.mrpack |
| 1.21.1 | heaphammer-diagnostics-pack-1.21.1.mrpack | dist/heaphammer-diagnostics-pack-1.21.1.mrpack |
| 1.21.4 | heaphammer-diagnostics-pack-1.21.4.mrpack | dist/heaphammer-diagnostics-pack-1.21.4.mrpack |

---

## Per-version mod matrix (for dependency declarations)

| MC Version | Fabric Loader | Fabric API | HeapHammer | Spark | Lithium | FerriteCore | Krypton |
|---|---|---|---|---|---|---|---|
| 1.16.5 | 0.19.5 | 0.42.0+1.16 | 1.0.0 | 1.6.0 | 0.6.6 | 2.1.1 | 0.1.2 |
| 1.18.2 | 0.19.5 | 0.77.0+1.18.2 | 1.0.0 | 1.10.39 | 0.10.3 | 4.2.1 | 0.1.9 |
| 1.19.2 | 0.19.5 | 0.77.0+1.19.2 | 1.0.0 | 1.10.37 | 0.11.1 | 5.0.3 | 0.2.1 |
| 1.20.1 | 0.19.5 | 0.92.12+1.20.1 | 1.0.0 | 1.10.53 | 0.11.4 | 6.0.1 | 0.2.3 |
| 1.21.1 | 0.19.5 | 0.116.17+1.21.1 | 1.0.0 | 1.10.109 | 0.15.4 | 7.0.3 | 0.2.8 |
| 1.21.4 | 0.19.5 | 0.119.4+1.21.4 | 1.0.0 | 1.10.121 | 0.15.3 | 7.1.3 | 0.2.8 |

---

## CurseForge publication steps

### Prerequisites

- The maintainer must be logged into CurseForge and have an approved
  project for the pack (or create one first). The HeapHammer mod itself
  is already approved on CurseForge:
  https://www.curseforge.com/minecraft/mc-mods/heaphammer
- The pack project on CurseForge may need to be created as a new
  project under the "Modpacks" category. Do NOT create the project
  without the maintainer's explicit confirmation of the project name,
  slug, and description.

### For each version (skip 1.16.5 per the caveat above):

1. Navigate to the pack's CurseForge project page.
2. Go to the "Files" tab and click "Upload File".
3. Select the corresponding `dist/heaphammer-diagnostics-pack-<version>-curseforge.zip`.
4. Set the release type to "Release" (not Beta or Alpha).
5. Set the Minecraft version to the matching version (e.g. 1.20.1).
6. Set the mod loader to "Fabric".
7. Set the changelog. Suggested changelog for v0.1.0:

   ```
   Initial release of the HeapHammer Diagnostics Pack for Minecraft <version>.

   Bundled mods:
   - HeapHammer 1.0.0 — deterministic memory-leak/regression detector
   - Spark <version> — CPU/tick/heap profiler
   - Lithium <version> — vanilla logic optimizer
   - FerriteCore <version> — memory dedup optimizer
   - Krypton <version> — network stack optimizer
   - Fabric API <version> — required platform dependency

   All six mods have been boot-verified on a dedicated Fabric server
   with Java 21. See the repository Testing.md for per-version
   verification receipts.

   Usage: install the pack, boot the server, then run:
     /hh doctor
     /spark profiler start
     /hh run chunks --iterations=5 --batch=10 --radius=8 --strategy=spiral
     /spark profiler stop
     /hh report show last
   ```

8. Review all fields for correctness before clicking "Submit".
9. After submission, verify the file appears in the project's file list
   with the correct version and loader tags.

### CurseForge project metadata (if creating a new project)

- **Name:** HeapHammer Diagnostics Pack
- **Slug:** heaphammer-diagnostics-pack (confirm with maintainer)
- **Summary:** A Fabric modpack for server admins: HeapHammer leak
  detector + Spark profiler + minimal optimizer set (Lithium,
  FerriteCore, Krypton) for clean baseline profiling.
- **Categories:** Modpacks; Technology; Performance
- **License:** LGPL-3.0-only
- **Mod loader:** Fabric
- **Supported Minecraft versions:** 1.18.2, 1.19.2, 1.20.1, 1.21.1,
  1.21.4 (and 1.16.5 pending HeapHammer slf4j fix)

---

## Modrinth publication steps

### Prerequisites

- The maintainer must be logged into Modrinth.
- A Modrinth project for the pack must exist or be created. Do NOT
  create the project without the maintainer's explicit confirmation.
- HeapHammer is NOT yet on Modrinth (see Decision.md open follow-up).
  The .mrpack files bundle HeapHammer as a JAR override. This is
  acceptable for Modrinth but means Modrinth update-tracking will not
  track HeapHammer updates.

### For each version (skip 1.16.5 per the caveat above):

1. Navigate to the pack's Modrinth project page.
2. Go to the "Versions" tab and click "Publish new version".
3. Upload the corresponding `dist/heaphammer-diagnostics-pack-<version>.mrpack`.
4. Set the version name to `0.1.0` (or `0.1.0-<version>` if the
   maintainer prefers per-version versioning).
5. Set the version number to `0.1.0`.
6. Set the channel to "release".
7. Set the Minecraft version(s) to the matching single version.
8. Set the mod loader to "fabric".
9. Set the version type to "Release".
10. Add dependencies:
    - HeapHammer: bundled (no Modrinth project exists yet)
    - Spark: required (Modrinth project exists)
    - Lithium: required (Modrinth project exists)
    - FerriteCore: required (Modrinth project exists)
    - Krypton: required (Modrinth project exists)
    - Fabric API: required (Modrinth project exists)
11. Set the changelog (same as CurseForge changelog above).
12. Review all fields before clicking "Publish".
13. After publishing, verify the version appears in the project's
    version list with the correct Minecraft version and loader.

### Modrinth project metadata (if creating a new project)

- **Name:** HeapHammer Diagnostics Pack
- **Slug:** heaphammer-diagnostics-pack (confirm with maintainer)
- **Description:** A Fabric modpack for server admins: HeapHammer leak
  detector + Spark profiler + minimal optimizer set (Lithium,
  FerriteCore, Krypton) for clean baseline profiling.
- **Categories:** performance, technology
- **License:** LGPL-3.0-only
- **Mod loader:** Fabric
- **Supported Minecraft versions:** 1.18.2, 1.19.2, 1.20.1, 1.21.1,
  1.21.4 (and 1.16.5 pending HeapHammer slf4j fix)
- **Project links:**
  - Source: https://github.com/DurdeuVlad/heaphammer-diagnostics-pack
  - Issues: https://github.com/DurdeuVlad/heaphammer-diagnostics-pack/issues
  - Wiki: (none yet)

---

## Pre-submission checklist (Claude must verify each before submitting)

- [ ] Confirm with the maintainer which versions to publish (recommend:
      1.18.2, 1.19.2, 1.20.1, 1.21.1, 1.21.4 — exclude 1.16.5).
- [ ] Confirm the CurseForge project exists and the maintainer has
      upload rights, or create it with explicit maintainer approval.
- [ ] Confirm the Modrinth project exists and the maintainer has upload
      rights, or create it with explicit maintainer approval.
- [ ] Verify each artifact file exists at the expected path in dist/.
- [ ] Verify the project description, categories, and license are correct
      on both platforms.
- [ ] Verify the mod loader is set to "Fabric" for every version on both
      platforms.
- [ ] Verify the Minecraft version is set correctly for each file
      (do not cross-assign versions).
- [ ] Verify all dependency declarations match the per-version mod
      matrix above.
- [ ] Verify the changelog is present and accurate for each version.
- [ ] Do NOT publish 1.16.5 unless the maintainer explicitly overrides
      the slf4j caveat.
- [ ] After each submission, confirm the file/version appears publicly
      on the platform before moving to the next.
- [ ] Do not invent project IDs, URLs, or checksums. Only use values
      present in the repository or confirmed by the maintainer.
- [ ] Respect third-party mod licensing and attribution. The pack does
      not relicense or claim ownership of any bundled mod.

---

## Post-submission

After all versions are published on both platforms:

1. Record the CurseForge and Modrinth project URLs in the repository
   README.md (under a new "Download" or "Install" section).
2. Update Milestones.md to mark M2 (publication) as complete.
3. Commit the documentation updates with AI disclosure.
4. Do not claim publication is complete until both platforms show all
   intended versions publicly.
