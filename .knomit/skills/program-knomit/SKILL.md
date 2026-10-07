---
name: program-knomit
description: Use when asked to set something up in knomit - a new knowledge base or mission repo, a trigger, a recipe, a skill, context keys, guidance - or to explain how to program knomit. Finds the answer in the knomit-playbooks knowledge base and starts from one of its templates.
---

# Program knomit

Use this knowledge base to answer "set up X in knomit", "how do I make knomit
do Y", or "create a repo for Z". It holds how-tos, patterns, reference and
gotchas under `kb/`, and templates under `.knomit/templates/`, each
described by facts under `kb/templates/` that ref its files.

$ARGUMENTS

## 1. Bind to this knowledge base

knomit serves this skill on a binding of the `knomit-playbooks` repo itself.
On the unscoped MCP endpoint, bind to it and keep the handle:

```json knomit_bind
{"repo": "knomit-playbooks"}
```

Pass the returned `binding` on every call below. On this repo's own
endpoint, `/api/v1/repos/knomit-playbooks/branches/main/mcp`, the binding
comes from the URL: leave `binding` out.

On most instances `knomit-playbooks` is a subscription, which refuses writes.
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
| templates | `kb/templates/` | per template: what it is for, one fact per file it carries, what to edit |

A result row carries a snippet only. Read each fact you rely on in full:

```json knomit_explain
{"binding": "<handle>", "file": "<the file value from the result row>"}
```

Pass `file` exactly as the result row gives it. Through a lens, a fact from
`knomit-playbooks` comes back as `kb://<repo-id>/kb/...`; pass that whole
value. A fact names the knomit source files it was verified against in its
refs.

## 3. Pick a template

Each template has one fact with context `part: template`: what the template
is for and how to create a repo from it. List them:

```json knomit_query
{"binding": "<handle>", "path": "kb/templates/", "context": {"part": "template"}, "text": "<what you are asked to set up>"}
```

| Template | For |
|---|---|
| `mission` | the agents of one mission coordinating (tasks, claims, queues, acknowledgements) and annotating a target knowledge base |
| `general` | a general knowledge base (knomit's preset `default`, ontology id `general`) |
| `coding` | a knowledge base for a codebase (knomit's preset `code`, ontology id `source-code`) |
| `fleet` | a fleet repository: one member record per agent (knomit's preset `fleet`) |

Pick the closest one. Its facts all carry context `template: <name>`.

## 4. Read the template and its files

Explain the template fact:

```json knomit_explain
{"binding": "<handle>", "file": "<the file value of the template's row>"}
```

The walk returns the template fact in full and, as summary nodes, what it
refs:

- the **file facts** (`kb/templates/<name>/files/...`, context `part: file`):
  one per file the template carries, saying what the file does, its knobs,
  and where it lands in the new repo;
- the **edit facts** (`kb/templates/<name>/use/<topic>/...`, context
  `part: edit`): one per change to make after creating the repo, such as
  hosting, timing or the skills;
- under each file fact, a `system_file` node for the file itself, at its
  exact path under `.knomit/templates/<name>/`.

When the result has `has_more: true`, call again with its `cursor` for the
rest of the walk.

Read each file fact and each edit fact in full by explaining its `path` and
`commit`. Read each file's content by explaining the `system_file` node's
`path` and `commit`:

```json knomit_explain
{"binding": "<handle>", "file": ".knomit/templates/<name>/<path>", "commit": "<the node's commit>"}
```

It comes back as one node with kind `system_file` and the raw file in
`content`. Through a lens, pass the path exactly as the walk gives it,
`kb://<repo-id>/.knomit/templates/<name>/<path>`. Leave `commit` out to read
the file at the tip of the branch.

A file at `.knomit/templates/<name>/<path>` lands at `<path>` in the new
repo: `.knomit/templates/mission/.knomit/triggers/claims.js` becomes
`.knomit/triggers/claims.js`, and `.knomit/templates/mission/README.md`
becomes `README.md`.

Then decide the edits: the ones the edit facts list, and the ones the
request needs, in the ontology (`.knomit/ontology.yaml`), the skills
(`.knomit/skills/`), the recipes (`.knomit/recipes/`) and the trigger
scripts (`.knomit/triggers/`). The `kb/howto/` facts say how to write each.

## 5. Create the knomit repo

A repo's ontology is set when knomit creates the repo, so create it from the
finished files, never before them.

- **The template is an ontology only** (`general`, `coding`, or any
  template whose only file under `.knomit/` is `ontology.yaml`, except
  `fleet`): create a local repo with the edited ontology inline.

  ```
  POST /api/v1/repos
  {"name": "<new repo>", "mode": "custom", "ontology_yaml": "<the edited .knomit/ontology.yaml>"}
  ```

- **The template has skills, recipes or trigger scripts** (`mission`):
  write every file to its path in a new git repository, commit, push it to
  a git remote, and create the knomit repo by cloning that remote.

  ```sh
  git init my-repo
  # write each file's content to my-repo/<path>, with your edits
  git -C my-repo add -A
  git -C my-repo commit -m "<name> template"
  git -C my-repo push <git URL of my-repo> HEAD:main
  ```

  ```
  POST /api/v1/repos
  {"name": "<new repo>", "mode": "clone", "origin": {"url": "<git URL of my-repo>", "branch": "main"}}
  ```

  Then follow the template's `README.md` for hosting (the `mission`
  template's "Copy it" section).

- **A fleet repository** (ontology id `fleet`) is never created as a local
  repo: knomit treats any mounted repo with that id as the instance's fleet,
  and registering with the real fleet then fails. Write its files to a new
  git repository as above, push it where every instance can reach it, and
  each instance joins with `knomit fleet register <git URL>` (REST:
  `PUT /api/v1/fleet {"url": "<git URL>"}`).

Creating a repo is an operator act, through the REST API or the web UI. If
you cannot reach the REST API, give the person the exact request.

## 6. Check before you finish

- Query `kb/gotchas/` for the area you touched.
- Every new ontology returns `"ok": true` from `POST /api/v1/ontologies:validate`,
  with the YAML file as the request body.
- Skills and recipes are read from the tip of the new repo's consensus
  branch: a change to one on an agent branch takes effect once it is merged
  there.
