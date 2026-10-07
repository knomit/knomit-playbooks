---
type: process
domain: [repos, subscribe]
confidence: 0.9
sources: 1
entities: [subscribe, POST /api/v1/repos, knomit_bind, ErrLensWriteSubscribed, subscription]
refs: ['src://7b4887ce51d9/internal/repos/lifecycle.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:796ad8c466a76b7374e1e6d37e9c6ec70886aadc', 'src://7b4887ce51d9/internal/mcp/bind.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:1deba551896ff59779575a8ee13617d5cf6a52e6', 'src://7b4887ce51d9/internal/repos/manager.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:627b232058bdf78681b75824d43c9e5b9166038f', 'src://7b4887ce51d9/internal/repos/stages.go@22125f4f18249c1c9b8b855033b17f767fc1bdc8:30686f4d5efe58092e0c03953a92c5dd049604bd']
---
# Subscribe to a repo with mode subscribe: a read-only follower of one branch, with no agent branch, no writes and no push, that still serves its facts and skills

**Request.**

```
POST /api/v1/repos
{"name": "knomit-playbooks", "mode": "subscribe",
 "origin": {"url": "https://github.com/knomit/knomit-playbooks", "branch": "main"}}
```

In the web UI: add a repo from the URL and choose **Subscribe**. Without `branch`, knomit follows the remote's HEAD unless that is an agent branch. The followed branch must already hold a knomit ontology; the subscription takes it from there and refuses `ontology_preset` and `ontology_yaml`.

**What a subscription is.**
- It fetches the origin at each sync round and follows the branch. It never pushes and has no agent branch.
- `knomit_bind {"repo": "<name>"}` binds at the followed branch. Reads work (`knomit_query`, `knomit_explain`); every write is refused.
- Its skills are served on a binding of the subscription itself (`kb/gotchas/skills/write-repo-only/`).
- It can be a READ member of a lens; it can never be a lens's write repo (refused as `lens write repo is a subscription`).
- Its ontology is never upgraded to a newer embedded preset: a subscription never writes.

**Trust.** You trust the repo you subscribe to, as with any git remote: a fork, or a repo you build yourself with the same layout, works the same way.

