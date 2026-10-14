# Fleet repository

The source of truth for one knomit fleet. Each agent's member record is a
fact at `kb/members/<agent-id>/<id>.md`: its state (`active`, `left` or
`revoked`), its current signing key, and its host and branch. Records are
never deleted; earlier states and keys stay readable as the record's
history.

An instance joins by writing its own record on its agent branch. A person
accepts it by merging that branch into `main`. Everything under `.knomit/`
changes through git only.

Agent ids are lower case. They are compared exactly, and nothing folds their
case.

The ontology turns signature verification on (`verify_signatures: enforce`).
Every commit that enters `main` is judged against the member records. Once
the fleet has a root certificate, it is also judged against the certificates
under `artifacts/fleet/`. The ontology's `artifacts:` section declares these
files and the change rules the judge enforces on them:

- `root.crt` is the fleet root certificate. It can be added only while it is
  missing, and it never changes after that.
- `crl.pem` is the revocation list. A replacement must be signed by the root
  and carry a higher Number. It must also still list every serial that the
  list it replaces lists.
- `certs/<agent-id>.pem` holds each member's current certificate. It can be
  replaced only by a renewal for that member.
- `issued/*.pem` holds one file per issued certificate: the certificate
  itself. These files are written once.
- `revocations/*.txt` holds one file per revocation: a statement signed by
  the root. These files are written once.

The names of the files under `issued/` and `revocations/` only keep the files
apart. Nothing reads an order or a meaning from a file name.

## Setting up a fleet: the root first, members after

1. Create the fleet repository.
2. As its first commit, add `artifacts/fleet/root.crt` and
   `artifacts/fleet/crl.pem`.
3. Only after that, add members.

The root can be added only while it is missing. Adding it first, before any
member exists, leaves no window in which someone else can add a root of
their own.
