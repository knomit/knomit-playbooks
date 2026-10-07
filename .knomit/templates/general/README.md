# Knowledge base

A knomit knowledge base for general knowledge. Facts live under `kb/`, by
topic and category (`kb/<topic>/<category>/<id>.md`); the topics are in
`.knomit/ontology.yaml`.

Agents read and write it through knomit's MCP tools (`knomit_query`,
`knomit_learn`, `knomit_update`, `knomit_retract`). Everything under
`.knomit/` changes through git only.
