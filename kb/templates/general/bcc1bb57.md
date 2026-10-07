---
type: reference
domain: [templates, ontology]
confidence: 0.9
sources: 1
entities: [general template, general, default preset]
refs: ['kb://034f37d5b4a5/kb/howto/templates/create-repo/8eba1951.md', 'kb://034f37d5b4a5/kb/templates/general/files/ontology/bc074670.md', 'kb://034f37d5b4a5/kb/templates/general/files/readme/c462495d.md', 'kb://034f37d5b4a5/kb/templates/general/use/readme/2700ed54.md', 'kb://034f37d5b4a5/kb/templates/general/use/taxonomy/1d527aa2.md']
context: {part: template, template: general}
---
# The general template: a general knowledge base on any subject, knomit's built-in general preset (ontology id general) with a broad subject taxonomy, ontology only

Template folder: `.knomit/templates/general/`.

**What it is for.** A knowledge base about any subject. Its ontology is knomit's embedded `default` preset (id `general`): thirteen top-level subject topics, each with child categories, and no validations, triggers, skills or recipes.

**Its files** (one fact each under `kb/templates/general/files/`): `README.md` and `.knomit/ontology.yaml`, landing at the same paths in the new repo.

**What to edit** (under `kb/templates/general/use/`): the taxonomy (or nothing, to keep the preset and its upgrades), and the README.

**How to create a repo from it.** It is an ontology only. Create a repo from the template: `POST /api/v1/repos` with mode `template`, `kb repo create <new repo> --template knomit-playbooks/general`, or the web UI wizard's **From a template**. knomit copies `README.md` and `.knomit/ontology.yaml` to the same paths in the new repo, in one signed commit, and the new repo is an ordinary repo afterwards (`kb/howto/templates/create-repo/`).

```
POST /api/v1/repos
{"name": "<new repo>", "mode": "template", "template": {"repo": "knomit-playbooks", "name": "general"}}
```

With an edited ontology, use `mode: custom` with the edited file as `ontology_yaml`.
