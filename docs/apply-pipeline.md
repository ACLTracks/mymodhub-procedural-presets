# Apply Pipeline (Procedural Presets)

This document defines **how validated entries are applied to a character** once they are loaded into the runtime registry.

The apply pipeline is where procedural modding succeeds or fails.  
Its primary job is to **change only what is intended**, preserve user choice, and remain reversible.

Nothing in this pipeline is allowed to be destructive.

---

## Goals

- Apply morph data safely and deterministically
- Never overwrite user-selected head parts
- Support stacking and iteration
- Allow clean revert
- Produce predictable results every time
- Make failures visible, not silent

---

## Non-Goals (v0.1)

- NPC batch application
- Background or automatic application
- Randomization at runtime
- Cross-race morph translation
- Persistent save-level storage beyond session

v0.1 focuses on **manual, intentional application**.

---

## Entry Point

### When Apply Is Allowed

Apply may only be triggered when:
- RaceMenu is open
- A character is actively being edited

Apply must not:
- run on game load
- run automatically
- run without user confirmation

This ensures the user is always in control.

---

## High-Level Apply Flow

1. User selects an entry
2. Engine validates entry is still loaded and valid
3. Snapshot current morph state
4. Resolve apply settings
5. Apply morph operations
6. Clamp and normalize values
7. Report summary to user
8. Keep snapshot available for revert

If any step fails, abort and revert to snapshot.

---

## Snapshot Rules

### What Is Snapshotted

Before applying anything:
- All current RaceMenu slider values
- Only sliders that the entry *might* touch need to be snapshotted

Snapshots are:
- per-session
- in-memory
- lightweight

### Snapshot Lifetime

- One snapshot per apply action
- Snapshot is discarded when:
  - user applies another entry
  - user leaves RaceMenu
  - game session ends

Persistent undo is out of scope for v0.1.

---

## Scope Enforcement

### What Can Be Modified

Only:
- RaceMenu morph sliders

Explicitly forbidden:
- hair
- eyes
- brows
- head meshes
- body meshes
- skeletons
- textures
- plugins

If an entry attempts to modify anything outside morph sliders, it is rejected.

---

## Order of Operations

### Required User Order

1. User selects:
   - head mesh (vanilla or High Poly Head)
   - hair
   - eyes
   - brows
2. User optionally loads a base preset
3. User applies procedural entry

The engine assumes head parts are final when apply is triggered.

---

## Apply Settings Resolution

Apply behavior is resolved in this order:

1. Entry-level overrides
2. Entry defaults
3. Pack defaults
4. Engine defaults

Resolved settings include:
- apply mode
- strength multiplier
- clamp ranges
- skip-missing behavior

---

## Apply Modes

### Additive
