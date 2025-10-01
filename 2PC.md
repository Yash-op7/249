
### Imagine this:

You have a **banking system** with 2 replicas:

- Node A: Has user balances
- Node B: Has transaction logs

You want to:

> Transfer $100 from Alice to Bob

So you must:

- Subtract $100 in Node A
- Add entry in Node B

You need **both nodes to commit** this change.  
But what if:

- Node A crashes after writing?
- Node B times out?
---
## 🧠 Enter: Two-Phase Commit (2PC)

2PC is trying to **coordinate agreement** _before_ making irreversible changes.

---

Now you're in an inconsistent state: one node changed data, the other didn't.
### ✅ Phase 1: Prepare

Coordinator sends:

> "Can you commit this transaction?"

Each participant does a **dry-run**:

- Writes everything to a **temporary log**
- Ensures it's ready to commit
- Checks for issues (e.g., disk space, locks)

Then replies:

- ✅ **YES** = "I’ve written it to disk, I’m ready to commit"
- ❌ **NO** = "Something's wrong, I can't commit"

**Nothing is actually committed yet.**

---

### ✅ Phase 2: Commit or Abort

If all participants said **YES**:

- Coordinator says: **COMMIT**
- Participants commit and **write to disk**

If **anyone said NO**:

- Coordinator says: **ABORT**
- Everyone discards the prepared state