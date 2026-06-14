# Daily Routine (2026–2028)

## Mission

**Primary Goal 1:** Become a high-caliber Software Engineer capable of securing stronger backend/distributed systems roles.

**Primary Goal 2:** Build a strong, athletic physique through consistent strength training.

---

# Weekday Schedule

| Time               | Activity                           |
| ------------------ | ---------------------------------- |
| 5:00 AM            | Wake up                            |
| 5:00 – 5:30 AM     | Bath, coffee/tea, light stretching |
| 5:30 – 7:30 AM     | Deep Study Block #1                |
| 7:30 – 8:00 AM     | Breakfast                          |
| 8:00 – 9:30 AM     | Deep Study Block #2                |
| 9:30 – 10:00 AM    | Commute                            |
| 10:00 AM – 7:00 PM | Work                               |
| 7:00 – 7:30 PM     | Commute                            |
| 7:30 – 8:30 PM     | Gym                                |
| 8:30 – 9:15 PM     | Dinner + Bath                      |
| 9:15 – 9:45 PM     | Guitar / Relaxation                |
| 9:45 – 10:00 PM    | Prepare for next day               |
| 10:00 PM           | Sleep                              |

---

## Non-Negotiables

* Sleep: **10:00 PM – 5:00 AM**
* Work: **10:00 AM – 7:00 PM**
* Study before work
* Train every weekday
* **No negotiating with your weaker self**
* No gratification until the work is done

---

# Daily Rules

* Morning study is sacred.
* Never miss sleep to study.
* Never skip training because of low motivation.
* Optimize for consistency, not intensity.
* Track progress weekly, not daily.

---

# Weekly Scorecard

| Metric                   | Target | wk 1 | wk 2 | wk 3 | wk 4 | wk 5 | wk 6 | wk 7 | wk 8 |
| ------------------------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ | ------ |
| Morning Study Sessions   | 10     |        |        |        |        |        |        |        |        |
| Gym Sessions             | 6      |        |        |        |        |        |        |        |        |
| DDIA Chapters Completed  | 1–2    |        |        |        |        |        |        |        |        |
| LeetCode Problems Solved | 15–25  |        |        |        |        |        |        |        |        |
| System Design Sessions   | 2      |        |        |        |        |        |        |        |        |
| Average Sleep            | 7+ hrs |        |        |        |        |        |        |        |        |

---
# Focus Reminder

- Protect the hours between **5:30 AM and 9:30 AM** with your life **CONSISTENTLY!**
- Those four hours determine the trajectory of your career/life more than any other part of the day.
> ***Discipline yourself*** and **Be all you can Be**

![[Screenshot 2026-03-21 at 4.40.23 PM.png]]






# TARGET
  
⚡what are the concrete requirements i need to satisfy to become a Senior Software Engineer in backend and distributed systems in 2026, what do i need study, what do i need to learn, what do i need to do⚡

  

To become a **Senior Software Engineer focused on backend and distributed systems in 2026**, the requirements are much more concrete than many people realize.

Companies may use different titles, but across top technology companies, startups, fintechs, cloud companies, and large enterprises, senior engineers are generally expected to demonstrate competence in **four areas simultaneously**:

1. Technical depth
    
2. System design capability
    
3. Delivery and ownership
    
4. Engineering influence
    

---

# 1. Backend Engineering Fundamentals

You should be able to build production services without relying heavily on frameworks to make decisions for you.

### Programming

Become highly proficient in at least one backend language:

- Go

Know:

- Concurrency
    
- Memory management basics
    
- Networking fundamentals
    
- Error handling
    
- Profiling
    
- Performance optimization
    

For example:

Instead of merely using goroutines, understand:

- Scheduler behavior
    
- Race conditions
    
- Mutexes
    
- Channels
    
- Context cancellation
    
- CPU vs I/O bottlenecks
    

---

### Databases

You should understand databases deeply.

Relational:

- PostgreSQL
    
- MySQL
    

Learn:

- Indexes
    
- Query optimization
    
- Transactions
    
- Isolation levels
    
- MVCC
    
- Replication
    
- Partitioning
    
- Sharding
    

NoSQL:

- Redis
    
- Cassandra
    
- DynamoDB-like concepts
    
- MongoDB (optional)
    

Know:

- When not to use NoSQL
    
- Consistency tradeoffs
    
- Access pattern design
    

---

### Networking

Many engineers underestimate this.

Learn:

- TCP/IP
    
- DNS
    
- TLS
    
- HTTP/1.1
    
- HTTP/2
    
- HTTP/3
    
- gRPC
    
- Load balancing
    

Understand:

- Connection pooling
    
- Timeouts
    
- Retries
    
- Backpressure
    
- Circuit breakers
    

If somebody says:

> "P99 latency increased after enabling retries"

you should understand exactly why.

---

# 2. Distributed Systems (Critical)

This is the core differentiator.

You should understand:

### Consistency

Study:

CAP Theorem

- Strong consistency
    
- Eventual consistency
    
- Causal consistency
    

---

### Consensus

Learn:

- Raft
    
- Paxos (high level)
    

Understand:

- Leader election
    
- Log replication
    
- Quorum
    

Read about:

- etcd
    
- Consul
    

---

### Distributed Data

Know:

- Sharding
    
- Replication
    
- Rebalancing
    
- Hot partitions
    
- Data locality
    

Questions you should answer:

