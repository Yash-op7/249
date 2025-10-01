Related:
- [[Kafka]]

> One publisher many readers

### Req/resp Pros and Cons (explained in the next sections)
- Pros
	- Elegant and Simple
	- Scalable
- Cons
	- Bad for multiple receivers
	- High coupling
	- Client/server have to be running
	- Chaining, circuit breaking thats why we have service mesh and sidecar proxies, etc

---

## 🔹 Request/Response Model (normal HTTP or RPC)

This is the "classic" model:

- A **client** sends a **request** to the **server**.
- The **server** computes something and sends back a **response**.
- The interaction ends.

---

## ✅ [[Pros of Req/Resp]]

1. **Elegant and Simple**
    
    - Easy to understand: one question, one answer.
        
    - Works great for typical CRUD APIs (`GET /users/123 → {id:123, name:"Alice"}`).
        
2. **Scalable**
    
    - Horizontally scalable: you can add more servers behind a load balancer.
        
    - Each request is stateless and independent → you can handle millions of them.
        

---

## ❌ Cons of Req/Resp

1. **Bad for multiple receivers**
    
    - If **one event** needs to be delivered to **many clients**, request/response struggles.
        
    - Example: A chat message arrives → 100 users should get it.
        
    - With request/response, the server would have to send **100 separate responses**, because a response only goes back to the client who requested.
        
    - There’s no "broadcast" capability.
        
2. **High coupling**
    
    - Client must know **exactly which server endpoint to call**.
        
    - Server must be designed to answer those requests.
        
    - If the API changes, all clients break.
        
    - Tight coupling between producer (client) and consumer (server).
        
3. **Client and server both must be running**
    
    - No offline delivery.
        
    - If the client is down when the server responds, the response is lost.
        
    - That’s fine for fetching a web page, but bad for messaging/notifications, where you’d want the message even if you were offline.
        
4. **Chaining, circuit breaking**
    
    - In microservices, one service often calls another, and that one calls another (request chains).
        
    - If one link is slow or down, the whole chain suffers.
        
    - This creates fragility → need "circuit breakers" (cut off calls when a service is down) and service meshes/sidecars to manage retries, failures, timeouts.
        
    - So request/response gets complex in distributed systems.
        

---

## 📌 Why Pub/Sub Helps

- Pub/Sub = "One publisher, many subscribers".
    
- Instead of direct requests, events are published into a **message broker** (Kafka, RabbitMQ, Redis Streams, etc.).
    
- Subscribers pick them up asynchronously.
    
- Solves:
    
    - Broadcasts (multiple receivers).
        
    - Decoupling (publisher doesn’t care who’s listening).
        
    - Offline delivery (broker stores events until subscribers come online).
        

---

✅ **Summary for you:**

- **Req/Resp** is perfect for simple APIs (CRUD, synchronous data fetches).
    
- But it **breaks down** when you need **broadcasts, decoupling, offline delivery, or resilience** in microservices.
    
- That’s why **pub/sub systems** exist: to solve these limitations.

---
⭐ This is an important part of why people move from plain **request/response** to more advanced patterns like **pub/sub** or **service meshes**. Let’s break it down slowly.

---

## 🔗 1. [[Chaining]]

- In **[[microservices]]**, one request often triggers a **chain of requests**:
    
    - Example:
        
        - User calls **API Gateway** →
            
        - Gateway calls **Auth Service** →
            
        - Auth calls **User Service** →
            
        - User Service calls **Billing Service**.
            
- This is a **request chain**.
    
- Problem: if **one link is slow or fails**, the whole chain is affected → the original request is delayed or fails.
    

---

## 🔌 2. [[Circuit Breaking]]

- Imagine a microservice is **down or overloaded**.
    
- If callers keep sending requests, they will just pile up, time out, and waste resources.
    
- **Circuit breaker pattern** solves this:
    
    - If a service is failing too much, the caller “opens the circuit” and **stops sending requests temporarily**.
        
    - After a cool-off, it checks again.
        
    - Like an electrical circuit breaker that trips to stop overload.
        

So instead of endlessly retrying a dead service, the system “fails fast” and prevents [[cascading failures]].
- if **Service A** calls **Service B**, then **Service A is the caller**. The circuit breaker lives on the caller side.

- kinda like rate limiting, if the server is overloaded and working on requests, causing new requests to fail, the new requests are stopped before reaching the server, allow requests through after a while

## 🌊 [[Cascading Failures]] - Circuit Breaking Application

This is when **one service failing causes others to fail**. Example:

1. Service A → Service B → Service C.
    
2. Service C becomes slow/unavailable.
    
3. Service B keeps waiting on C, so B threads/connections get stuck.
    
4. Service A keeps calling B, so A also gets stuck.
    
5. Very quickly, the whole chain collapses = cascading failure.
    

Without a circuit breaker:

- Calls keep piling up.
    
- Every upstream service wastes resources waiting for a dead downstream.
    
- Eventually, the system runs out of threads/connections.
    

With a circuit breaker:

- Service B stops calling C after repeated failures.
    
- Service A gets a quick "failure response" from B.
    
- System degrades _gracefully_ instead of collapsing.
> ⭐ So circuit breakers work locally (protect the caller), but you still need monitoring to debug root cause.

---
## 🛠️ 3. [[Service Mesh]]

- A **service mesh** is an infrastructure layer for managing service-to-service communication. At enterprise layer, the number of microservices in the backend can be huge, service mesh helps manage this 
    
- It handles:
    - Load balancing
    - Service discovery
    - Retries
    - Circuit breaking
    - Monitoring & security
        
- Popular example: **Istio** (built on top of Envoy proxies).
    

---

