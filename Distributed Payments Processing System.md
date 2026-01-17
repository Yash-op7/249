# 1️⃣ Big Picture: What Are We Building?

We are building a **mini Stripe-like core**, not a full payments product.

**This system does NOT:**

- Talk to real banks
    
- Process real money
    
- Do KYC / PCI / auth UIs
    
- Implement real FX markets
    

**This system DOES:**

- Correctly model **payment lifecycles**
    
- Handle **retries, duplicates, failures**
    
- Use **Kafka for async settlement**
    
- Maintain an **immutable ledger**
    
- Prove **correctness under failure**
    

That’s exactly what backend fintech roles care about.

---

# 2️⃣ Scope Defined by Your Resume (Contract)

Your resume is now a **contract**.  
We must be able to defend _every bullet_ in design and code.

Let’s map them.

---

## Resume Bullet → Concrete Feature Mapping

### 1. _Event-driven payments processing system_

✅ Meaning in code:

- HTTP APIs do **not** do everything synchronously
    
- Kafka topics drive settlement & ledger writes
    
- Services communicate via events, not function calls
    

---

### 2. _Idempotent APIs_

✅ Meaning in code:

- Every `POST /payments` accepts an `Idempotency-Key`
    
- Duplicate requests return the same result
    
- Stored and enforced at DB level
    

---

### 3. _Kafka-based settlement workflows_

✅ Meaning in code:

- `payment.created` → Kafka
    
- `payment.authorized` → Kafka
    
- `payment.captured` → Kafka
    
- Consumers drive state transitions
    

No fake Kafka. Real producer/consumer logic.

---

### 4. _State-machine–driven payment lifecycle_

✅ Explicit states:

`CREATED → AUTHORIZED → CAPTURED → SETTLED`

Rules:

- No skipping states
    
- Invalid transitions rejected
    
- Transitions are atomic + persisted
    

---

### 5. _Immutable financial ledger_

✅ Meaning in code:

- Ledger table is **append-only**
    
- No UPDATEs, no DELETEs
    
- Every balance change = new row
    
- Derived balances are computed, not stored
    

---

### 6. _Correctness under retries & partial failures_

✅ Meaning in code:

- Kafka consumer retries
    
- Dead-letter queue
    
- Reconciliation job that fixes inconsistencies
    

---

### 7. _Currency- and FX-safe arithmetic_

✅ Meaning in code:

- No floats
    
- Minor units (`cents`, `paise`)
    
- Explicit currency codes
    
- Optional FX service with fixed rates
    

---

### 8. _Exactly-once–style processing guarantees_

⚠️ Important: **not true exactly-once** (that’s a lie in most systems)

✅ Meaning in interviews:

- Idempotent consumers
    
- Deduplication keys
    
- Safe reprocessing


## 3️⃣ System Architecture (High-Level)

```scss
┌──────────────┐
│  API Service │
│ (Node + TS)  │
└──────┬───────┘
       │
       ▼
┌──────────────────┐
│  PostgreSQL DB   │
│ payments, ledger │
└──────────────────┘
       │
       ▼
┌──────────────┐
│   Kafka      │
│ (Topics)     │
└──────┬───────┘
       │
       ▼
┌─────────────────────┐
│ Settlement Workers  │
│ Ledger Consumers    │
└─────────────────────┘

```

## Kafka Topics (Concrete)

We’ll keep this clean and realistic:

```cs
payments.events
payments.settlement
payments.dlq
```

Each event has:

- `event_id`
    
- `payment_id`
    
- `event_type`
    
- `version`
    
- `timestamp`

## Database Tables (Mental Model)

You should already visualize these:

### `payments`

- `id`
    
- `state`
    
- `amount_minor`
    
- `currency`
    
- `created_at`
    
- `updated_at`
    

### `idempotency_keys`

- `key`
    
- `request_hash`
    
- `response`
    
- `expires_at`
    

### `ledger_entries` (append-only)

- `id`
    
- `payment_id`
    
- `currency`
    
- `amount_minor`
    
- `entry_type` (debit/credit)
    
- `created_at`
    

### `consumer_offsets / dedupe`

- Used to avoid double-processing

# 4️⃣ Failure Model (Very Important)

