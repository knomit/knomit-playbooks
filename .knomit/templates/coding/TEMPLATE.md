---
name: coding
description: A knowledge base for a codebase - invariants, architecture, conventions, decisions, gotchas, incidents, meta and principles. The same ontology as knomit's built-in source-code preset (ontology id source-code).
---

# coding

This file is the template's manifest. It is not part of the repo you create:
leave it out when you copy the template.

## Where it came from

Copied byte for byte from the knomit repository,
`internal/fact/ontology_code.yaml` at commit
`0e9288eb1d53759184f5bb3e57b5d257cd277ea3`
(https://github.com/knomit/knomit/blob/0e9288eb1d53759184f5bb3e57b5d257cd277ea3/internal/fact/ontology_code.yaml),
to `.knomit/ontology.yaml`. It is the ontology knomit embeds as its `code`
preset.

## What is in it

- `README.md`: the new repo's README.
- `.knomit/ontology.yaml`: the topics `invariants`, `architecture`,
  `conventions`, `decisions`, `gotchas`, `incidents`, `meta` and
  `principles`, each with child categories. `principles` carries four
  validations: a principle is `kind: pragmatic`, `type: policy`, has the
  entity `designer`, and has a domain that is either `[global]` or area
  paths. No triggers, skills or recipes.

## What to edit after copying

- **Keep it as it is** to get the source-code preset. The ontology keeps the
  id `source-code`, so knomit treats the repo exactly like one created from
  the built-in preset: when a newer knomit ships a preset that only adds to
  this one, the repo's ontology is upgraded to it when the repo opens.
- **Change the topics or validations** to fit your codebase. Then also change
  `id`, `name` and `description`: a repo whose ontology has diverged from
  the preset is not upgraded, and a different id says so.
- **README.md**: name the codebase this knowledge base describes.
