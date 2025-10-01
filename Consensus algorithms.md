## 🧠 Paxos: The Consensus Problem

When you want **multiple unreliable machines** to **agree on a value** (e.g., whether to commit a transaction), you need **consensus algorithms**.

**Paxos** is one of the most famous and battle-tested (used in Google's Chubby lock service, etc.)

---

### 🧩 The Consensus Problem

> In an unreliable network, how do N machines agree on a value (e.g., "commit") such that:
> - Only one value is chosen
> - Everyone eventually learns that value
> - Even with some failures or delays

---
### ⚙️ Paxos Roles

| Role         | Description                        |
| ------------ | ---------------------------------- |
| **Proposer** | Proposes a value to be agreed upon |
| **Acceptor** | Votes on proposals                 |
| **Learner**  | Learns the final agreed-upon value |

---
### 🔁 Paxos Steps (simplified)

1. **Prepare Phase**:
    - Proposer asks acceptors: "Can I propose value X with ballot #42?"
    - Acceptors respond with:
        - OK (and any previously accepted value)
        - Or reject if ballot is too old
2. **Accept Phase**:
    - Proposer sends accepted value with same ballot
    - Acceptors accept it (if still valid)
3. **Learn**:
    - Once a majority accepts, the value is chosen
    - Learners are notified

---

### ✅ Paxos Guarantees:

- **No split-brain** (only one value chosen)
    
- **Survives crashes**
    
- **No blocking** (unlike 2PC)
    

---

### ❌ Paxos Downsides:

- Very **hard to implement** correctly
    
- Complex message flow
    
- Requires majority of nodes (quorum) to be online