## 🔑 Basic terms

### **1. Connection**

- A **TCP connection** is like a pipe between your browser and the server.
    
- Every HTTP request/response flows **through a TCP connection**.
    
- Think of it as a **dedicated road** between browser ↔ server.
    

Example:  
When you open a webpage, Chrome might open **6 TCP connections** to `example.com`.

---

### **2. Request**

- An **HTTP request** is a message sent over a connection (e.g. "GET /index.html").
    
- The server replies with an **HTTP response** ("here’s your HTML!").
    
- Then you can reuse the same connection for another request (if the server allows "keep-alive").
    

---

### **3. [[In-flight]] request**

- A request is **[[in-flight]]** when:
    
    - It has been **sent to the server**,
        
    - But the **response hasn’t finished** coming back yet.
        

In **HTTP/1.1**, while one request is in-flight, you **can’t send another request** on the same connection → that connection is **busy**.

---

### **4. [[Pre-flight]] (different thing than in-flight)**

- "Pre-flight" usually refers to **CORS pre-flight requests** (using `OPTIONS`) to check server permissions.

---

### **5. Multiplexing**

- **Multiplexing** = sending **multiple requests and responses at the same time over a single connection**, without blocking each other.
    
- HTTP/2 introduces multiplexing:
    
    - Connection stays open.
        
    - Requests are split into **streams**.
        
    - Server can reply to several requests simultaneously.
        

So:

- HTTP/1.1 → 1 connection = 1 in-flight request at a time.
    
- HTTP/2 → 1 connection = many in-flight requests, all interleaved.
    

---