## 📦 4. [[Sidecar Proxies]]

- A **sidecar proxy** is a helper container that sits next to your service.
    
- Instead of your service making direct HTTP calls, it sends traffic to the sidecar proxy.
    
- The proxy then handles retries, timeouts, circuit breaking, encryption, etc.
    
- In Kubernetes, every pod can have a sidecar proxy → all the networking smarts are handled without changing app code.
    

---

## ⚡ Putting it all together

- **Chaining** = multiple services depend on each other in one request path.
    
- **Circuit breaking** = protect the system when one service in the chain fails.
    
- **Service mesh + sidecar proxies** = infrastructure solution to manage these automatically, instead of writing logic in every service.
    

---

✅ **In short:**  
Request/response in microservices creates fragile **chains of calls**. If one service fails, everything can break.  
We use **circuit breakers** to cut off failing calls, and **service meshes with sidecars** to handle retries, load balancing, and failures automatically.

### How this is done in practice:
## 🟢 [[Istio + Envoy]]

- **Envoy** = a high-performance proxy (built by Lyft).
    
- **Istio** = a **service mesh** that orchestrates Envoy proxies.
    
- How they work together:
    
    - Every service pod in Kubernetes gets an **[[Envoy]] sidecar proxy**.
        
    - Istio controls all these Envoys (central management plane).
        
    - So your services don’t need to know about retries, load balancing, TLS, metrics → Envoy does it for them.
        

Think of **Envoy = engine**, **Istio = control tower**.


⭐ **In summary:**

- **Chaining** = request passes through multiple services.
    
- **Cascading failure** = one slow/failing service drags the whole chain down.
    
- **Circuit breaker** = caller-side protection to cut off failing calls quickly.
    
- **Sidecar proxy** = helper next to your service that handles all network concerns.
    
- **Istio + Envoy** = the modern way to implement this at scale in Kubernetes.
- **Envoy** → most common example of a **sidecar proxy**.
    
- **Sidecar pattern** → each microservice pod has its own **helper container** (sidecar) that does generic networking work.

What sidecar handles:

- **Service discovery**: finds where other services live (IP, port).
    
- **Load balancing**: balances outgoing requests to other service instances.
    
- **Circuit breaking**: protects against failing downstreams.
    
- **Retries & timeouts**:
    
    - _Retry_ = if a request to Service B fails, try again (maybe another instance).
        
    - _Timeout_ = stop waiting after X ms instead of hanging forever.
        
- **Monitoring & logging**: every request is observed → metrics and logs.
    
- **Encryption (TLS/mTLS)**:
    
    - **TLS** = normal HTTPS (server proves identity, client trusts server).
        
    - **mTLS** (mutual TLS) = both client and server authenticate each other. Useful in zero-trust networks → ensures only legit services talk.
        
- **Proxy (concise)**:
    
    - A **[[proxy]]** is an intermediary that sits between client ↔ server.
        
    - **[[Forward proxy]]**: sits on client’s side, hides client identity, forwards requests (e.g., corporate proxy for browsing).
        
    - **[[Reverse proxy]]**: sits on server’s side, hides servers, balances load (e.g., Nginx, Envoy as ingress).
        
    - **[[Sidecar proxy]]**: runs _with_ the app, intercepts its traffic.
        
- **Service Mesh (Istio)**:
    
    - Yes, it’s an **infrastructure layer** that manages how services talk.
        
    - Istio’s **control plane** configures Envoy **data plane** proxies.
        
    - So Istio tells Envoy proxies: "Here are the IPs/ports, here’s the load balancing/circuit breaker/TLS rules."
        
    - Envoy proxies handle the actual traffic.
        

👉 So yes, your understanding is correct — just needed small clarifications on retries, timeouts, TLS/mTLS, and proxy types.

**this was the question:** So envoy is an example for implementing sidecar pattern, where sidecar pattern is when each microservice pod in a backend kubernetes cluster has its own personal helper container which takes care of repetitive general logic unrelated to the service such as where other services live, load balancing incoming and outgoing requests, circuit breaking, retries and timeouts (what are the retries and timeouts in this context just tell me concisely), monitoring, logging and encrypting traffic using TLS (what is mTLS) and this microservice specific container is called a sidecar proxy (all tell me what is a proxy concisely, reverse proxy, forward proxy), and all the sidecar Envoy proxies are managed by Istio a service which implements service mesh, and service mesh is an infrastructure layer which takes care of connecting microservices to each other, service discovery, load balancing between services, and istio provides this service information such as IPs and ports of other services to Envoy proxies, and manages Envoy proxy containers. do i understand everything correctly?

---

## ✅ 2. Benefits of [[Sidecar Pattern]] (clarified)

### **Standardized policies**

Yes, exactly → things like:

- **Security policies**: mTLS everywhere, no plaintext traffic.
    
- **Traffic policies**: retries, timeouts, circuit breaking rules.
    
- **Load balancing policies**: round robin, least-connections, weighted.
    
- **Access policies**: which service can talk to which (zero trust networking).
    

Instead of every team writing their own retry logic differently, the sidecar enforces the same rules everywhere.

---

## Back to PubSub
![[Pasted image 20251001164912.png]]

## Pros and Cons of Pub/Sub
- Pros
	- Scales with multiple receivers/consumers
	- Great for microservices
	- Loose coupling
	- Works even when the clients are offline
- Cons
	- Message delivery issues (The classic Two Generals Problem)
		- How to know if the consumer actually got the message, what if the message got consumed twice, 
	- Adds Complexity
		- Adding more brokers, partitions, to scale better, partition keys
	- Network saturation, in case of polling between clients and the brokers

## Last section, simple RabbitMQ example
- trivial