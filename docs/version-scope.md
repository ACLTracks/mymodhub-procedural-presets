# Version Scope

This repo is intentionally scoped.  
Procedural modding only works if the rules stay tight and the guarantees stay real.

This document defines what **v0.1** includes, what it explicitly does not include, and what is tentatively planned next.

---

## v0.1 Goals

v0.1 ships the minimum complete loop:

- Define packs and entries as **validated JSON**
- Provide schemas that enforce safety and consistency
- Provide documentation for pack authors and reviewers
- Define loader flow and apply pipeline contracts
- Prove the system with clean example packs

v0.1 is the foundation. Nothing clever, nothing fragile.

---

## v0.1 Includes

### Pack System
- Pack folder structure
- `manifest.json` schema
- `index.json` schema
- Entry schema for RaceMenu morph operations
- Pack tiers documentation: Featured vs Experimental
- Pack validation checklist and review rubric
- Example pack with multiple entries

### Loader Contracts
- Deterministic load order rules
- Validation rules (schema + required fields)
- Failure behavior: skip, warn, never crash
- Registry model: loaded packs, loaded entries

### Apply Contracts
- Snapshot before apply
- Deterministic apply order
- Safe clamps
- Missing slider behavior
- Revert within session

### Website Compatibility (Authoring Side Only)
- Packs can be generated or edited on a website
- Packs can be exported as a zip
- The game/mod consumes only local files

No runtime network calls. No runtime generation.

---

## v0.1 Does NOT Include

### Runtime AI or Online Features
- No API calls from the game
- No OpenRouter keys in-game
- No “prompt to sliders” live generation
- No telemetry
- No cloud dependency

### Preset Loading and Head Part Management
- No automatic preset importing
- No jslot write-back
- No automatic head part selection
- No guarantee that preset formats stay stable across RaceMenu versions

The user picks parts first. This system only touches morph sliders.

### Body, Skeleton, Textures, and Physics
- No body morphs
- No CBBE/3BA integration
- No skeleton edits
- No HDT/SMP tuning
- No texture swaps
- No ENB configs

v0.1 is strictly about face morph sliders.

### NPC / World Application
- No NPC batch processing
- No distributing generated NPCs
- No world edits
- No leveled list injection
- No SPID usage
- No OAR behavior injection
- No combat package edits

### Save Persistence
- No saved profiles stored in the save file
- No multi-session undo
- No auto-apply on load
- No background daemon

Snapshots are session-only and UI-driven.

### Community Systems
- No rating system
- No built-in moderation tooling
- No reputation system
- No auto-promotion logic beyond documented tier rules

Those are platform features, not v0.1 repo requirements.

---

## Compatibility Guarantees (v0.1)

v0.1 guarantees:

- Packs are validatable offline
- Packs are deterministic
- Packs are additive by default
- Packs do not overwrite head parts
- Packs do not require load order fragility
- A failed pack cannot crash the loader if the loader follows the spec
- Apply can always revert to the snapshot for the current session

If any of these are violated, v0.1 is not shippable.

---

## Quality Bar for v0.1 Release

A v0.1 release is acceptable when:

- Schemas validate the example pack with zero warnings
- Docs are complete and consistent with schemas
- Pack authors can create a pack using docs alone
- Review checklist + rubric can be used by a human reviewer
- Loader flow + apply pipeline have no contradictions
- The repo feels like a real standard, not a vibe

---

## v0.2 Candidates (Next Logical Expansion)

v0.2 is not promised, but likely directions:

### Better Composition
- Weighted stacking rules
- Conflict resolution strategies
- Per-entry “tags as behaviors” (soft, sharp, glam, rugged)

### Preset Interop (Careful)
- Optional import/export helpers
- Explicit “preset bridge” tools, not silent magic
- Clear boundaries so user part choices never get overwritten

### More Schema Types
- Camera presets
- Screenshot / pose kits
- Lighting templates (offline)
- Dialogue / behavior packs (offline configs)

Each expansion must preserve:
- offline determinism
- user control
- non-destructive behavior

---

## v0.3+ (Aspirational)

Long-term, procedural modding can extend to:
- environment look packs
- behavior packs
- combat style packs
- social systems packs

But those require different engines, different safety rules, and probably separate repos.

This repo stays clean and focused until the face pipeline is rock-solid.

---

## One Rule That Never Changes

If it breaks user choice, breaks determinism, or breaks stability, it is out.

Procedural modding is leverage, not chaos.
