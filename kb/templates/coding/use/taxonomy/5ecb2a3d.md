---
type: process
domain: [templates, ontology]
confidence: 0.9
sources: 1
entities: [source-code, ontology, preset upgrade, principles]
refs: ['kb://034f37d5b4a5/kb/templates/coding/files/ontology/04a562dd.md', 'src://7b4887ce51d9/internal/repos/stages.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:30686f4d5efe58092e0c03953a92c5dd049604bd']
context: {part: edit, template: coding}
---
# Coding edit, the taxonomy: keep the ontology as it is to get the source-code preset and its upgrades; to fit your codebase, change topics or validations AND the id, name and description

Edit `.knomit/ontology.yaml` before you create the repo, or not at all.

- **Keep it as it is** to get the source-code preset. With the id `source-code`, knomit upgrades the stored ontology to a newer embedded preset when the repo opens, as long as the stored one is a strict subset of it.
- **Change topics or validations** to fit your codebase, and then also change `id`, `name` and `description`. knomit leaves an ontology that is no longer a subset of its preset alone, with a warning.

A repo's ontology is set when knomit creates it. Validate the edited file with `POST /api/v1/ontologies:validate`.
