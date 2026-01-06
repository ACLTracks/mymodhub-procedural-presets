Pack Structure Guide

This document explains how procedural preset packs are structured in the MyModHub ecosystem, what each file is responsible for, and what content authors should and should not modify.
If you understand this document, you can create valid packs without touching code.

Overview

A procedural preset pack is a data-only mod.

It contains:

metadata
indexing information
one or more content entries
It does not contain:
scripts
binaries
meshes
textures that alter gameplay logic
The engine reads these files and applies them deterministically in-game.

Directory Layout

A standard pack lives at:

packs/<pack-name>/
├─ manifest.json
├─ index.json
├─ entries/
│  ├─ <entry-id>.json
│  └─ <entry-id>.json
├─ previews/
│  └─ <entry-id>.webp
└─ assets/
   ├─ icon.webp
   └─ banner.webp


Only manifest.json, index.json, and entries/*.json are required.

File Responsibilities
manifest.json

System contract. Read by the engine.

This file defines:

pack identity
versioning
compatibility rules
licensing
hard and soft requirements
guarantees about what the pack will and will not modify
This file should change rarely.

Do not:

rename fields
change pack_id after release
lie about compatibility
Think of this as the legal and technical agreement between the pack and the engine.

index.json
UI and discovery layer. Read first.

This file defines:

which entries exist in the pack
how they are displayed
sorting
tags
previews
high-level requirements

The engine uses this file to:

list available entries
filter by tags
show previews
resolve paths to entry files
This file may change more often than the manifest.
entries/*.json
Action layer. This is the content.

Each entry file contains:

one procedural template
slider operations only
no head parts
no meshes
no external state
Entries are:
additive by default
safe to combine

independent of each other

If an entry file is removed, nothing else should break.

Design Rules (Important)
1. Head Parts Are User-Owned

Packs must not:

set hair
set eyes
set brows
set head meshes

assume High Poly Head is active

Users choose their parts first.
Packs only shape morphs.

2. Morphs Must Be Deterministic

Given the same base character and the same entry:

the result should always be the same
no randomness at runtime
no hidden state
This ensures:
reproducibility
shareability
stability

3. Skip What You Don’t Understand

If a slider does not exist:

skip it
log it
do not fail

Never hard-require sliders unless absolutely necessary.

4. Additive by Default

Use:

clamped_additive as the default apply mode
conservative values
normalized units
Absolute values are powerful and should be used sparingly.

5. Packs Are Modular

Packs should:

work alone
work together
not assume load order priority
If two packs affect the same slider, the engine decides resolution order. Packs must not fight each other.
Versioning Guidelines

schema_version refers to the JSON format, not the pack content
version refers to the pack itself

changing structure requires a schema version bump
adding entries does not
Never reuse a version number once published.

What Authors Are Allowed to Do

Authors may:

create new entry files
add tags
add previews
tune morph values
share packs freely

Authors should:

credit sources
document intent
test with multiple base faces
What Authors Should Not Do

Authors should not:

override head parts
assume specific ENBs or textures
encode playstyle assumptions
abuse extreme morph values
break schema rules

If a pack breaks users’ setups, it will be rejected by the engine.

Philosophy

Procedural packs are not finished characters.
They are building blocks.
They are meant to:
be combined
be remixed
be iterated on

The goal is not perfection.
The goal is leverage.

Next Steps

If you want to build a pack:
Copy the example pack
Change metadata
Add entries
Validate against schemas
Test in-game
If you want to build tools:
target index.json for UI
target entries/*.json for generation
never write directly to manifest.json unless intentional

This document defines the contract.

Follow it, and everything works.
