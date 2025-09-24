## **1. What is Kafka?**

**Apache Kafka** is a _distributed event streaming platform_.

- At its core: a scalable, high-throughput, low-latency platform for handling real-time data feeds.
- **Primary use:** Messaging (like a queue), event streaming, log aggregation, decoupling services.
## **2. Key Concepts/Terminologies**

- **Broker:** A Kafka server; a cluster consists of multiple brokers.
    
- **Topic:** A named stream of data, like a "channel" or "feed." Producers write messages to topics; consumers read from topics.
    
- **Partition:** Each topic is split into _partitions_ for scalability and parallelism. Each partition is a sequence of messages, stored and delivered in order.
    
- **Message or Record:** is the basic unit of data in Kafka. It's what you send (produce) to and receive (consume) from Kafka topics.
	- **What does a record contain?**
	    - **Key (Record Key or Partition Key)**
		    - The **key** is an optional field in a Kafka record.
		    - If set, it influences **partition assignment**.
		    - **What does it do?**
		    - **Partitioning:** Kafka will hash the key, modulo # of partitions, to decide **which partition** the record goes into (for that topic).
	        - `partition = hash(key) % num_partitions`
		    - This means **same key always goes to the same partition** (unless you add/remove partitions).
	    - **Value** (the main data; any binary/text info)
	    - **Headers** (optional, metadata)
	    - **Timestamp** (when produced or broker-received)
	    - **Other metadata** (offset, topic, partition—Kafka maintains these)
    
- **Offset:** Each message in a partition has a unique, monotonically increasing _offset_. Used to keep track of how much a consumer has read. 
	- It's basically a unique number given to every message in a partition. It increases by one for each new message.
	- Each consumer keeps track of the **offset** they have read up to, so they can continue from the right spot next time.\
	- Offsets are **unique** within a partition and always go up by one.
	Example: 
	- here: **msg1** is assigned **offset 0**, and so on
	- If a consumer finishes reading msg2 (offset 1), it remembers the offset **2** to know where to start next time.
	- By default, **Kafka lets the consumer store this information in a special Kafka topic called `__consumer_offsets`**, so the consumer doesn't need to store it locally to remember the  last read message's offset.
```
Partition 0: 
[ msg1 ][ msg2 ][ msg3 ][ msg4 ]
     0      1      2      3
```
	
- **Producer:** App/process that _writes_ messages to Kafka topics.
    
- **Consumer:** App/process that _reads_ messages from Kafka topics.
    
- **Consumer Group:** A set of consumers sharing a group ID. Kafka assigns partitions to consumers in the group; each partition is consumed by only one member within the group (for parallelism and fault tolerance).
    
- **Replication:** Each partition has multiple copies (default 3) on different brokers for redundancy.
    
- **ZooKeeper:** (Pre-Kafka 2.8) Kafka used ZooKeeper for broker metadata/management. Newer Kafka versions (KRaft mode) can eliminate ZooKeeper

## **3. Kafka’s Core Strengths**

- **Durability:** Data is persisted to disk (can configure _how_ and _when_).
- **Scalability:** Easily add partitions/brokers to scale throughput/parallelism.
- **Fault-tolerance:** Replication of partitions across brokers means the system can survive broker failures.
- **High throughput:** Designed for millions of messages/sec.
- **Decoupling:** Enables microservices to communicate asynchronously.

---

## **4. How Kafka Works (at a high level)**

1. **Producer sends a message to a Topic.**
2. The Topic is split into Partitions.
    - Which partition gets the message? Depends on configured key, round-robin, or explicit.
3. Each Partition is stored (persisted) on disk, and is replicated (copies stored on other brokers).
4. Consumers read data from partitions, tracking their current _offset_.
    - Different consumer groups read at their own pace—doesn't affect others.

---

## **5. Guarantees & Delivery Semantics**

- **At least once:** Default. Consumers may see the same message more than once if they fail after consuming but before committing offset.
    
- **At most once:** If offset is committed before processing, failures may cause data loss.
    
- **Exactly once:** Available with special configuration (transactions).
    
