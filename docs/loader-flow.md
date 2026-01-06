# Loader Flow (SKSE Plugin)

This document defines how the MyModHub SKSE plugin discovers, validates, caches, and loads Procedural Preset Packs **without ever blocking game launch**.

If a pack is broken, it gets skipped and logged. Skyrim still boots.

---

## Goals

- **Deterministic loading:** same install, same result
- **Non-destructive:** never overwrites user files
- **Non-blocking:** invalid content never prevents the game from starting
- **Safe-by-default:** only packs that pass validation enter the runtime registry
- **Fast startup:** caching and minimal IO on subsequent launches
- **Clear logs:** authors and users can debug without guessing

---

## Non-Goals (v0.1)

- Downloading packs in-game
- Runtime internet calls
- UI for browsing packs
- Applying morphs automatically to NPCs
- Merging pack content into new files

v0.1 only loads packs, validates them, and exposes them to the apply pipeline.

---

## Terms

- **Pack:** a folder containing `manifest.json`, `index.json`, and an `entries/` directory
- **Entry:** a JSON file referenced by the pack index (example: a RaceMenu morph preset)
- **Registry:** in-memory list of validated packs and their validated entries
- **Cache:** stored validation results + resolved file paths to reduce IO next launch

---

## Pack Discovery

### Search Roots (in order)

The loader searches these directories in order. Later roots can add more packs but cannot override an existing pack ID.

