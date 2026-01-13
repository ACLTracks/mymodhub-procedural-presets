mymodhub-procedural-presets

Procedural Modding Framework for Skyrim

Overview

MyModHub Procedural Presets defines a data-driven framework for generating, loading, and sharing RaceMenu preset templates without overwriting user choices or breaking load orders.

This is not a collection of finished presets.
This is the system that produces them.

All generation happens offline.
Skyrim only consumes validated data.

Philosophy

Procedural modding replaces static assets with systems.

The mod is not the output.
The mod is the engine that produces the output.

The goal is leverage, not automation.
Structure, not randomness.
Control, not fragility.

What This Enables

Procedural preset packs that:

Are authored as structured data

Install like normal mods

Apply deterministically

Can be shared, remixed, and versioned safely

Never override user-controlled assets

What This Is Not

This framework does not:

Run AI inside Skyrim

Modify skeletons, animations, or physics

Overwrite head parts, hair, eyes, or brows

Require accounts, servers, or online services

Replace RaceMenu sculpting

Core Concepts
Procedural Modding

Systems generate game-ready output from intent, templates, and rules.

Template Packs

A template pack is a data-only mod containing:

Metadata and attribution

Compatibility and requirement flags

One or more morph templates

Safe, additive slider deltas

Template packs are:

Deterministic

Versioned

Installable via MO2 or Vortex

Safe to distribute independently

User Ownership

All visual assets remain under user control.

This framework only applies explicitly defined morph data and skips anything unsupported or missing.

Initial Scope

Version 0.1.x focuses on:

RaceMenu facial morph templates

Additive, non-destructive application

Offline data loading

Clear validation and logging

Future procedural domains may include cameras, environments, and behaviors, but this repository starts with characters.

Repository Structure
mymodhub-procedural-presets/
├─ schemas/        JSON schema definitions
├─ configs/
│  └─ examples/    Example template packs
├─ docs/           Specifications and design notes
├─ tools/          Validators and utilities
└─ skse-plugin/    Runtime implementation reference

Status

Early development.

Currently defined:

Procedural philosophy

Pack format contract

Schema structure

Example data layouts

Runtime implementation follows once the data layer is locked.

License

MIT License for framework code.

Template packs and user-generated content may use their own licenses as defined by their authors.