- **Order:** Guaranteed _within_ a partition. No order guarantee _across_ partitions (If you split your topic across multiple partitions, consumers working on each partition might process messages at different speeds).
    

---

## **6. Key Configuration Options You Should Know**

**Producers (Clients that send data to topics)**: 

- `acks` (Acknowledgement level):
	- **What:** Controls when the producer considers a message “successfully sent.”
	- **Why care?**: Tradeoff between speed vs. data guarantees.
    - `0`: No ack needed. Fire and forget. Fastest, but data may be lost if broker is unavailable.
	- `1`: Waits for leader broker to write. More reliable than `0`, but if leader crashes before replicating, data may be lost.
	- `all`: Waits for **all in-sync replicas** to write. Slowest, but highest durability—safe even if a broker crashes right after writing.
- `retries`: 
	- **What:** How many times to retry sending a record if it initially fails (e.g., because of a transient network error).
	- **Why care?**: Raises chances of successful delivery, but can increase duplication if not used carefully.
- `linger.ms` and `batch.size`:
	- **What:** Control producer batching.
    - `linger.ms`: Max time (ms) to wait before sending a batch—lets producer wait a short period to build a bigger batch.
    - `batch.size`: Max batch size in bytes before sending, regardless of time.
	- **Why care?**: Bigger batches = better throughput & compression, but adds latency.
- `compression.type`: 
	- **What:** Compress batches before sending (`none`, `gzip`, `snappy`, `lz4`, `zstd`).
	- **Why care?**: Saves network + storage, can improve throughput, small cpu overhead.

**Brokers:**

- `num.partitions`: 
	- **What:** The default number of partitions for new topics.
	- **Why care?**: More partitions = more parallelism, higher throughput. (But above a threshold, can strain metadata and coordination.)
- `default.replication.factor`: Fault-tolerance.
	- **What:** How many copies of a partition are stored (usually 2–3).
	- **Why care?**: Replication gives reliability if brokers fail.

**Topics (Named data streams):**

- Per-topic retention policies:
    - Time (how long messages are kept)
	    - `retention.ms` — Keep data for X ms (e.g., 7 days)
    - Size (keep at most X GB)
	    - `retention.bytes` — Keep up to X GB per partition
	- **Why care?**: Controls data storage costs & compliance; after this, old data is deleted.

**Consumers (Clients that read data):**

- `auto.offset.reset`: What to do at the start (`earliest`, `latest`).
	- - **What:** If there’s **no stored offset** (first run, or if the offset has expired), where should the consumer start?
    - `earliest`: Start at the oldest available message
    - `latest`: Start at the most recent/new messages
- **Why care?**: Controls whether you want to reprocess all history or only new data.
- `enable.auto.commit`: Whether offsets are committed automatically or manually (explicit control is more reliable).
- `max.poll.records`: Batch consumption.
	- - **What:** How many messages to fetch in a single poll.
	- **Why care?**: Helps manage consumer throughput and memory usage.

**General:**

- **Min In-Sync Replicas**: Number of brokers that must confirm a write before it’s considered committed (key for durability under failures).
	- **Why care?**: Prevents data loss—if not enough replicas are alive, writes fail instead of silently losing durability.
- **Log Compaction**: Remove old duplicate records with same key (for change feeds, caching, etc), A way to keep only the latest messages for each unique key in a topic.
	- **Why care?**: Saves space for topics where only the latest value matters (think: caches, state changelogs).
- **Rack-Awareness**: Distribute replicas across racks/data centers for hardware fault tolerance.
	- **What:** Ensures replicas of the same partition are distributed **across racks (physical or logical)**.
	- **Why care?**: Prevents all copies from going down if a rack/data center fails = higher availability.

---

## **7. Fault Tolerance and Reliability**

- **Replication**: If the broker holding the partition leader crashes, another replica takes over.
- **Producer/Consumer Retries & Idempotence**: Configured to avoid loss/duplication.
- **Consumer Group Rebalancing**: If a consumer dies, partitions are reassigned.
- **Retention**: Even if a consumer goes down, data is retained in Kafka for a set period (can be replayed).