We explicitly design for failure.

### What can fail?

- API crashes after DB write
    
- Kafka consumer crashes mid-processing
    
- Duplicate Kafka messages
    
- Network retries
    

### How we handle it:

- DB transactions
    
- Idempotent writes
    
- Retry with backoff
    
- DLQ for poison messages
    
- Reconciliation job scans DB vs ledger
    

This is what makes the project **non-trivial**.

# 5️⃣ What We Are NOT Building (Important Boundaries)

To avoid scope creep:

- ❌ No auth system
    
- ❌ No frontend UI (maybe Postman / curl)
    
- ❌ No real payment gateways
    
- ❌ No real-time FX markets
    
- ❌ No Kubernetes deployment (Docker is enough)
    

You still sound senior without these.

# Day 1 – Architecture, State Machine, Data Model

We will design **four things** today:

1. Payment lifecycle (state machine)
    
2. Service boundaries (API vs workers)
    
3. Database schema
    
4. Kafka event contract
    

Once these are frozen, implementation becomes mechanical.

---

## 1️⃣ Payment State Machine (Authoritative)

This is the heart of the system.

### States

`CREATED → AUTHORIZED → CAPTURED → SETTLED`

Optional terminal states:

`FAILED CANCELLED`

But we’ll keep those optional for now.

---

### Valid Transitions

|From|To|Trigger|
|---|---|---|
|CREATED|AUTHORIZED|API call|
|AUTHORIZED|CAPTURED|API call|
|CAPTURED|SETTLED|Kafka worker|
|ANY|FAILED|Worker / validation|

---

### Invariants (important for interviews)

- Cannot skip states
    
- Cannot go backwards
    
- Each transition emits a Kafka event
    
- State change + event emission must be atomic
    

We will encode this explicitly.

---

## 2️⃣ Service Architecture (Single Service + Workers)

We’ll run **3 processes** from one repo:

`/api-server /settlement-worker /ledger-worker`

All share:

`/core (domain logic) /db /kafka`

---

### Responsibilities

#### API Server

- Create payment
    
- Authorize payment
    
- Capture payment
    
- Idempotency enforcement
    
- Persist state
    
- Emit Kafka events
    

#### Settlement Worker

- Consume payment events
    
- Perform settlement logic
    
- Transition CAPTURED → SETTLED
    
- Emit settlement events
    

#### Ledger Worker

- Consume settlement events
    
- Write immutable ledger entries
    
- Deduplicate events
    

---

## 3️⃣ Database Schema (Version 1)

### payments

```sql
id (uuid, pk)
state (enum)
amount_minor (bigint)
currency (char(3))
created_at
updated_at
```

---

### idempotency_keys

```sql
key (text, pk)
request_hash (text)
response_json (jsonb)
created_at
expires_at
```
---

### ledger_entries (append-only)

```sql
id (uuid, pk)
payment_id (uuid)
currency (char(3))
amount_minor (bigint)
entry_type (credit/debit)
created_at
```
---

### processed_events (consumer dedupe)

```sql
event_id (uuid, pk)
processed_at
```
---

## 4️⃣ Kafka Event Contract

All services must speak the same language.

### Base Event Format

```json
{
  "event_id": "uuid",
  "event_type": "PAYMENT_CREATED",
  "payment_id": "uuid",
  "amount_minor": 1000,
  "currency": "USD",
  "timestamp": "ISO-8601"
}
```
---

### Topics

```cs
payments.events
payments.settlement
payments.dlq
```
---

### Event Types

- PAYMENT_CREATED
    
- PAYMENT_AUTHORIZED
    
- PAYMENT_CAPTURED
    
- PAYMENT_SETTLED
    

---

## 5️⃣ Directory Structure (Final)

```bash
/src
  /api
  /workers
    /settlement
    /ledger
  /core
    payment.ts
    state-machine.ts
    events.ts
    money.ts
  /db
  /kafka
  /config
```
---

# Your First Task (Before Coding)

I want you to do **one thing**:

### Write down the state machine formally:

In text or markdown:

- States
    
- Transitions
    
- Which component triggers each transition
    
- Which Kafka event is emitted
    

Example:

`CREATED --(API authorize)--> AUTHORIZED -> emits PAYMENT_AUTHORIZED`

