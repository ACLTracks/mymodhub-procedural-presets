Contributing Procedural Packs

This guide explains how to create and submit procedural preset packs for the MyModHub ecosystem.

You do not need to write code.
You do not need to understand SKSE.
You only need to follow the structure and rules below.

If you follow this guide, your pack will work.

What You Are Contributing

You are contributing content, not engine code.

Specifically:

data-driven procedural templates
authored as JSON
validated against schemas
installable like any normal mod

Your pack becomes input to a system, not a replacement for it.

What Makes a Good Pack

A good procedural pack is:

focused
intentional
additive
respectful of user choice

It should:

enhance characters, not overwrite them
work with many setups, not one
feel grounded, not extreme
Think in terms of direction, not domination.

Required Files

Every pack must include:

packs/<your-pack>/
├─ manifest.json
├─ index.json
└─ entries/
   └─ <entry-id>.json


Optional but recommended:

previews/ for images
assets/ for icon or banner
All files must validate against the schemas in /schemas/.

Authoring Rules (Read Carefully)
1. You Do Not Control Head Parts

You must not:

set hair
set eyes
set brows
set head meshes

assume High Poly Head is installed

Users choose their own parts.
Your pack only shapes morphs.

If your pack modifies head parts, it will be rejected.

2. Be Additive by Default

Use:

clamped_additive apply mode
normalized values
conservative ranges
Your pack should nudge, not force.

Absolute morphs are allowed only when clearly documented and justified.

3. Skip Missing Sliders

Not every user has the same RaceMenu setup.

If a slider is missing:

skip it
do not error
do not assume

This is mandatory for compatibility.

4. Avoid Extremes

Avoid:

exaggerated proportions
broken facial topology
values that only look good from one angle
Procedural packs are meant to stack.
Extreme values break stacking.

5. One Intent Per Entry

Each entry should represent:

one style
one direction
one clear outcome
Do not try to do everything in one entry.
Smaller entries remix better.
Metadata Matters
Fill out metadata honestly.
name entries clearly
describe what they do
tag accurately
credit sources if inspired

This helps:

discovery
filtering
remixing
Low-effort metadata hurts the entire ecosystem.
Testing Your Pack
Before submitting or sharing:
Test on a clean character
Test with different head parts
Test with High Poly Head enabled and disabled

Apply your entry twice
The result should remain stable

Remove your pack
Nothing should break

If it fails any of these, fix it first.

Licensing Your Content

You choose the license for your pack.

Common options:

CC-BY-4.0
CC-BY-NC-4.0

MIT

All Rights Reserved

Be clear.
Do not upload content you do not have rights to distribute.

Submitting Packs
Where and how packs are submitted depends on the platform.

Possible paths:

direct upload on MyModHub
shared ZIP files
community curation
third-party hosting
Follow the current submission instructions when available.
What Will Be Rejected

Packs may be rejected if they:

overwrite head parts
break schema rules
hard-require niche setups without reason
abuse extreme morphs
misrepresent compatibility
include stolen or uncredited work

Rejection is not personal.
It protects everyone.

Philosophy for Authors

Procedural modding is collaborative by nature.

Your pack is not the final word.
It is a building block.

Create things others can build on.
That’s how this ecosystem grows.

Questions

If something is unclear:

read docs/pack-structure.md
check example packs
review schemas
ask before guessing
Clear questions prevent broken packs.

Thank you for contributing.

You are helping build a system that scales creativity instead of locking it down.