---

## **8. Scaling Kafka**

- **Scale Horizontally:** Add more brokers to increase storage and throughput.
- **Scale Vertically:** Faster disks (SSD/NVMe), more RAM for page cache.
- **Increase Partitions:** More partitions = more throughput potential, but too many can stress Zookeeper (if used) and slow down rebalancing.
	- **Why do this?** More partitions mean more parallelism: messages can be read and written by many consumers and producers at the same time.

---

## **9. Common Architectural Patterns**

- **Event Sourcing**: All changes to application state are stored as Kafka events.
- **Change Data Capture (CDC)**: Use Debezium/connectors to capture DB changes via Kafka.
- **Log Aggregation**: Collect logs across microservices into a centralized Kafka topic.
- **Ingestion Pipelines**: Ingest IoT/web/app data in real time, then fan out to datastores/analytics.

---

## **10. Common Mistakes/Misconceptions**

- **"More partitions is always better"** — No, check per-partition throughput and Zookeeper limits.
- **"Ordering is strict"** — Only within a partition, not across entire topic!
- **"Kafka replaces a database"** — No, Kafka is a log/event/message system, not for lookup/query.

---

## **11. Integration with the Cloud**

- **Kafka as a Service**: Confluent Cloud, AWS MSK, Azure Event Hubs, etc.
- **Managed Zookeeper**, scaling, build-in metrics, seamless integration with Kubernetes.

---

## **12. Monitoring, Observability & Operations**

- **Metrics**:
    - Broker health, under-replicated partitions, consumer lag, request/response rates, disk usage, etc.
- **Alerting**:
    - Under-replicated partitions, disk full, Zookeeper disconnects, high lag, etc.
- **Monitoring Tooling**:
    - Prometheus+Grafana, Confluent Control Center, Burrow.

---

## **13. Security**

- **Authentication**: SASL/PLAIN, SCRAM, Kerberos, OAuth.
- **Authorization**: ACLs for topics, consumers, producers.
- **Encryption**: TLS for on-the-wire traffic.

---

## **14. Partitioning, Keys, and Topic Design**

- **Partitioning**: Keyed messages always go to the same partition (helps with ordering).
- **Keys**: Used for logical partitioning (e.g., by user/device/region).
- **Partition Count**: Chosen based on:
    - Consumer parallelism needs
    - Aggregate throughput
    - Broker count

---

## **15. When to use Kafka?**

- Real-time data pipelines (ingestion, ETL)
- Decoupling microservices with asynchronicity
- Event sourcing, CDC
- Log aggregation/analytics
- IoT ingestion from millions of devices

---

## **16. Good Interview Nuggets / Quotes**

- “Kafka acts as the central nervous system for distributed data.”
- “It decouples producers and consumers in time, space, and scaling dimension.”
- "Kafka guarantees order within partitions, not across the whole topic."
- “Each message is persisted and replicated for durability; data can be replayed by consumers at any time, provided it is still retained.”
- “Choosing the right partition key is crucial for even load and predictable ordering.”
- “We leverage consumer groups for scaling out and providing failover for downstream processing.”

---

## **Sample Mini-Design**

> "For scalable ingest, producers send to a Kafka topic with N partitions (based on throughput & consumer parallelism). Each partition is replicated three times. Consumer groups process in parallel. Brokers are automatically monitored; failed brokers or consumers are detected and the workload is redistributed to maintain uptime and data integrity."

---

## **Summary Table: Key Kafka Knowledge for Backend Interviews**

|Concept|Good to Know|
|---|---|
|Broker|Kafka server, cluster of brokers for redundancy|
|Topic|Named stream of data, split into partitions|
|Partition|Unit of parallelism/ordering, assigned to brokers|
|Producer/Consumer|Writes/reads data; use keys for partition consistency|
|Consumer Group|Parallelizing consumption, one consumer per partition in group|
|Offset|Marker for position in partition, managed per consumer group|
|Replication|Default|
