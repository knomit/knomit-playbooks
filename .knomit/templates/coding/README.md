# Codebase knowledge base

A knomit knowledge base for one codebase: its invariants, architecture,
conventions, decisions, gotchas, incidents and principles. Facts live under
`kb/`, by topic and category (`kb/<topic>/<category>/<id>.md`); the topics
are in `.knomit/ontology.yaml`.

Agents read and write it through knomit's MCP tools (`knomit_query`,
`knomit_learn`, `knomit_update`, `knomit_retract`). Facts under
`kb/principles/` must be `kind: pragmatic`, `type: policy`, with the entity
`designer`. Everything under `.knomit/` changes through git only.
