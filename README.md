# mymodhub-procedural-presets
Procedural modding framework for Skyrim. Defines a data-driven system for generating, loading, and sharing RaceMenu preset templates without overwriting user choices or breaking load orders.

MyModHub Procedural Presets
Procedural modding framework for Skyrim.

This project defines a data-driven system for generating, loading, and sharing RaceMenu preset templates without overwriting user choices or breaking load orders.
This is not a collection of finished presets.
This is the system that produces them.

What This Is

MyModHub Procedural Presets is an engine-first approach to Skyrim modding.
Instead of shipping static, one-off mods, this framework introduces procedural preset packs that:
are authored as structured data
install like normal mods
apply deterministically and safely
can be remixed, shared, and iterated on

The goal is to modernize how character presets are created and distributed while remaining fully offline, stable, and compatible with existing mod setups.

What This Is Not

This project does not:
run AI inside Skyrim
modify skeletons, animations, or physics
overwrite head parts, hair, eyes, or brows
depend on accounts, servers, or runtime services
attempt to replace RaceMenu or sculpting

All generation happens before runtime.
Skyrim only consumes validated data.

Core Concepts
Procedural Modding

Procedural modding replaces static assets with systems that generate game-ready content from intent, templates, and rules.

The mod is not the output.
The mod is the engine that produces the output.

Template Packs

A template pack is a data-only mod that contains:

metadata and attribution

compatibility and requirement flags
one or more morph templates
safe, additive slider deltas

Template packs are:

deterministic
versioned
installable via MO2 or Vortex
shareable without breaking other setups
User Ownership
Head parts, meshes, textures, skeletons, and visual mods always remain under user control.

This framework only touches explicitly defined morph data and skips anything that does not exist or should not be modified.

Initial Scope

Version 0.1.x focuses exclusively on:
RaceMenu facial morph templates
additive, non-destructive application
offline data loading
clear logging and validation

Future systems may expand into cameras, behaviors, environments, and other procedural domains, but this repository starts with characters.

Repository Structure
mymodhub-procedural-presets/
├─ skse-plugin/        # SKSE engine source
├─ papyrus/            # Papyrus scripts if required
├─ schemas/            # JSON schema definitions
├─ configs/
│  └─ examples/        # Example template packs
├─ docs/               # Specifications and design notes
└─ tools/              # Validators and pack utilities

Status

Early development.

The repository currently defines:

the procedural philosophy
the pack format contract
example data structures
Runtime implementation will follow once the data layer is locked.

License
This project is licensed under the MIT License.

Template packs and user-generated content may be distributed under their own licenses as defined by their authors.

Philosophy

Mods should not trap creativity inside finished assets.
They should provide systems that let creativity scale.
Procedural modding is about leverage, not automation.
Structure, not randomness.
Control, not fragility.

Links

Patreon: (https://www.patreon.com/c/Asherz)

Platform: MyModHub.com

Concept: Procedural Modding for Skyrim