- How do you shard 500 million users?
    
- What happens when one shard grows much larger?
    
- How do you rebalance with minimal downtime?
    

---

### Messaging Systems

Understand deeply:

- Apache Kafka
    
- RabbitMQ
    

Know:

- At-most-once
    
- At-least-once
    
- Exactly-once semantics
    
- Consumer groups
    
- Ordering guarantees
    
- Dead letter queues
    

---

### Failure Handling

Senior engineers think in failures.

Learn:

- Partial failures
    
- Network partitions
    
- Retry storms
    
- Cascading failures
    
- Thundering herd problems
    

Be able to answer:

> What happens when an upstream service becomes slow but doesn't fail?

---

# 3. System Design

This is usually the largest interview component for senior roles.

You should design:

- URL shortener
    
- Notification system
    
- Distributed cache
    
- Chat service
    
- Payment system
    
- Search system
    
- Rate limiter
    
- Job scheduler
    

More importantly:

Explain:

- Scalability
    
- Reliability
    
- Cost
    
- Security
    
- Monitoring
    
- Tradeoffs
    

---

### Capacity Planning

Learn to estimate:

- QPS
    
- Storage growth
    
- Network bandwidth
    
- Memory requirements
    

Example:

If a service handles:

- 100M requests/day
    
- 2 KB/request
    

Can you estimate:

- Daily traffic
    
- Monthly storage
    
- Peak load
    

within a few minutes?

A senior engineer should.

---

# 4. Cloud and Infrastructure

You should be comfortable operating systems in production.

Learn:

### Containers

Docker

Know:

- Images
    
- Layers
    
- Networking
    
- Resource limits
    

---

### Orchestration

Kubernetes

Understand:

- Pods
    
- Services
    
- Deployments
    
- StatefulSets
    
- Ingress
    
- Autoscaling
    

You don't need to be a platform engineer, but you should comfortably deploy and debug services.

---

### Cloud

Pick one:

- Amazon Web Services
    
- Google Cloud
    
- Microsoft Azure
    

Learn:

- Compute
    
- Storage
    
- Networking
    
- IAM
    
- Managed databases
    
- Observability
    

---

# 5. Observability and Production Engineering

Many engineers never learn this.

Senior engineers must.

Study:

- Logging
    
- Metrics
    
- Tracing
    
- Alerting
    

Learn tools such as:

- Prometheus
    
- Grafana
    
- OpenTelemetry
    

Know:

- P50
    
- P95
    
- P99 latency
    
- SLOs
    
- SLIs
    
- Error budgets
    

---

# 6. Software Architecture

Senior engineers make architectural decisions.

Learn:

Patterns:

- Monoliths
    
- Modular monoliths
    
- Microservices
    
- Event-driven systems
    
- CQRS
    
- Event sourcing
    

Understand:

- When microservices are a bad idea
    
- Service boundaries
    
- Domain modeling
    
- API evolution
    

---

# 7. Security Basics

You don't need to be a security engineer.

You do need to understand:

- Authentication
    
- Authorization
    
- OAuth
    
- JWT
    
- Encryption
    
- Secrets management
    
- SQL injection
    
- XSS
    
- SSRF
    

You should design systems that are secure by default.

---

# 8. Engineering Leadership

This is often what separates Senior from Mid-level.

You should be able to:

- Lead projects
    
- Break down ambiguous problems
    
- Write design docs
    
- Review architecture
    
- Mentor juniors
    
- Drive technical discussions
    

A senior engineer doesn't wait for exact instructions.

They identify and solve problems independently.

---

# 9. What You Should Actually Build

Many people study endlessly and remain mid-level.

Build systems.

Examples:

### Project 1

Distributed URL Shortener

Includes:

- PostgreSQL
    
- Redis
    
- Caching
    
- Rate limiting
    
- Kubernetes deployment
    

---

### Project 2

Event-Driven Order System

Includes:

- Kafka
    
- Multiple services
    
- Retry handling
    
- DLQ
    
- Monitoring
    

---

### Project 3

Distributed Job Scheduler

Includes:

- Leader election
    
- Worker nodes
    
- Retry policies
    
- Horizontal scaling
    

---

### Project 4

API Gateway

Features:

- Authentication
    
- Rate limiting
    
- Metrics
    
- Tracing
    

---

# 10. Books Worth Reading

Must-read:

- Designing Data-Intensive Applications
    
- Database Internals
    
- System Design Interview – An Insider's Guide
    
- The Pragmatic Programmer
    

Advanced:

- Distributed Systems
    
- Site Reliability Engineering
    

---

# A realistic 2026 senior engineer checklist

If you can confidently do **most** of the following, you're operating near senior level:

- Build production-grade backend services.
    
- Design systems handling millions of requests.
    
- Diagnose latency and performance problems.
    
- Design reliable APIs.
    
- Understand distributed system tradeoffs.
    
- Operate services in cloud environments.
    
- Debug production incidents.
    
- Create architecture/design documents.
    
- Lead projects across multiple engineers.
    
- Mentor less experienced developers.
    
- Make engineering decisions based on scalability, reliability, and cost tradeoffs.
    

The fastest path is usually:

**Backend language → Databases → Networking → Distributed Systems → Cloud/Kubernetes → Observability → System Design → Leading increasingly larger projects.**

That progression mirrors how many backend engineers actually grow into senior roles at companies building large-scale distributed systems.
