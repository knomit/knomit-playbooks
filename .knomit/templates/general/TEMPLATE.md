---
name: general
description: A general knowledge base, with topics for any subject (people, technology, science, society, culture, and more). The same ontology as knomit's built-in general preset (ontology id general).
---

# general

This file is the template's manifest. It is not part of the repo you create:
leave it out when you copy the template.

## Where it came from

Copied byte for byte from the knomit repository,
`internal/fact/ontology_default.yaml` at commit
`0e9288eb1d53759184f5bb3e57b5d257cd277ea3`
(https://github.com/knomit/knomit/blob/0e9288eb1d53759184f5bb3e57b5d257cd277ea3/internal/fact/ontology_default.yaml),
to `.knomit/ontology.yaml`. It is the ontology knomit embeds as its `default`
preset.

## What is in it

- `README.md`: the new repo's README.
- `.knomit/ontology.yaml`: the topics. Each top-level topic (people,
  technology, science, society, culture, geography, history, health,
  philosophy, religion, business, reference, meta) has a few child categories.
  It declares no validations, triggers, skills or recipes.

## What to edit after copying

- **Keep it as it is** to get the general preset. The ontology keeps the id
  `general`, so knomit treats the repo exactly like one created from the
  built-in preset: when a newer knomit ships a preset that only adds to this
  one, the repo's ontology is upgraded to it when the repo opens.
- **Change the topics** to build your own taxonomy. Then also change `id`,
  `name` and `description` at the top of the file: a repo whose ontology
  has diverged from the preset is not upgraded, and a different id says so.
- **README.md**: say what this knowledge base is for.
