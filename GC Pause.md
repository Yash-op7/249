# 1. GC pause

**GC = Garbage Collection** (for Java, Go, Node/V8, etc.)

When memory fills up, the runtime:

1. Stops the program (or slows it a lot)
    
2. Finds unused objects
    
3. Frees memory
    

This stop is a **GC pause**.

### Why it matters

During a pause:

- No requests are processed
    
- Latency spikes
    
- Timeouts happen
    
- Upstream services think you are “slow or dead”
    

### Example

Node API normally responds in 20ms.

Suddenly:

`GC pause = 600ms`

Now:

- Requests take 620ms
    
- Load balancer retries
    
- Threads pile up
    
- Cascading failure begins