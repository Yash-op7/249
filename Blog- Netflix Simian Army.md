## Netflix's approach to improving availability and reliability
- First design a fault tolerant system
- Then let a tool called Chaos Monkey run during business hours which randomly induces faults in components of the system and then study how other services respond and cope with it, and develop automatic recovery mechanisms
### Different Monkeys:
1. **Latency Monkey** induces artificial delays in our RESTful client-server communication layer to simulate service degradation and measures if upstream services respond appropriately. In addition, by making very large delays, we can simulate a node or even an entire service downtime (and test our ability to survive it) without physically bringing these instances down. This can be particularly useful when testing the fault-tolerance of a new service by simulating the failure of its dependencies, without making these dependencies unavailable to the rest of the system.
	- **What it does:**  
It _artificially slows down_ network calls between services to simulate partial outages / slowness.
**Why:**  
In real systems, services often don’t fully crash—they become **slow** (DB overloaded, GC pause, network congestion). Slowness causes cascading failures.

**Simple example:**
Architecture:
`User → API Service → Payment Service → DB`
Latency Monkey injects:
`API → Payment  = +2 seconds delay`
Now observe:

|Component|Good behavior|
|---|---|
|API Service|Has timeout (e.g., 500ms)|
|API Service|Uses circuit breaker|
|API Service|Returns graceful error or fallback|
|System|Does NOT block threads forever|
|System|Does NOT overload itself|

If API waits forever → threads pile up → entire service dies → **failure**.

Latency Monkey ensures:

> “We fail fast, degrade gracefully, and recover automatically.”

2. **Conformity Monkey** finds instances that don’t adhere to best-practices and shuts them down. For example, we know that if we find instances that don’t belong to an auto-scaling group, that’s trouble waiting to happen. We shut them down to give the service owner the opportunity to re-launch them properly. (see [[Auto Scaling Groups]])
3. **Doctor Monkey** taps into health checks that run on each instance as well as monitors other external signs of health (e.g. CPU load) to detect unhealthy instances. Once unhealthy instances are detected, they are removed from service and after giving the service owners time to root-cause the problem, are eventually terminated.
**high CPU ≠ always unhealthy**.

Doctor Monkey does **not use CPU alone**. It uses **multiple signals**.

### Why CPU _can_ indicate unhealthy

High CPU may mean:

- Infinite loop bug
    
- Thread deadlock spin
    
- Memory leak → GC thrashing
    
- Stuck I/O retries
    
- DDoS / runaway traffic
    
- Misconfigured deployment
    

These often correlate with:

- Increased latency
    
- Timeouts
    
- Error rates
    

### Typical health criteria (real systems)

Doctor Monkey combines:

**Application-level**

- Health endpoint (`/health`)
    
- Error rate
    
- Request success %
    
- Response latency
    

**System-level**

- CPU (sustained high)
    
- Memory usage
    
- Disk I/O wait
    
- Network errors
    

**Platform-level**

- Instance unreachable
    
- Failed heartbeats
    
- Load balancer removing instance
Rule Example:
```yaml
If:
- error rate > 5% for 2 mins
- AND latency > 2s
- AND CPU > 85%

→ mark unhealthy
→ remove from load balancer
→ terminate
```
Key idea:
> Replace suspicious instances early instead of letting them poison the system.
> Instances are cheap. Reliability is expensive.


## 4) Janitor Monkey – with context + examples
**Janitor Monkey** ensures that our cloud environment is running free of clutter and waste. It searches for unused resources and disposes of them.

**Goal:** Prevent cloud resource garbage accumulation.

Cloud leaks are common because engineers forget to clean up.

### Examples of “cloud trash”

| Resource       | Example problem                |
| -------------- | ------------------------------ |
| EC2 instances  | Old test servers still running |
| Volumes (EBS)  | Detached disks costing money   |
| Load balancers | Not used anymore               |
| Snapshots      | Thousands of backups           |
| Elastic IPs    | Reserved but unused            |
| Databases      | Forgotten staging DB           |
### Example scenario

Engineer creates test environment:

`- 3 EC2 servers - 1 DB - 2 disks`

Deletes only servers.

Result:

- DB + disks remain → \$\$\$ monthly
Janitor Monkey:

```yaml
Find:
- resources not attached
- resources unused for N days
- resources without owner tags

Action:
- notify owner
- wait grace period
- delete
```

### Why this matters

- Cost control
    
- Security (old resources = attack surface)
    
- Operational clarity
    
- Quota limits

**Security Monkey** is an extension of Conformity Monkey. It finds security violations or vulnerabilities, such as improperly configured AWS security groups, and terminates the offending instances. It also ensures that all our SSL and DRM certificates are valid and are not coming up for renewal.
See:
- [[AWS Security Groups]]
- [[SSL/TLS]]
- ### DRM Certificates

Used to protect:

- Movies
    
- TV shows
    
- Streaming rights
    

They prove:

> “This service is allowed to decrypt this content.”

If expired:

- Netflix cannot legally play content
    
- Playback breaks
    

Security Monkey checks:

- Expiry dates
    
- Valid issuer
    
- Correct scope