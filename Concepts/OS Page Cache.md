# OS Page Cache

**First referenced in:** [[Chapter 3: Storage and Retrieval]]

---

## Definition

The OS page cache is a region of RAM that the Linux (and other OS) kernel uses as a transparent buffer between application file I/O and physical disk. When an application reads or writes a file, it is almost always interacting with the page cache, not the disk directly.

---

## Why It Exists

Disk I/O is orders of magnitude slower than RAM access (HDD: ~10ms seek; NVMe SSD: ~100µs; RAM: ~100ns). The page cache allows the OS to:
- Serve repeated reads from RAM instead of disk
- Absorb write bursts in RAM and flush to disk asynchronously in the background
- Decouple application write speed from disk write speed

Without the page cache, every `write()` call would stall until the disk confirmed the write — making all file I/O as slow as the disk.

---

## How It Works

### Reads
1. Application calls `read(fd, buf, n)`
2. OS checks: is this file region already in page cache?
   - **Cache hit:** copy data from RAM to application buffer — no disk I/O
   - **Cache miss (page fault):** OS reads the page from disk into page cache, then serves it to the app
3. Subsequent reads of the same region are served from cache

### Writes (buffered I/O — the default)
1. Application calls `write(fd, buf, n)`
2. OS writes data into the page cache and marks those pages as **dirty** (modified but not yet on disk)
3. `write()` returns to the application immediately — the app does not wait for disk
4. OS kernel flushes dirty pages to disk asynchronously via background threads (`pdflush` / `kworker`)
5. Flush is triggered by: elapsed time, dirty page ratio threshold, or explicit `fsync()` call

### `fsync()` — forcing a flush
- Calling `fsync(fd)` forces all dirty pages for that file to be flushed to disk before returning
- Databases call `fsync` at commit time to guarantee durability (the "D" in ACID)
- `fsync` is expensive — it converts the async write into a synchronous one

### Page Eviction
- Page cache fills available RAM; when more space is needed, the OS evicts **cold pages** (least recently used)
- If a consumer reads data that was evicted from cache, it triggers a disk read — causing a latency spike
- This is why a Kafka consumer that is far behind the producer suddenly slows: recent messages are in cache, old segments are on disk

---

## Tradeoffs

| Advantage | Cost |
|---|---|
| Writes return instantly (no disk wait) | Data in page cache is lost on power failure if not flushed |
| Repeated reads served from RAM | Eviction of cold pages causes unpredictable read latency spikes |
| OS manages caching transparently — no app code needed | Application has no direct control over what stays in cache |
| `sendfile` / zero-copy can serve data from cache directly to network | Large working sets exceed RAM → cache thrashing → all reads hit disk |

---

## The `fsync` Durability Gap

Because writes are buffered in RAM, there is a window between `write()` returning and data hitting disk where a crash loses data. Databases close this gap using a [[Write-Ahead Log]] — they call `fsync` on the WAL entry before acknowledging a transaction, ensuring at least the intent is durable even if the actual data page is not yet flushed.

Kafka brokers, by default, rely on replication for durability rather than calling `fsync` per message — they accept that a single broker crash could lose recently written messages that haven't flushed, but replication to other brokers means the data isn't lost overall.

---

## Production Systems

| System | How It Uses Page Cache |
|---|---|
| **Kafka** | Relies almost entirely on page cache for both writes (producers) and reads (consumers). Kafka intentionally avoids using JVM heap for message buffering and delegates caching to the OS. This is why Kafka recommends giving brokers large amounts of RAM — more RAM = larger page cache = more partitions served from memory. |
| **PostgreSQL** | Uses page cache for data file reads. Also maintains its own internal buffer pool (`shared_buffers`). Data flows: disk → page cache → shared_buffers → query. Writes go: shared_buffers → page cache → disk (async), with WAL `fsync` for durability. |
| **RocksDB / LevelDB** | SSTables are read via page cache. Frequently accessed SSTable blocks stay in cache, reducing compaction read cost. |
| **Elasticsearch** | Relies heavily on page cache for Lucene segment files. A warm Elasticsearch node serves almost all queries from RAM. A cold restart flushes the cache and causes a warmup period of elevated latency. |

---

## Zero-Copy I/O (`sendfile`)

A related OS optimization: normally serving a file to a network socket requires:
1. Disk → page cache (kernel space)
2. Page cache → application buffer (user space)
3. Application buffer → socket buffer (kernel space)
4. Socket buffer → NIC

`sendfile` short-circuits steps 2 and 3 — data goes directly from page cache to the socket buffer without copying through user space. Kafka uses this for consumer reads, which is why Kafka can serve many consumers at high throughput with low CPU overhead.

---

## Interview Angle

**Q: Why does Kafka have much higher write throughput than a typical database even on the same hardware?**
**A:** Three reasons working together: (1) Sequential disk appends — no random I/O, no index updates per message. (2) OS page cache buffering — producer `write()` calls return as soon as data lands in RAM; the OS flushes to disk asynchronously. The producer never waits for disk. (3) Zero-copy reads via `sendfile` — consumer reads go from page cache to network without copying through user space. A traditional database doing random writes with index maintenance pays the cost at every write; Kafka defers and batches the disk cost.

**Q: A Kafka consumer that was running fine for weeks suddenly shows high latency. What's a likely cause?**
**A:** The consumer has fallen behind and is now reading log segments that have been evicted from the OS page cache. Recent messages are served from RAM (fast); old segments require a disk read (slow). This manifests as a sudden latency increase rather than gradual degradation. Diagnose by checking consumer lag (in time, not just message count) and broker page cache hit rate. Fix: catch the consumer up, or increase broker RAM to hold a larger working set in cache.

---

## Linked Concepts
- [[Write-Ahead Log]] — uses `fsync` to close the durability gap that page cache buffering creates
- [[Compaction]] — in LSM-Tree engines, compaction reads/writes SSTables through the page cache; a compaction-heavy system can evict hot data from cache
- [[LSM-Tree]] — storage engines that rely on page cache for SSTable reads during compaction and query serving
