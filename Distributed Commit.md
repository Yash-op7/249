## ✅ 1. What is a [[Distributed Commit]]?

A **distributed commit** means **coordinating multiple machines (participants)** to agree whether to **commit or abort** a transaction.

This requires:

- Agreement
- Atomicity (either all commit or none)
- Fault tolerance
---

[[Coordination Protocols]] - [[2PC]] and [[3PC]]
## ⚙️ 2. Two-Phase Commit ([[2PC]])

### 🧠 Basic Idea:

Ensure **all replicas** agree to either **commit** or **abort** a transaction.

### 🏗️ Roles:

- **Coordinator**: Manages the process
- **Participants**: The replicas or database nodes

### 🔁 Phase 1: Prepare

- Coordinator sends `PREPARE` to all participants
- Each participant replies:
    - `YES` (if it can commit safely)
    - `NO` (if it can't)

### 🔁 Phase 2: Commit/Abort

- If **all said YES** → coordinator sends `COMMIT`
- If **any said NO** → coordinator sends `ABORT`
### 🔥 Problem:

If the **coordinator crashes after all YES replies**, but **before sending COMMIT**, participants are left in limbo ("prepared" but unsure what to do).

This is called a **blocking problem**

---
## ⚙️ 3. Three-Phase Commit ([[3PC]])

### Goal:

Avoid the **blocking** problem of 2PC by adding a **third phase**.
### 🔁 Phase 1: CanCommit (like 2PC)

- Coordinator sends: “Can you commit?”
- Participants reply: “Yes” or “No”

---

### 🔁 Phase 2: PreCommit

- If all said "Yes", coordinator sends `PRE-COMMIT` (a signal to prepare)
- Participants acknowledge and **write to disk**: "I'm ready"

Now **everyone knows** a commit is likely — safe to proceed even if coordinator dies.

---

### 🔁 Phase 3: Commit

- Coordinator sends `COMMIT`
- All participants 

### ✅ Benefit:

If the **coordinator fails**, participants **know they were in pre-commit**, and can safely **coordinate among themselves** to commit or abort.

### ❌ Still not perfect:

- Requires strong timing guarantees and **synchronized clocks**
- More complex and **rarely used in real-world systems**

### Next use [[Paxos]]
## 🏗️ [[Paxos]] vs 2PC/3PC

|Feature|2PC|3PC|Paxos|
|---|---|---|---|
|Consensus?|❌ No|❌ No|✅ Yes|
|Blocking?|✅ Yes|❌ No|❌ No|
|Real-world usage|✅ Common|❌ Rare|✅ Used in many DBs|
|Handles crashes?|Partially|Better|✅ Yes|
|Requires majority?|❌ No|❌ No|✅ Yes|