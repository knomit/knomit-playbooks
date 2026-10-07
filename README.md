# knomit-playbooks

knomit-playbooks is a knomit knowledge base of playbooks for programming
knomit. It holds:

- **`kb/`**: facts on how to program knomit, under the topics `howto`,
  `patterns`, `reference`, `gotchas` and `templates`.
- **`.knomit/templates/`**: templates for new knomit repos. Each template is
  the `.knomit/` tree (ontology, skills, recipes, trigger scripts) and the
  `README.md` of the repo it creates. The facts under `kb/templates/<name>/`
  describe each template and each file in it, and ref every file by its
  path.
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

In the knomit web UI, add a repo from `https://github.com/knomit/knomit-playbooks`,
branch `main`, and choose **Subscribe**. With the REST API:

```
POST /api/v1/repos
{"name": "knomit-playbooks", "mode": "subscribe",
 "origin": {"url": "https://github.com/knomit/knomit-playbooks", "branch": "main"}}
```

Trust follows git: you trust the repo you subscribe to. A fork, or a repo you
make with the same layout, works the same way.

## Find a template

Templates are found by querying `kb/templates/`. Each template has:

- one fact with context `part: template`: what the template is for and how
  to create a repo from it;
- one fact per file it carries, with context `part: file`
  (`kb/templates/<name>/files/...`): what the file does, its knobs, and
  where it lands in the new repo. It refs the file by its path, such as
  `.knomit/templates/mission/.knomit/triggers/claims.js`;
- one fact per change to make after creating the repo, with context
  `part: edit` (`kb/templates/<name>/use/<topic>/...`).

Every fact of a template carries context `template: <name>`. List the
templates:

```json knomit_query
{"binding": "<handle>", "path": "kb/templates/", "context": {"part": "template"}}
```

`knomit_explain` on a template fact walks its file facts and edit facts,
and under each file fact the file itself. `knomit_explain` on a file's path
returns the file's content:

```json knomit_explain
{"binding": "<handle>", "file": ".knomit/templates/mission/.knomit/triggers/claims.js"}
```

Through a lens, the path is `kb://<repo-id>/.knomit/templates/...`, as the
walk returns it.

## Create a repo from a template

knomit creates a repo from a template of a mounted repo, so subscribe to
knomit-playbooks first. `GET /api/v1/templates` lists the templates of every
mounted repo; each `description` is the title of the template's fact.

```
POST /api/v1/repos
{"name": "my-mission", "mode": "template",
 "template": {"repo": "knomit-playbooks", "name": "mission"}}
```

`repo` is the name knomit-playbooks is mounted under; `GET /api/v1/templates`
shows it. The CLI is `kb repo create my-mission --template knomit-playbooks/mission`.
The web UI's create wizard has **From a template** on its ontology step.

knomit copies the template's root `README.md` and everything under `.knomit/`
(a file at `.knomit/templates/<name>/<path>` goes to `<path>` in the new repo)
in one signed commit, and the new repo is an ordinary repo afterwards. This
works the same for `mission`, `general` and `coding`. Mode `template` copies the
template as it is. To start from edited files, write them to a git repository,
push it, create the repo with `"mode": "clone"` and the `origin`, and for a
repo a knomit instance hosts remove the origin afterwards
(`DELETE /api/v1/repos/<repo>/origin`); an ontology-only template can use
`"mode": "custom"` with the edited ontology as `ontology_yaml`. The `mission`
template's `README.md` says how to host and run the mission repo.

A fleet repository is never created as a local repo: knomit treats any
local repo whose ontology id is `fleet` as the instance's fleet, so mode
`template` refuses the `fleet` template. Create it in mode `initialize` on a
git repository every instance of the fleet can reach (a branch with a commit
and no knomit ontology):

```
POST /api/v1/repos
{"name": "my-fleet", "mode": "initialize",
 "origin": {"url": "<git URL>", "branch": "main"},
 "template": {"repo": "knomit-playbooks", "name": "fleet"}}
```

Once a person has merged the creating instance's agent branch into `main`, each
instance joins with `knomit fleet register <url>`.

## Templates

- [`mission`](.knomit/templates/mission/): a mission repo. The agents of
  one mission coordinate there (tasks, claims, working copies,
  acknowledgements) and annotate facts in a target knowledge base.
- [`general`](.knomit/templates/general/): a general knowledge base.
  knomit's built-in preset `default` (ontology id `general`).
- [`coding`](.knomit/templates/coding/): a knowledge base for a codebase.
  knomit's built-in preset `code` (ontology id `source-code`).
- [`fleet`](.knomit/templates/fleet/): a fleet repository, one member
  record per agent. knomit's built-in preset `fleet` (ontology id `fleet`).

## Artifacts

- [`recipes/claude-session.js`](.knomit/artifacts/recipes/claude-session.js):
  a recipe that starts one headless Claude Code session. Copy it to
  `.knomit/recipes/` in your repo.

## The program-knomit skill

[`.knomit/skills/program-knomit/SKILL.md`](.knomit/skills/program-knomit/SKILL.md)
tells an agent how to answer "set up X in knomit": which topics to query,
how to find a template and read its files through knomit, and how to create
the repo, with the exact MCP calls.

knomit serves a repo's skills on a binding of that repo: the `knomit_skill`
tool and the MCP prompts list them. A lens serves the skills of its write
repo only, and a subscription cannot be a lens's write repo, so bind
`knomit-playbooks` itself to get this skill:

```json knomit_bind
{"repo": "knomit-playbooks"}
```

```json knomit_skill
{"binding": "<handle>", "name": "program-knomit"}
```

An MCP client connected to the repo's own endpoint,
`/api/v1/repos/knomit-playbooks/branches/main/mcp`, also gets it as the
`program-knomit` prompt, and calls `knomit_skill` there without a binding.
The facts are queryable from any lens that mounts `knomit-playbooks`.

## Layout

```
kb/                  facts: howto, patterns, reference, gotchas, templates
.knomit/
  ontology.yaml      this knowledge base's topics
  templates/<name>/  one folder per template
  artifacts/         standalone pieces
  skills/<name>/     this knowledge base's skills
```

Files under `.knomit/templates/` and `.knomit/artifacts/` are inert in this
repo: they are not indexed as facts, their triggers do not fire, and their
recipes do not run. `knomit_explain` reads any file under `.knomit/` by its
exact path. Everything under `.knomit/` changes through git only.

## License

See [LICENSE](LICENSE).