Do this for all transitions.

This forces correct mental modeling.

---

## Why we do this

In real fintech teams:

> You never code the payment flow before writing the state machine.

This is how bugs enter systems.

---

API server manages **payment intent and state**, settlement worker manages **external execution**, ledger worker manages **accounting truth**.

## Day 2

Day 2 = **freeze the data contracts**. Once these are correct, the rest of the system becomes straightforward.

We’ll finalize:

1. Database schema (SQL)
    
2. Kafka topic + event schemas
    
3. Repo / service skeleton
    
4. Local dev stack (Docker Compose)
    

I’ll guide like a senior reviewer: explain _why_, not just _what_.

---

# 1️⃣ Final Database Schema (Authoritative)

We model **three concerns**:

- Payment workflow state
    
- Idempotency safety
    
- Accounting truth (ledger)
    

---

## 🟦 payments table (workflow state)

```sql
CREATE TABLE payments (
    id UUID PRIMARY KEY,
    amount_minor BIGINT NOT NULL CHECK (amount_minor > 0),
    currency CHAR(3) NOT NULL,
    direction VARCHAR(3) NOT NULL CHECK (direction IN ('IN', 'OUT')),
    state VARCHAR(16) NOT NULL CHECK (state IN ('CREATED', 'AUTHORIZED', 'CAPTURED', 'SETTLED', 'FAILED')),
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    updated_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### Why this design

- `amount_minor > 0` → no negative money
    
- `direction` → determines ledger behavior
    
- `state` enforced by DB → prevents corruption
    
- timestamps → auditing

## 🟦 idempotency_keys table

```sql
CREATE TABLE idempotency_keys (
    key TEXT PRIMARY KEY,
    request_hash TEXT NOT NULL,
    response_json JSONB NOT NULL,
    created_at TIMESTAMPTZ NOT NULL DEFAULT now(),
    expires_at TIMESTAMPTZ NOT NULL
);
```

### Why

- prevents double-charging
    
- detects conflicting retries
    
- response caching
### 5️⃣ What is `request_hash` in idempotency_keys?

Idempotency key alone is not enough.

We also store:

> A hash of the request payload

Why?

To detect this situation:

`Client sends: Idempotency-Key: abc Amount: 100  Then retries: Idempotency-Key: abc Amount: 500`

That is a **bug or fraud**.

So we:

- hash request body
    
- store it with the key
    
- if same key + different hash → reject
    

This is industry standard (Stripe does this).

## 🟦 ledger_entries table (append-only)

```sql
CREATE TABLE ledger_entries (
    id UUID PRIMARY KEY,
    payment_id UUID NOT NULL REFERENCES payments(id),
    currency CHAR(3) NOT NULL,
    amount_minor BIGINT NOT NULL CHECK (amount_minor > 0),
    entry_type VARCHAR(6) NOT NULL CHECK (entry_type IN ('CREDIT', 'DEBIT')),
    created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```

### Why separate ledger

- immutable
    
- auditable
    
- recomputable balances
    
- matches fintech practice

## 🟦 processed_events table (Kafka dedupe)

```sql
CREATE TABLE processed_events (
    event_id UUID PRIMARY KEY,
    processed_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
```
### Why

- idempotent consumers
    
- safe retries
    
- exactly-once–style semantics
---
# 2️⃣ Kafka Topics & Event Schema

We now define **strict contracts**.

---

## Topics

```sql
payments.events
payments.settlement
payments.dlq
```

---

## Base Event Schema (JSON)

```json
{
  "event_id": "uuid",
  "event_type": "PAYMENT_CAPTURED",
  "payment_id": "uuid",
  "amount_minor": 1000,
  "currency": "USD",
  "direction": "IN",
  "timestamp": "2026-01-10T12:00:00Z"
}
```
---

## Event Types

|Event|Producer|Consumer|
|---|---|---|
|PAYMENT_CREATED|API|none|
|PAYMENT_AUTHORIZED|API|none|
|PAYMENT_CAPTURED|API|settlement-worker|
|PAYMENT_SETTLED|settlement-worker|ledger-worker|

---
## DLQ Event

Same schema +:

```json
"error": "reason"
```

[[Idempotency]]