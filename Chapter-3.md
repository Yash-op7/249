# Chapter 3: Storage and Retrieval

## Core Idea

Every storage engine makes one unavoidable tradeoff: structures that speed up reads slow down writes, and vice versa. Two engine families dominate: **log-structured** (append-only writes, merge/compact later — e.g. RocksDB, Cassandra) and **page-oriented** (B-trees, in-place update — e.g. Postgres, MySQL). A second axis — OLTP vs OLAP — determines whether row-oriented or column-oriented layout wins. Choosing the right engine requires understanding what it does on disk, not just its API.

---

## Sections

### Introduction — Why Storage Engine Internals Matter

#### Core Mechanic
You won't build a storage engine, but you will choose and tune one. The wrong engine for your workload is a silent performance killer that only reveals itself at scale.

#### Two Primary Axes of Choice

| Axis | Option A | Option B |
|---|---|---|
| Write strategy | Log-structured (append, compact later) | Page-oriented (in-place update) |
| Workload type | OLTP — low-latency point ops, row-oriented | OLAP — high-throughput aggregations, column-oriented |

#### Production Reality
- Log-structured: RocksDB, Cassandra, LevelDB, HBase
- Page-oriented: PostgreSQL, MySQL, SQLite
- Column-oriented (OLAP): Redshift, ClickHouse, BigQuery, Parquet files

> <span style="color:#fbbf24;">**Mental Model:**</span> Choosing a storage engine is choosing *when* to pay the I/O cost — at write time (B-tree, in-place) or deferred to compaction time (LSM-tree, append-then-merge).

---

### Data Structures That Power Your Database

#### Core Mechanic
An <mark>append-only log</mark> is the fastest possible write primitive — sequential disk I/O, no seeking. The cost is O(n) reads. An <mark>index</mark> solves the read problem by maintaining a derived lookup structure, but every index must be updated on every write. The fundamental tradeoff: indexes accelerate reads at the direct cost of write throughput.

#### How It Works

**Append-only log (the primitive):**
- Every write appends a new `key,value` record to the end of a file
- Updates don't overwrite — they append a newer version; old versions persist until compaction
- Reads must scan the full file and return the last occurrence of a key
- Read cost: O(n) without index; write cost: O(1) amortized (sequential append)

**Index (the accelerator):**
- A separate data structure derived from the primary data; stored alongside it, not inside it
- Maps keys (or expressions over keys) to byte offsets or row locations in the data file
- Adding/dropping an index does not change the data — only affects query performance
- Every write to primary data must also update all relevant indexes

#### Tradeoffs

| Advantage | Cost |
|---|---|
| Append: O(1) amortized write, no seek penalty | Append: O(n) read without index |
| Append: no partial-write corruption (immutable past is never touched) | Append: unbounded disk growth without compaction |
| Index: reduces read from O(n) to O(log n) or O(1) depending on type | Index: every write fans out to N index updates ([[Write Amplification]]) |
| Index: enables multiple access patterns on one dataset | Index: unused indexes waste write budget on every insert/update |

#### Failure Modes
- <span style="color:#f87171;">⚠</span> **Over-indexing:** write throughput collapses when each insert must update many indexes; commonly introduced by adding indexes speculatively rather than based on measured query patterns
- <span style="color:#f87171;">⚠</span> **Under-indexing:** query latency grows linearly with data volume — invisible in development, catastrophic at 100M+ rows in production
- <span style="color:#f87171;">⚠</span> **Index-data divergence:** a crash between writing to the data file and updating the index leaves them inconsistent; requires either a [[Write-Ahead Log]] or a full index rebuild on recovery

#### Production Reality
- Kafka's partition storage is an append-only log segment; consumers track their position via offsets instead of scanning — this is why Kafka achieves near-linear write throughput regardless of how many consumers are reading
- The append-only ledger in the payments project follows the same principle: every transaction is a new row appended, old rows are never modified, and balance is derived by reading forward from the log
- The transactional outbox pattern is an append-only write to a side table — the outbox processor reads from one end, marks entries as processed, and downstream consumers advance their own position
- Postgres/MySQL: adding an index on a high-write table in production requires `CREATE INDEX CONCURRENTLY` to avoid locking the table; this makes the index maintenance cost visible as a real operational concern
- DynamoDB: forces explicit index declaration upfront since it cannot perform ad-hoc secondary scans at scale

#### Interview Angles

- <span style="color:#60a5fa;">**Q:**</span> Why does Kafka achieve such high write throughput compared to a traditional database?
  <span style="color:#4ade80;">**A:**</span> Kafka writes are sequential appends to a partition log on disk. Sequential writes are faster than random writes on both HDDs (no seek time) and SSDs (no partial-page rewrites). Additionally, Kafka relies on the [[OS Page Cache]] — writes go to RAM first and the OS flushes to disk asynchronously, so the producer doesn't wait for a physical disk write. Kafka also avoids per-message index updates entirely; consumers track position themselves via offsets. These three factors together (sequential I/O, page cache buffering, no index overhead) explain the throughput gap.

- <span style="color:#60a5fa;">**Q:**</span> You have a Postgres table with 10 indexes and insert throughput is degrading under load. What's happening and how do you fix it?
  <span style="color:#4ade80;">**A:**</span> Each insert triggers 10 additional index updates — 1 logical write becomes 11 physical writes ([[Write Amplification]]). Under high insert volume this becomes the bottleneck. Fix: use `pg_stat_user_indexes` to identify indexes with zero or near-zero scans, then drop the unused ones. For the remaining indexes, consider partial indexes (indexing only a subset of rows) or expression indexes to reduce maintenance surface.

