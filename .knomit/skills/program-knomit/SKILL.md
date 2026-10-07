---
name: program-knomit
description: Use when asked to set something up in knomit - a new knowledge base or mission repo, a trigger, a recipe, a skill, context keys, guidance - or to explain how to program knomit. Finds the answer in the knomit-recipes knowledge base and starts from one of its templates.
---

# Program knomit

Use this knowledge base to answer "set up X in knomit", "how do I make knomit
do Y", or "create a repo for Z". It holds how-tos, patterns, reference and
gotchas under `kb/`, and templates under `.knomit/templates/`.

$ARGUMENTS

## 1. Bind to this knowledge base

knomit serves this skill on a binding of the `knomit-recipes` repo itself.
On the unscoped MCP endpoint, bind to it and keep the handle:

```json knomit_bind
{"repo": "knomit-recipes"}
```

Pass the returned `binding` on every call below. On an endpoint scoped by
its URL (this repo's own `/api/v1/repos/knomit-recipes/branches/main/mcp`, or
a bridge started with `--repo` or `--lens`), leave `binding` out.

On most instances `knomit-recipes` is a subscription, which refuses writes.
Unless you were asked to add to this knowledge base, write nothing to it:
every write in the steps below goes to the repo you are setting up.

## 2. Find what applies

Query by topic. End every `path` with `/`: it is a plain string prefix.

```json knomit_query
{"binding": "<handle>", "path": "kb/howto/", "text": "<what you are asked to set up>"}
```

| Topic | `path` | What it holds |
|---|---|---|
| howto | `kb/howto/` | step-by-step instructions for one task |
| patterns | `kb/patterns/` | designs built from knomit's primitives, such as the mission repo |
| reference | `kb/reference/` | what each key does |
| gotchas | `kb/gotchas/` | sharp edges; check them before you finish |
| templates | `kb/templates/` | one fact per template: what it is for, what to edit |

A result row carries a snippet only. Read each fact you rely on in full:

```json knomit_explain
{"binding": "<handle>", "file": "<the file value from the result row>"}
```

Pass `file` exactly as the result row gives it. Through a lens, a fact from
`knomit-recipes` comes back as `kb://<repo-id>/kb/...`; pass that whole
value. A fact names the knomit source files it was verified against in its
refs.

## 3. Pick a template

List the templates:

```json knomit_query
{"binding": "<handle>", "path": "kb/templates/", "limit": 20}
```

| Template | For |
|---|---|
| `mission` | the agents of one mission coordinating (tasks, claims, queues, acknowledgements) and annotating a target knowledge base |
| `general` | a general knowledge base (knomit's `general` preset) |
| `coding` | a knowledge base for a codebase (knomit's `source-code` preset) |
| `fleet` | a fleet repository: one member record per agent (knomit's `fleet` preset) |

Pick the closest one. Each template folder has a `TEMPLATE.md` with what to
edit after copying.

## 4. Get the template files

The templates are files in git, under `.knomit/templates/<name>/`. No knomit
tool reads them: a path under `.knomit/` is closed to `knomit_query` and
`knomit_explain`. Clone the repo (or the fork your instance subscribes to):

```sh
git clone https://github.com/knomit/knomit-recipes
git init my-repo
cp -R knomit-recipes/.knomit/templates/<name>/. my-repo/   # the trailing /. copies .knomit/ too
rm my-repo/TEMPLATE.md
```

Then make the edits `TEMPLATE.md` lists, and the ones the request needs:
the ontology (`.knomit/ontology.yaml`), the skills (`.knomit/skills/`), the
recipes (`.knomit/recipes/`) and the trigger scripts (`.knomit/triggers/`).
The `kb/howto/` facts say how to write each. Commit:

```sh
git -C my-repo add -A
git -C my-repo commit -m "<name> template"
```

## 5. Create the knomit repo

A repo's ontology is set when knomit creates the repo, so create it from the
finished files, never before them.

- **The template is an ontology only** (`general`, `coding`, `fleet`, or
  any template whose only file under `.knomit/` is `ontology.yaml`): create
  a local repo with the ontology inline.

  ```
  POST /api/v1/repos
  {"name": "<new repo>", "mode": "custom", "ontology_yaml": "<contents of my-repo/.knomit/ontology.yaml>"}
  ```

- **The template has skills, recipes or trigger scripts** (`mission`):
  push `my-repo` to a git remote and create the knomit repo by cloning it.

  ```
  POST /api/v1/repos
  {"name": "<new repo>", "mode": "clone", "origin": {"url": "<git URL of my-repo>", "branch": "main"}}
  ```

  Then follow the template's `README.md` for hosting (the `mission`
  template's "Copy it" section).

- **A fleet repository** is not created on one instance: push `my-repo`
  where every instance can reach it, and each instance joins with
  `PUT /api/v1/fleet {"url": "<git URL>"}`.

Creating a repo is an operator act, through the REST API or the web UI. If
you cannot reach the REST API, give the person the exact request.

## 6. Check before you finish

- Query `kb/gotchas/` for the area you touched.
- Every new ontology returns `"ok": true` from `POST /api/v1/ontologies:validate`,
  with the YAML file as the request body.
- Skills and recipes are read from the tip of the new repo's consensus
  branch: a change to one on an agent branch takes effect once it is merged
  there.
