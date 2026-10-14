# Fleet repository

The source of truth for one knomit fleet. Each agent's member record is a
fact at `kb/members/<agent-id>/<id>.md`: its state (`active`, `left` or
`revoked`), its current signing key, and its host and branch. Records are
never deleted; earlier states and keys stay readable as the record's
history.

An instance joins by writing its own record on its agent branch. A person
accepts it by merging that branch into `main`. Everything under `.knomit/`
changes through git only.

The ontology turns signature verification on (`verify_signatures: enforce`):
every commit that enters `main` is judged against the member records and,
once the fleet has a root certificate, against the certificates under
`artifacts/fleet/`. Its `artifacts:` section declares those certificate files
and ledgers and the change rules the judge enforces on them.