- <span style="color:#60a5fa;">**Q:**</span> Your payments ledger needs to be auditable and tamper-evident. How would you design the storage layer?
  <span style="color:#4ade80;">**A:**</span> Use an append-only log — never UPDATE or DELETE ledger rows. Every transaction event is a new immutable row with a timestamp, amount, and reference to the previous entry. This gives you a full audit trail by design since no history is ever erased. If tamper-evidence is required beyond access controls, you can chain entries using a hash of the previous row's content (similar to a hash chain / blockchain structure). The immutability also simplifies replication since followers only need to append, never reconcile conflicting updates.

> <span style="color:#fbbf24;">**Mental Model:**</span> Every index is a write-time consistency obligation. Add indexes to match proven access patterns, not anticipated ones.

#### Linked Concepts
- [[Hash Index]] — simplest index type; O(1) lookup via in-memory hash map; cannot do range queries; covered in next section
- [[B-Tree]] — ordered, page-oriented index; supports range queries; dominates OLTP systems; covered later this chapter
- [[LSM-Tree]] — log-structured merge tree; defers index maintenance cost to compaction; dominates write-heavy systems; covered later this chapter
- [[Write-Ahead Log]] — append-only log used for crash recovery, not primary storage; same append primitive, different purpose
- [[Compaction]] — process of merging and garbage-collecting log segments; the mechanism that makes append-only storage space-efficient
- [[Write Amplification]] — one logical write causing multiple physical writes; the core cost of maintaining indexes

---

## Cross-Chapter Connections

| Chapter | Connection |
|---|---|
| Ch. 2 — Data Models | Document model's locality advantage mirrors log locality — whole record stored together for one sequential read |
| Ch. 5 — Replication | Replication log is an append-only log; followers replay the leader's [[Write-Ahead Log]] — same append primitive |
| Ch. 7 — Transactions | [[Write-Ahead Log]] is the durability mechanism behind ACID; index-data consistency requires atomic writes |
| Ch. 11 — Stream Processing | Kafka's log is the production implementation of append-only storage at distributed scale |

---

## Interview Bank

<span style="color:#60a5fa;">**Q:**</span> Why does append-only beat in-place update for write throughput on both HDDs and SSDs?
<span style="color:#4ade80;">**A:**</span> HDDs require a physical head seek before every random write — sequential writes avoid this entirely. SSDs avoid random writes for a different reason: flash memory writes in pages (typically 4KB), so a small in-place update forces a full page read-modify-write cycle, causing [[Write Amplification]]. Sequential appends allow both HDDs and SSDs to write at their maximum sustained throughput. This is why any system optimized for writes (Kafka, RocksDB, Cassandra) uses append-only log structures as their core write primitive.

---

<span style="color:#60a5fa;">**Q:**</span> What is write amplification and why does it matter for index-heavy tables?
<span style="color:#4ade80;">**A:**</span> [[Write Amplification]] is the ratio of physical bytes written to disk per logical byte written by the application. A table with N indexes has a write amplification of at least N+1 (one write to the data file, one per index). At high insert/update rates, this multiplies disk I/O, consumes more SSD write cycles (shortening drive lifespan), and can saturate disk bandwidth before CPU or memory becomes the bottleneck. It also affects WAL size since index updates are logged too. Systems like RocksDB are specifically designed to reduce write amplification through batched compaction.

---

<span style="color:#60a5fa;">**Q:**</span> How would you decide which indexes to keep on a high-write Postgres table?
<span style="color:#4ade80;">**A:**</span> Start from query patterns, not intuition. Use `pg_stat_user_indexes` to see actual index scan counts since the last stats reset — any index with zero scans is a pure write overhead with no read benefit. For indexes that are used, measure whether the query speedup justifies the insert cost by comparing write throughput with and without the index under realistic load. Keep indexes that are on the critical read path. Drop or avoid indexes on columns that are only queried occasionally — those queries can tolerate a slower plan if the alternative is degraded write throughput across all inserts.

---

<span style="color:#60a5fa;">**Q:**</span> What is the difference between a Write-Ahead Log and an application-level append-only log?
<span style="color:#4ade80;">**A:**</span> A [[Write-Ahead Log]] is a database-internal durability mechanism: before the engine applies any change to its data structures, it writes a record of the intended change to the WAL. On crash recovery, the WAL is replayed to restore consistency. It is typically binary and managed entirely by the database. An application-level append-only log (like a ledger table or Kafka topic) is a deliberate data model choice where the application writes immutable records as its primary storage. They share the same underlying primitive (sequential appends) but serve different layers: WAL is infrastructure-level crash safety; application log is a business-logic design pattern.

---

<span style="color:#60a5fa;">**Q:**</span> Your Kafka consumer group is falling behind the producer. Could the storage engine be the bottleneck?
<span style="color:#4ade80;">**A:**</span> Yes, on the broker side. Kafka brokers serve reads from the [[OS Page Cache]] — if recent messages fit in page cache, reads are served from RAM at memory speed. But if the consumer is far behind and the relevant log segments have been evicted from page cache (because newer segments pushed them out), reads must go to disk, causing a sudden latency spike. The fix is either to increase broker RAM (more page cache), reduce log retention to keep the working set smaller, or add more partitions and consumer instances to reduce per-consumer lag. This is why Kafka operators monitor consumer lag in time, not just message count — a consumer that's hours behind is likely reading cold data from disk.
