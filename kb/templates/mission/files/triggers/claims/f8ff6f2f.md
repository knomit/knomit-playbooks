---
type: reference
domain: [templates, missions, triggers, scripts]
confidence: 0.9
sources: 1
entities: [claims.js, offer, decide, dup-check, WINDOW_SECONDS, CAPACITY, LEASE_SECONDS, FNV-1a]
refs: ['kb://034f37d5b4a5/.knomit/templates/mission/.knomit/triggers/claims.js']
context: {part: file, template: mission}
---
# mission claims.js: pattern 1, claim / wait / take (offer, decide, dup-check); WINDOW_SECONDS 15, CAPACITY 2 and LEASE_SECONDS 300 at the top are the knobs; lands at .knomit/triggers/claims.js

File: `.knomit/templates/mission/.knomit/triggers/claims.js`. Lands at `.knomit/triggers/claims.js` in the new repo.

The trigger script of pattern 1, claim / wait / take, run by three triggers of the ontology:
- `offer` (on learn of `tasks/**`): this machine claims the task, writing `claims/<task-id>/<agent-id>/` with `expires` = now + `WINDOW_SECONDS`, unless it holds `CAPACITY` copies.
- `decide` (on due of `claims/*/{agent}/**`, so on the claimer's own machine): withdraws the claim if the task is gone or taken; otherwise ranks the live claims by FNV-1a of `task|agent-id` (ties by agent id). The first-ranked claimer takes with one atomic move: write `inbox/<agent-id>/working/` with a lease, and retract the task, its own claim and every claim more than 2X past its `expires`, in one commit; if it is at `CAPACITY` by then, it withdraws its claim instead. Every other claimer re-arms its claim for one more window.
- `dup-check` (on learn of `inbox/**`): when two copies of one task meet, the lower-ranked holder deletes its copy.

**Knobs** (at the top of the file):
- `WINDOW_SECONDS = 15`: the claim window X. It follows from the sync settings (README "The timing rule").
- `CAPACITY = 2`: the backlog (copies in `working/` and `active/`) above which this machine stops claiming and taking.
- `LEASE_SECONDS = 300`: the lease written on a working copy.
