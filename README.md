# knomit-recipes

knomit-recipes is a knomit knowledge base for programming knomit. It holds:

- **`kb/`**: facts on how to program knomit, under the topics `howto`,
  `patterns`, `reference`, `gotchas` and `templates`.
- **`.knomit/templates/`**: templates for new knomit repos. Each template is
  the `.knomit/` tree (ontology, skills, recipes, trigger scripts) and the
  `README.md` of the repo it creates.
- **`.knomit/artifacts/`**: standalone pieces to copy into your own repos.
- **`.knomit/skills/program-knomit/`**: the skill that tells an agent how to
  use this knowledge base.

## Contributions

This repo is written only by its owner and the owner's own agents, through
knomit and git. Pull requests from anyone else are closed without review. For
a different template, fork the repo or build your own with the same layout.

## Subscribe to it

knomit instances use this repo by subscribing to it. A subscription is
read-only: it has no agent branch, never pushes, and picks up new commits on
`main` at each sync.

In the knomit web UI, add a repo from `https://github.com/knomit/knomit-recipes`,
branch `main`, and choose **Subscribe**. With the REST API:

```
POST /api/v1/repos
{"name": "knomit-recipes", "mode": "subscribe",
 "origin": {"url": "https://github.com/knomit/knomit-recipes", "branch": "main"}}
```

Trust follows git: you trust the repo you subscribe to. A fork, or a repo you
make with the same layout, works the same way.

## Create a repo from a template

Example for the `mission` template:

```sh
git clone https://github.com/knomit/knomit-recipes
git init my-mission
cp -R knomit-recipes/.knomit/templates/mission/. my-mission/   # the trailing /. copies .knomit/ too
rm my-mission/TEMPLATE.md
git -C my-mission add -A
git -C my-mission commit -m "mission template"
```

Then create the knomit repo by cloning `my-mission`, so knomit reads the
template's ontology when it creates the repo. The template's `README.md` says
how to host and run it.

A template whose only file under `.knomit/` is `ontology.yaml` (`general`,
`coding`, `fleet`) can also be created as a local repo, with the ontology
inline:

```
POST /api/v1/repos
{"name": "my-kb", "mode": "custom",
 "ontology_yaml": "<contents of .knomit/templates/general/.knomit/ontology.yaml>"}
```

A fleet repository is shared by every instance of the fleet: push it to a
git host they can all reach, and each instance joins with
`knomit fleet register <url>`.

## Templates

- [`mission`](.knomit/templates/mission/TEMPLATE.md): a mission repo. The
  agents of one mission coordinate there (tasks, claims, working copies,
  acknowledgements) and annotate facts in a target knowledge base.
- [`general`](.knomit/templates/general/TEMPLATE.md): a general knowledge
  base. knomit's built-in `general` preset.
- [`coding`](.knomit/templates/coding/TEMPLATE.md): a knowledge base for a
  codebase. knomit's built-in `source-code` preset.
- [`fleet`](.knomit/templates/fleet/TEMPLATE.md): a fleet repository, one
  member record per agent. knomit's built-in `fleet` preset.

Each template folder has a `TEMPLATE.md` with its name, its description, what
to edit after copying, and its source. `TEMPLATE.md` is not part of the
created repo.

## Artifacts

- [`recipes/claude-session.js`](.knomit/artifacts/recipes/claude-session.js):
  a recipe that starts one headless Claude Code session. Copy it to
  `.knomit/recipes/` in your repo.

## The program-knomit skill

[`.knomit/skills/program-knomit/SKILL.md`](.knomit/skills/program-knomit/SKILL.md)
tells an agent how to answer "set up X in knomit": which topics to query,
how to pick and copy a template, and how to create the repo, with the exact
MCP calls.

knomit serves a repo's skills on a binding of that repo: the `knomit_skill`
tool and the MCP prompts list them. A lens serves the skills of its write
repo only, and a subscription cannot be a lens's write repo, so bind
`knomit-recipes` itself to get this skill:

```json knomit_bind
{"repo": "knomit-recipes"}
```

```json knomit_skill
{"binding": "<handle>", "name": "program-knomit"}
```

A bridge started with `--repo knomit-recipes` serves it as the
`program-knomit` prompt too. The facts are queryable from any lens that
mounts `knomit-recipes`.

## Layout

```
kb/                  facts: howto, patterns, reference, gotchas, templates
.knomit/
  ontology.yaml      this knowledge base's topics
  templates/<name>/  one folder per template, with its TEMPLATE.md
  artifacts/         standalone pieces
  skills/<name>/     this knowledge base's skills
```

Files under `.knomit/templates/` and `.knomit/artifacts/` are inert in this
repo: they are not indexed as facts, their triggers do not fire, and their
recipes do not run. Everything under `.knomit/` changes through git only.

## License

See [LICENSE](LICENSE).
