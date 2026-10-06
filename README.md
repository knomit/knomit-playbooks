# knomit-recipes

## What this repo is

knomit-recipes is a knomit knowledge base with two halves:

- **`kb/`**: facts that teach an agent how to program knomit (how-tos,
  patterns, reference, gotchas). They are queried like any other knowledge
  base, with `knomit_query`. This half is empty today; facts are added by
  later pull requests.
- **`.knomit/templates/`**: templates. Each one is the full `.knomit/` tree
  (ontology, skills, recipes, trigger scripts) of a repo to create, plus that
  repo's `README.md`.

## Who writes it

Only the owner of this repo writes to it, by hand, with Claude Code and git,
as pull requests the owner merges. No knomit instance and no agent writes to
it.

## Contributions are not accepted

Pull requests and changes from anyone other than the owner are closed without
review. Agents never contribute to this repo either. If you want a different
template, fork the repo or make your own (see below).

## How an instance uses it, and why it trusts it

A knomit instance only ever subscribes to this repo. A subscription is
read-only: it has no agent branch, never writes, never pushes, and picks up
changes from the followed branch at each sync round.

Today you add the subscription yourself. In the knomit web UI, add a repo
from `https://github.com/knomit/knomit-recipes`, branch `main`, and choose to
subscribe. Or call the REST API, `POST /api/v1/repos` with:

```json
{"name": "knomit-recipes", "mode": "subscribe",
 "origin": {"url": "https://github.com/knomit/knomit-recipes", "branch": "main"}}
```

Trust comes from which repo you subscribe to: you trust the repo you chose,
as with any git remote. There is no project signing key for this repo. A fork, or a repo you made yourself with the same layout, works the
same way.

Planned, not built yet: knomit subscribing to this repo automatically on
first start, with a setting and an install flag to opt out. Until then
nothing is registered automatically, and the ontology presets built into the
knomit binary are what repo creation offers.

## How to create a repo from a template

Today a template is copied by hand. For the `mission` template:

```sh
git clone https://github.com/knomit/knomit-recipes
git init my-mission
cp -R knomit-recipes/.knomit/templates/mission/. my-mission/   # the trailing /. copies .knomit/ too
rm my-mission/TEMPLATE.md                                     # the manifest is not part of the repo
git -C my-mission add -A
git -C my-mission commit -m "mission template"
```

Then create the knomit repo FROM that git repository (clone it), so that knomit
reads the template's ontology when it creates the repo. The template's own
`README.md` says how to host it.

Planned, not built yet: creating a repo from a template in one step (in the
web UI, the CLI and the REST API), with knomit reading the template from this
repo's subscription. Until it is built, use the steps above.

## Templates

- [`mission`](.knomit/templates/mission/TEMPLATE.md): a mission repo, where
  the agents of one mission coordinate (tasks, claims, working copies,
  acknowledgements) and annotate a target knowledge base's facts.

Each template folder has a `TEMPLATE.md` manifest (its name, its description,
what to edit after copying, and where it came from). The manifest is not
copied into the new repo.

`.knomit/artifacts/` holds system artifacts: standalone pieces that are not a
whole template, for you to copy into a repo of your own.

- [`recipes/claude-session.js`](.knomit/artifacts/recipes/claude-session.js):
  a sample recipe that starts one headless Claude Code session. Copy it to
  `.knomit/recipes/` in your repo.

## Layout

```
.knomit/
  ontology.yaml     this knowledge base's topics
  templates/<name>/ one folder per template, with its TEMPLATE.md
  artifacts/        system artifacts (standalone pieces)
kb/                 the facts (none yet)
```

- **`kb/`** has five topics: `howto`, `patterns`, `reference`, `gotchas` and
  `templates` (one fact per template).
- **`.knomit/templates/`** and **`.knomit/artifacts/`** are inert in this
  repo: they are never indexed as facts, their triggers never fire and their
  recipes never run here.
- **Everything under `.knomit/`** is written through git only, by the owner,
  as pull requests.

## License

See [LICENSE](LICENSE).
