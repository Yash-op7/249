# 🧠 What is a “pattern” (in this context)?

A **pattern** is:

> A reusable solution to a _recurring problem_ in a specific context.

It’s not code, not a library, not a framework.

It’s:

- a **way of structuring logic or systems**
- with known **tradeoffs**
- that solves a **class of problems**

# Crucial Patterns:

## 📊 Where does DataLoader fit?

👉 **DataLoader pattern = LLD (Low-Level Design) pattern**

- Scope: inside a service / request
- Problem: N+1 queries
- Solution: batch + cache per request

---

## 📊 Where does Transactional Outbox fit?

👉 **Transactional Outbox = HLD (High-Level Design) pattern**

- Scope: across services / databases / messaging
- Problem: DB + message consistency
- Solution: write event to DB, then publish asynchronously

# 🧩 Clean classification

### 🧱 LLD Patterns (code-level, inside services)

|Pattern|Problem it solves|
|---|---|
|DataLoader|N+1 queries|
|Repository Pattern|DB abstraction|
|Unit of Work|transaction coordination|
|Circuit Breaker (client-side)|failing dependencies|
|Retry with Backoff|transient failures|
|Bulkhead|resource isolation|
|Cache-Aside|caching reads|
|Decorator|adding behavior dynamically|

---

### 🏗️ HLD Patterns (system-level, architecture)

|Pattern|Problem|
|---|---|
|Transactional Outbox|DB + event consistency|
|Saga Pattern|distributed transactions|
|CQRS|read/write scaling|
|Event Sourcing|audit + replay|
|API Gateway|central entry point|
|Backend for Frontend (BFF)|client-specific APIs|
|Strangler Fig|legacy migration|
|Service Mesh|service-to-service concerns|
|Rate Limiting|abuse control|
|Idempotency|safe retries|

---

# 🔥 The patterns you SHOULD learn (high ROI)

Since you liked **Outbox**, you’ll probably benefit most from these:

---

## 🧨 1. Saga Pattern (MUST KNOW)

> Handles multi-service transactions without 2PC

Two types:

- **Orchestrated** (you already kind of have this 👀)
- **Choreographed**

Used in:

- payments
- order systems

---

## 🔁 2. Idempotency Pattern

> Ensures retries don’t cause duplicate effects

Example:

- payment API called twice → charge once

---

## 📦 3. CQRS (Command Query Responsibility Segregation)

> Separate read and write models

Useful when:

- reads >> writes
- complex queries

---

## 🧾 4. Event-Driven Architecture

> Services communicate via events

Works beautifully with:

- Outbox
- Kafka / queues

---

## 🧠 5. Cache Patterns (VERY practical)

- Cache Aside (most common)
- Write Through
- Write Back

---

## 🧯 6. Circuit Breaker

> Stop calling a failing service

Prevents:

- cascading failures

---

## 🔄 7. Retry + Backoff + Jitter

> Smart retries without overload

---

## 🧱 8. Bulkhead Pattern

> Isolate failures (like ship compartments)

Example:

- worker pool separation

---

## 🧬 9. Strangler Fig Pattern

> Gradually replace legacy systems

---

## 🧮 10. Sharding Pattern

> Split DB horizontally

---

# 🧠 How to think about patterns (important)

Don’t memorize names.

Instead ask:

👉 “What problem category is this?”

|Problem|Pattern|
|---|---|
|Too many DB calls|DataLoader|
|Inconsistent events|Outbox|
|Multi-service transaction|Saga|
|Duplicate requests|Idempotency|
|Slow reads|Cache|
|Failing service|Circuit breaker|

---

# ⚠️ Common mistake (avoid this)

People think:

> “Patterns = fancy architecture”

Wrong.

👉 Patterns are just:

> battle-tested solutions to pain you _will_ hit