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

newValue = current + (delta * strength)
### Clamped Additive (default)

newValue = clamp(current + (delta * strength))
### Absolute

newValue = clamp(delta * strength)

Absolute mode should be rare and clearly documented.

---

## Slider Processing Rules

For each slider operation:

1. Resolve slider key to RaceMenu slider
2. If slider does not exist:
   - skip if `skip_missing_sliders = true`
   - otherwise log warning and skip
3. Compute new value based on apply mode
4. Clamp to:
   - per-slider min/max if provided
   - else entry clamp
   - else engine clamp
5. Apply value

Sliders are processed in deterministic order:
- sorted by slider key name

---

## Stacking Behavior

### Multiple Applies

When multiple entries are applied sequentially:
- each apply starts from current state
- earlier changes persist
- later changes override per slider

No blending math beyond additive is performed in v0.1.

### Determinism Rule

Given the same starting state and the same sequence of entries:
- the result must always be identical

---

## Conflict Handling

### Same Slider Modified Multiple Times

- last applied entry wins for that slider
- no averaging or merging in v0.1

### Same Entry Applied Twice

- applying the same entry twice should not explode the face
- additive entries will compound
- users are responsible for taste
- snapshot + revert protects experimentation

---

## Safety Clamps

Default engine clamps:
- normalized sliders: `[-1.0, 1.0]`
- absolute safety max: `[-2.0, 2.0]`

Values outside safety range are clamped and logged.

---

## Reporting and Feedback

After apply, the engine should provide:

- number of sliders modified
- number skipped (missing)
- number clamped
- tier warning if Experimental
- option to revert immediately

This feedback builds trust.

---

## Revert Behavior

### Revert Trigger

- explicit user action
- only available if a snapshot exists

### Revert Action

- restore all snapshotted slider values
- clear snapshot
- log revert action

Revert must be instant and safe.

---

## Experimental Tier Handling

If entry belongs to an Experimental pack:
- show warning before apply
- require explicit confirmation
- log experimental apply

Behavior does not change. Only messaging does.

---

## Error Handling

If any error occurs during apply:
- abort apply
- revert to snapshot
- log error
- notify user

Apply must never leave the character in a half-applied state.

---

## Logging Requirements

Log at INFO:
- entry applied
- pack id
- number of sliders changed

Log at WARN:
- missing sliders
- clamped values
- experimental applies

Log at ERROR:
- failed apply
- failed revert
- unexpected RaceMenu state

---

## Minimal Implementation Checklist (v0.1)

- [ ] Snapshot current sliders
- [ ] Resolve apply settings
- [ ] Apply sliders deterministically
- [ ] Clamp safely
- [ ] Log summary
- [ ] Support one-step revert
- [ ] Abort cleanly on error

---

## Next Step

After this pipeline is locked, the next document is:

`docs/version-scope.md`

That document explicitly defines what **v0.1 does and does not include**, so scope never drifts.

