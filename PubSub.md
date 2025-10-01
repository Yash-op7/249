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
	- Chining, circuit breaking thats why we have service mesh and sidecar proxies, etc

---

## 🔹 Request/Response Model (normal HTTP or RPC)

This is the "classic" model:

- A **client** sends a **request** to the **server**.
    
- The **server** computes something and sends back a **response**.
    
- The interaction ends.
    

---

## ✅ Pros of Req/Resp

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

## 🔗 1. Chaining

- In **microservices**, one request often triggers a **chain of requests**:
    
    - Example:
        
        - User calls **API Gateway** →
            
        - Gateway calls **Auth Service** →
            
        - Auth calls **User Service** →
            
        - User Service calls **Billing Service**.
            
- This is a **request chain**.
    
- Problem: if **one link is slow or fails**, the whole chain is affected → the original request is delayed or fails.
    

---

## 🔌 2. Circuit Breaking

- Imagine a microservice is **down or overloaded**.
    
- If callers keep sending requests, they will just pile up, time out, and waste resources.
    
- **Circuit breaker pattern** solves this:
    
    - If a service is failing too much, the caller “opens the circuit” and **stops sending requests temporarily**.
        
    - After a cool-off, it checks again.
        
    - Like an electrical circuit breaker that trips to stop overload.
        

So instead of endlessly retrying a dead service, the system “fails fast” and prevents cascading failures.

---

## 🛠️ 3. Service Mesh

- A **service mesh** is an infrastructure layer for managing service-to-service communication.
    
- It handles:
    
    - Load balancing
        
    - Service discovery
        
    - Retries
        
    - Circuit breaking
        
    - Monitoring & security
        
- Popular example: **Istio** (built on top of Envoy proxies).
    

---

## 📦 4. Sidecar Proxies

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