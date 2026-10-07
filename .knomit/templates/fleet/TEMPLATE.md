---
name: fleet
description: A fleet repository - the source of truth for a knomit fleet, one member record per agent under kb/members/<agent-id>/. The same ontology as knomit's built-in fleet preset (ontology id fleet).
---

# fleet

This file is the template's manifest. It is not part of the repo you create:
leave it out when you copy the template.

## Where it came from

Copied byte for byte from the knomit repository,
`internal/fact/ontology_fleet.yaml` at commit
`0e9288eb1d53759184f5bb3e57b5d257cd277ea3`
(https://github.com/knomit/knomit/blob/0e9288eb1d53759184f5bb3e57b5d257cd277ea3/internal/fact/ontology_fleet.yaml),
to `.knomit/ontology.yaml`. It is the ontology knomit embeds as its `fleet`
preset.

## What is in it

- `README.md`: the new repo's README.
- `.knomit/ontology.yaml`: one topic, `members`, with one validation: a
  member record is `kind: pragmatic`, `type: policy`.

## How a fleet uses it

- **One record per agent**, at `kb/members/<agent-id>/<id>.md`. Its body
  starts with `field: value` lines: `agent`, `state` (`active`, `left`
  or `revoked`) and `key` (the agent's current `ssh-ed25519` key line) are
  required; `host`, `branch`, `addresses`, `git` and `capabilities` are
  optional; free text follows. Records are never deleted: leaving or
  revoking is a change of state.
- **Joining.** An instance joins with `knomit fleet register <url>` (REST:
  `PUT /api/v1/fleet {"url": "<url>"}`). knomit clones the repo, refuses it
  unless its ontology id is `fleet`, writes the instance's member record on
  its agent branch and pushes it. A person accepts the instance by merging that agent
  branch into the fleet repo's main branch.

## What to edit after copying

- **Keep the id `fleet`.** knomit recognises the fleet repository by its
  ontology id. With another id, the fleet code ignores the repo and joining
  is refused.
- **Host it** where every instance of the fleet can clone it and push its
  agent branch, such as a private GitHub repository, and decide who merges
  agent branches into main: that merge is what accepts a member.
- **README.md**: name the fleet and say who accepts members.
