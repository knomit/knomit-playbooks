# knomit-recipes

knomit-recipes is a knomit knowledge base for programming knomit. It holds:

- **`kb/`**: facts on how to program knomit, under the topics `howto`,
  `patterns`, `reference`, `gotchas` and `templates`.
- **`.knomit/templates/`**: templates for new knomit repos. Each template is
  the `.knomit/` tree (ontology, skills, recipes, trigger scripts) and the
  `README.md` of the repo it creates.
- **`.knomit/artifacts/`**: standalone pieces to copy into your own repos.

## Contributions

This repo is maintained by its owner only. Pull requests from anyone else are
closed without review, and no agent or knomit instance writes to it. For a
different template, fork the repo or build your own with the same layout.

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

## Templates

- [`mission`](.knomit/templates/mission/TEMPLATE.md): a mission repo. The
  agents of one mission coordinate there (tasks, claims, working copies,
  acknowledgements) and annotate facts in a target knowledge base.

Each template folder has a `TEMPLATE.md` with its name, its description, what
to edit after copying, and its source. `TEMPLATE.md` is not part of the
created repo.

## Artifacts

- [`recipes/claude-session.js`](.knomit/artifacts/recipes/claude-session.js):
  a recipe that starts one headless Claude Code session. Copy it to
  `.knomit/recipes/` in your repo.

## Layout

```
kb/                  facts: howto, patterns, reference, gotchas, templates
.knomit/
  ontology.yaml      this knowledge base's topics
  templates/<name>/  one folder per template, with its TEMPLATE.md
  artifacts/         standalone pieces
```

Files under `.knomit/templates/` and `.knomit/artifacts/` are inert in this
repo: they are not indexed as facts, their triggers do not fire, and their
recipes do not run. Everything under `.knomit/` changes through git only.

## License

See [LICENSE](LICENSE).
