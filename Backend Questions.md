### **[[Connections & Protocols]]**

- TCP vs UDP (stateful vs stateless, use cases).
    
- HTTP/1.1 vs HTTP/2 vs HTTP/3 (multiplexing, head-of-line blocking, QUIC).
    
- TLS/mTLS (why, how it works, certs).
    
- DNS (recursive vs authoritative, caching, why UDP, when TCP).'

**IQs:**

- “Explain difference between TCP and UDP with examples.”
    
- “Why does HTTP/3 use QUIC?”
    
- “What happens when you type google.com in a browser?”
---
### **Stateful vs Stateless**

- Stateful vs stateless servers.
    
- Sticky sessions vs stateless JWT-based auth.
    
- Why stateless systems scale better.
    

**Interview Qs:**

- “What is the difference between cookies, sessions, and tokens?”
    
- “Why are stateless servers easier to scale?”
    

---

### **Load Balancing & Scaling**

- L4 vs L7 load balancing.
    
- Sticky sessions.
    
- Reverse proxy (Nginx, Envoy).
    
- Service mesh (Istio, sidecar).
    

**Interview Qs:**

- “What is a reverse proxy?”
    
- “How do you scale a stateful app vs a stateless one?”
    

---

### **Reliability & Fault Tolerance**

- Circuit breaking, retries, timeouts.
    
- Cascading failures.
    
- Canary releases, blue-green deployments.
    

**Interview Qs:**

- “What is circuit breaking and why is it needed?”
    
- “What’s the difference between a canary release and A/B testing?”
    

---

### **Async & Messaging**

- Request/response vs Pub/Sub.
    
- Kafka, RabbitMQ basics.
    
- Why Pub/Sub reduces coupling.
    

**Interview Qs:**

- “When would you use Pub/Sub instead of request/response?”
    
- “How does Kafka handle durability?”
    

---

### **Caching**

- CDN, reverse proxy caching.
    
- Redis / Memcached.
    
- Cache invalidation.
    

**Interview Qs:**

- “What are some caching strategies?”
    
- “Why is cache invalidation hard?”

---
### **Databases**

- SQL vs NoSQL.
    
- CAP theorem.
    
- Sharding, replication, consistency.
    

**Interview Qs:**

- “Explain the CAP theorem.”
    
- “How does replication improve availability?”
    

---

👉 In short: Backend interviews test **how you design systems**: scalability, reliability, consistency, and networking fundamentals.