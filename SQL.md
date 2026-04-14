- see also: [[SQL Queries]]

## DB Transactions related syntax

### `FOR UPDATE`

`FOR UPDATE` **locks the selected rows** so that:

- **Other transactions cannot modify them**
    
- Other transactions that also try to lock them **must wait**
    
- The lock lasts **until your transaction commits or rolls back**
    

So this query:
```sql
SELECT state
FROM payments
WHERE id = $1
FOR UPDATE;
```

means:

> “Give me this row **and lock it**, because I plan to make decisions or updates based on it.”

This guarantees:

- Only one request can “own” that row at a time
    
- Your business logic stays correct
    

---

## What exactly gets locked?

`FOR UPDATE` creates a **row-level exclusive lock**:

- ✅ Prevents other transactions from:
    
    - `UPDATE`
        
    - `DELETE`
        
    - `SELECT ... FOR UPDATE`
        
- ❌ Does NOT block:
    
    - Plain `SELECT` (unless they also use `FOR UPDATE`)
        

So other code can still _read_ the row, but cannot change it.

- FOR UPDATE is always used in a transaction
- ⚠️ If you don’t wrap it in a transaction:

- The lock is released immediately
    
- `FOR UPDATE` becomes useless
## Mental model (important)

Think of `FOR UPDATE` as:

> “I am about to make a **decision that depends on this row staying unchanged**.”

Common use cases:

- Payments
    
- Inventory
    
- Account balances
    
- State machines
    
- Job queues
    
- Webhooks that may fire multiple times
## One subtle gotcha (Node/Postgres specific)

If two requests hit the same row:

- One will **wait**, not fail
    
- If you want to fail fast instead, you can use:

`FOR UPDATE NOWAIT`

or

`FOR UPDATE SKIP LOCKED`

(Useful for job queues—happy to explain later.)


### `FOR UPDATE SKIP LOCKED`

### ecap: `FOR UPDATE`

As you already know now:

```sql
FOR UPDATE
```


- Locks selected rows
    
- Other transactions trying to lock the same rows must wait
    

By default, Postgres does this:

> “If a row is locked, **wait** until it becomes available.”

That’s often _not_ what you want for background workers.

## `SKIP LOCKED`: what it does

`FOR UPDATE SKIP LOCKED`

means:

> “Lock the rows you can, and **silently ignore rows that are already locked**.”

No waiting.  
No error.  
Just skip them.

### Mental model

Imagine rows as jobs on a table, and workers as people grabbing jobs:

- `FOR UPDATE` → “Wait in line for this exact job”
    
- `FOR UPDATE NOWAIT` → “Fail if someone else has this job”
    
- `FOR UPDATE SKIP LOCKED` → **“If it’s taken, grab another one”**
    

This makes `SKIP LOCKED` **perfect for queues and workers**.

## `SKIP LOCKED` vs `NOWAIT`

### `NOWAIT`

`FOR UPDATE NOWAIT`

- If _any_ selected row is locked → **error immediately**
    
- Use when:
    
    - You want strict ownership
        
    - You want to fail fast
        
    - You are handling a single entity (e.g. payment)
        

Example use case:

> “If another request is processing this payment, abort.”

---

### `SKIP LOCKED` (what you’re using)

`FOR UPDATE SKIP LOCKED`

- Locked rows are **ignored**
    
- Query still succeeds
    
- You get _only unlocked rows_
    

Example use case:

> “Give me any jobs that aren’t already being processed.”

## Why `SKIP LOCKED` is essential for workers

If you ran **multiple instances** of this publisher:

- Same code
    
- Same DB
    
- Same table
    

Without `SKIP LOCKED`:

- All workers would fight over the same rows
    
- Some would block
    
- Throughput would collapse
    

With `SKIP LOCKED`:

- Each worker gets **a disjoint set of rows**
    
- No coordination required
    
- Perfect horizontal scaling