---
name: mission
description: A mission repo. Agents coordinate one mission here (tasks, claims, working copies, acknowledgements) and write annotations on a target knowledge base's facts; the knowledge itself goes to that target knowledge base.
---

# mission

This file is the template's manifest. It is not part of the repo you create:
leave it out when you copy the template.

## Where it came from

Copied from the knomit repository, `examples/mission/` at commit
`a2a4b6acc994bbe4f3ba46ffa01ceaf59dab2b66`
(https://github.com/knomit/knomit/tree/a2a4b6acc994bbe4f3ba46ffa01ceaf59dab2b66/examples/mission).
The only change is the "Copy it" section of `README.md`, which now names this
folder instead of `examples/mission/`.

## What is in it

Everything next to this file is copied to the same path in the new repo:

- `README.md`: how the mission repo works. Read it before you edit anything.
- `.knomit/ontology.yaml`: the topics, validations, repo settings and triggers.
- `.knomit/triggers/claims.js` and `.knomit/triggers/awards.js`: the two ways
  to take a task.
- `.knomit/skills/post-task/SKILL.md` and `.knomit/skills/work-task/SKILL.md`:
  how a session posts a task and how it works its queue.
- `.knomit/recipes/work-task.js`: the sample recipe that starts one headless
  session to work the queue.

## What to edit after copying

- **Hosting.** The ontology's `consensus: auto` is for a mission repo hosted
  by a knomit instance. For a repo on GitHub or another forge, drop that line
  and read the README's "GitHub-hosted" section first.
- **Timing.** `WINDOW_SECONDS` in `claims.js` follows from the sync settings;
  if you change `sync`, redo the README's "The timing rule".
- **Capacity and lease.** `CAPACITY` and `LEASE_SECONDS` in `claims.js` and
  `awards.js` (README "Capacity and parallelism" and "The queue").
- **The session.** `.knomit/recipes/work-task.js` starts the agent each copy
  runs in: its budget, its tools and the command. Change it to match the
  agent you run.
- **The skills.** Each mission has its own skills: rewrite `post-task` and
  `work-task` for the work this mission does, and keep the call skeletons
  (README "Skills").
- **The target knowledge base.** The mission's charter names it; the mission
  repo only points at it (README "The target knowledge base").
