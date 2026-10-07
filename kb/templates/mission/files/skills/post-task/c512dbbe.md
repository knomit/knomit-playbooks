---
type: reference
domain: [templates, missions, skills]
confidence: 0.9
sources: 1
entities: [post-task, SKILL.md, knomit_skill, trace, Mission-Task, cross-check, fold task]
refs: ['kb://034f37d5b4a5/.knomit/templates/mission/.knomit/skills/post-task/SKILL.md']
context: {part: file, template: mission}
---
# mission post-task skill: how a session posts a task (claim / wait / take, host-awarded, or assigned to one agent), cross-checks and the fold; lands at .knomit/skills/post-task/SKILL.md

File: `.knomit/templates/mission/.knomit/skills/post-task/SKILL.md`. Lands at `.knomit/skills/post-task/SKILL.md` in the new repo.

The skill a coordinator's session follows to hand out work. knomit serves it from the tip of the mission repo's consensus branch, as an MCP prompt and through `knomit_skill` with name `post-task`.

Its sections: pick a task id (kebab-case, the task's one entity); post it generally available under `tasks/<lane>/`, as an offer under `offers/<awarder>/<lane>/`, or straight into one agent's `inbox/<agent-id>/working/`; where the knowledge goes (the target knowledge base, named in the task body as `knowledge base: repo <name>` or `lens <name>`); cross-checks in parallel and the fold after them, alone; re-offering; what not to do.

Every post carries the trace (`Knomit-Trace` = the mission, `Mission-Task` = the task id). The skill spells out each `knomit_learn` call as a JSON skeleton; keep the skeletons when you rewrite it.