1. `Data\MyModHub\Packs\`
2. `Data\SKSE\Plugins\MyModHub\Packs\`

Reason:
- First path is clean and user-facing
- Second path is plugin-adjacent for power users

### Pack Folder Rules

A folder is considered a candidate pack if it contains:

- `manifest.json`
- `index.json`
- `entries\` directory

If any of these are missing, the folder is ignored.

### Unique Pack ID Rule

Each pack has a globally unique `packId` from `manifest.json`.

If two packs share the same `packId`:
- The first one discovered wins
- The duplicates are skipped
- A warning is logged with both paths

No "last write wins". No ambiguity.

---

## Startup Sequence

### High-level Sequence

1. Initialize logging
2. Determine game runtime (SE/AE/VR)
3. Resolve pack roots
4. Discover candidate pack folders
5. Load + validate pack manifests
6. Load + validate pack indexes
7. Load + validate entry files referenced by indexes
8. Build runtime registry
9. Write cache summary
10. Expose registry to apply pipeline

At no point should failure in steps 4-9 prevent step 10. Worst case: registry is empty.

---

## Validation Strategy

### Two-layer Validation

Validation is done in two passes:

**Pass A: Schema validation**
- Validate JSON shape against schemas in `/schemas/`
- Fail fast if schema fails

**Pass B: Safety + consistency checks**
- Enforce rules not expressible in JSON schema
- Example: entry IDs unique, file referenced exists, allowed slider ranges, etc.

Only entries that pass both are registered.

### Validation Strictness Levels

Three practical modes exist, selectable via INI later:

- **Strict (default):** drop any entry with any issue
- **Lenient:** drop only dangerous issues, allow minor missing metadata
- **Dev:** load everything possible and log loudly

For v0.1, implement Strict only, but structure code so levels can be added.

---

## What Gets Validated

### Manifest (`manifest.json`)

Minimum checks:
- JSON parses
- Matches `mymodhub.pack-manifest.schema.json`
- `packId` is present, non-empty, stable format
- `version` is valid SemVer-ish string
- Declared `type` is supported by the plugin build
- Optional tier metadata is informational only in v0.1

If manifest fails: skip entire pack.

### Index (`index.json`)

Minimum checks:
- JSON parses
- Matches `pack-index.schema.json`
- Entry list exists and is non-empty
- Every entry has:
  - `id`
  - `type`
  - `file` (relative path)
- `id` values are unique within the pack
- `file` values do not escape the pack (no `..` traversal)

If index fails: skip entire pack.

### Entry Files (`entries/*.json`)

Minimum checks:
- File exists and readable
- JSON parses
- Matches schema based on `type`
  - Example: `racemenu-morph-entry.schema.json`
- Safety checks:
  - No unknown slider keys (or optionally allow unknown with warnings later)
  - Numeric values within allowed ranges (as defined by schema + safety layer)
  - No attempt to reference absolute paths or external resources

If an entry fails: skip entry but keep pack.

---

## Registry Build Rules

### Registry Contents

At runtime, the registry holds:

- `packId`
- `packName`
- `packVersion`
- `tier` (Featured / Experimental, informational for now)
- `packPath`
- `entries[]` (only validated entries)
- Precomputed hashes for cache comparison

### Pack Acceptance Rule

A pack is only registered if:
- Manifest is valid
- Index is valid
- At least one entry is valid

Packs with zero valid entries are ignored (with a warning).

---

## Caching

### Why Cache Exists

Avoid re-reading and re-validating everything every startup.

Cache does not replace validation.
It reduces work when nothing has changed.

### Cache File Location

Default:
- `Data\SKSE\Plugins\MyModHub\cache\pack-cache.json`

### Cache Key

A pack is considered unchanged if:

- `manifest.json` last modified timestamp + size matches
- `index.json` last modified timestamp + size matches
- Each referenced entry file timestamp + size matches

Optionally later:
- replace timestamps with hashes for better robustness
- but timestamps+size is good for v0.1

### Cache Behavior

On startup:

- If cache missing: do full validation
- If cache exists:
  - For each pack candidate:
    - If unchanged: reuse cached validation results + resolved paths
    - If changed: revalidate pack and update cache

Cache is always best-effort.
If cache read fails: ignore cache and do full validation.

---

## Logging

### Log File Location

- `Data\SKSE\Plugins\MyModHub\MyModHub.log`

### Required Log Events

Always log:

- Plugin version + runtime
- Pack roots scanned
- Packs discovered count
- Packs registered count
- Entries registered count
- Packs skipped with reason
- Entries skipped with reason and file path

### Log Severity

- INFO: normal flow summary
- WARN: pack skipped, duplicates, missing optional metadata
- ERROR: JSON parse failures, schema failures, unsafe paths

Never spam per-slider details in normal mode.
Keep logs human-readable.

---

## Failure Behavior (Must Never Crash)

If any of these happen, plugin continues:

- a pack has invalid JSON
- a pack references missing entry files
- an entry schema fails
- cache cannot be read or written
- pack folder has junk files

The only time the plugin should abort loading is:
- catastrophic failure in plugin init itself (rare)
- memory allocation failure (extreme)
- file system access is entirely unavailable (also rare)

In those cases:
- log error
- registry stays empty
- game continues

---

## Security and Safety Rules

- Only load files within discovered pack directories
- Reject any entry file path with:
  - `..`
  - leading `/` or drive letter
  - symlink escape (best-effort check)
- No runtime networking
- No execution of scripts
- No DLL loading from pack content
- Packs are treated as data only

---

## Determinism Rules

Given the same installed packs:
- discovery order is stable
- duplicate pack IDs are resolved the same way every time
- registry order is consistent (sort by `packId` + `entryId`)

Implementation detail:
- after discovery, sort candidate packs by absolute path or packId
- choose one and stick to it

---

## Minimal Implementation Checklist (v0.1)

- [ ] Add pack roots scan
- [ ] Find candidate pack folders
- [ ] Read + validate manifest
- [ ] Read + validate index
- [ ] Read + validate entries
- [ ] Build registry in memory
- [ ] Write cache file
- [ ] Log summary counts
- [ ] Expose registry getter for apply pipeline

---

## Example: Discovery Layout

Data/MyModHub/Packs/
nord-facepack/
manifest.json
index.json
entries/
soft_nord_glow.json
sharp_nord_edge.json

Data/SKSE/Plugins/MyModHub/Packs/
dev-pack/
manifest.json
index.json
entries/
wild_test.json


Result:
- Both packs load if IDs differ
- If IDs collide, `Data\MyModHub\Packs\` wins due to search root order

---

## Next Doc

After this loader flow is locked, the next document is:

`docs/apply-pipeline.md`

That document defines how entries are applied, stacked, and reversed safely.
Example install:

