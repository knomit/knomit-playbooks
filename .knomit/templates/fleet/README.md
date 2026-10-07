# Fleet repository

The source of truth for one knomit fleet. Each agent's member record is a
fact at `kb/members/<agent-id>/<id>.md`: its state (`active`, `left` or
`revoked`), its current signing key, and its host and branch. Records are
never deleted; earlier states and keys stay readable as the record's
history.

An instance joins by writing its own record on its agent branch. A person
accepts it by merging that branch into `main`. Everything under `.knomit/`
changes through git only.
