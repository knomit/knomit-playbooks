---
type: process
domain: [templates, fleet]
confidence: 0.9
sources: 1
entities: [fleet, IsFleetOntology, not_a_fleet, already_registered]
refs: ['kb://034f37d5b4a5/kb/templates/fleet/files/ontology/b13e3549.md', 'src://7b4887ce51d9/internal/repos/fleet.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:c1296c0109515c2df09bd94fb9f7a1262c1f147e']
context: {part: edit, template: fleet}
---
# Fleet edit, the id: keep the ontology id fleet; knomit finds the fleet repository by it, and with another id joining is refused (not_a_fleet)

Do not change `id: fleet` in `.knomit/ontology.yaml`. knomit recognises the fleet repository by that id (`fact.IsFleetOntology`). With another id, `knomit fleet register` refuses the repo with `not_a_fleet` and the fleet code ignores it.

The other side of the same rule: never create a repo with the id `fleet` as a local repo (`mode: custom` or `preset`). knomit treats any mounted repo with that id as the instance's fleet, and registering with the real fleet then fails with `already_registered`.
