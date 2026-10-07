---
type: reference
domain: [templates, ontology]
confidence: 0.9
sources: 1
entities: [general template, general, default preset]
refs: ['kb://034f37d5b4a5/kb/howto/templates/create-repo/8eba1951.md', 'kb://034f37d5b4a5/kb/templates/general/files/ontology/bc074670.md', 'kb://034f37d5b4a5/kb/templates/general/files/readme/c462495d.md', 'kb://034f37d5b4a5/kb/templates/general/use/readme/2700ed54.md', 'kb://034f37d5b4a5/kb/templates/general/use/taxonomy/1d527aa2.md']
context: {part: template, template: general}
---
# The general template is knomit's built-in general preset (ontology id general) as a template: a broad subject taxonomy, ontology only, created as a custom local repo

Template folder: `.knomit/templates/general/`.

**What it is for.** A knowledge base about any subject. Its ontology is knomit's embedded `default` preset (id `general`): thirteen top-level subject topics, each with child categories, and no validations, triggers, skills or recipes.

**Its files** (one fact each under `kb/templates/general/files/`): `README.md` and `.knomit/ontology.yaml`, landing at the same paths in the new repo.

**What to edit** (under `kb/templates/general/use/`): the taxonomy (or nothing, to keep the preset and its upgrades), and the README.

**How to create a repo from it.** It is an ontology only: create a local repo with the file's content as `ontology_yaml`, or write both files to a git repository and clone it (`kb/howto/templates/create-repo/`).

```
POST /api/v1/repos
{"name": "<new repo>", "mode": "custom", "ontology_yaml": "<.knomit/templates/general/.knomit/ontology.yaml>"}
```
