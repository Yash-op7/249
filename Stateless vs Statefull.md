- Style of storing state within an application and relying on those states to be there otherwise the logic breaks
- This can be taken and applied to a backend, a function, a protocol


## Stateful vs Stateless
- Stateful
	-  Stores state about client in its memory
	- Depends on the information being there (this is the main problem)
- Stateless
	- Client is responsible to "transfer the state" with every request
	- May store something but can safely lose it


### Stateless backends
- Stateless backends can still store data somewhere else
- Can you restart the backend during idle time while the client workflow continues to work.

### What makes a backend stateless
- Stateless backends can store state somewhere else (like a database)
- The backend remains stateless but the system is stateful
- Can you restart the backend during idle time and the client workflow continue to work, think about executions backend and the executions registry

![[Pasted image 20251001174135.png]]
![[Pasted image 20251001174145.png]]
![[Pasted image 20251001174151.png]]

- For stateful client and server you will need sticky load balancing (session token or other info in memory)
- For stateless backend you will have a DB read on every request (verifying session token)

### Stateful vs Stateless protocols
- Talking about the protocols itself, the way we talk between client and server
- The Protocols can be design to store or not store (not rely on) state
- [[TCP]] is stateful, because there is information stored about the client and the server in both the client and the server, there are also sequences, every segment sent is labeled with a sequence, and the sequence is actually stored in the state, there is a state diagram saying the connection is now closed, open, established, waiting, there is a state machine living in the client and server, and they maintain the connection, maintain the sequences, maintain the window sizes, the flow control, the congestion control, the congestion control window, all of this is state information if any of the state is lost, the connection is effectively useless
	- more formal terms: TCP is stateful
		- Sequences, Connection file descriptor
- [[UDP]] is stateless, its message based it doesnt store anything, even if it does store (like the file descriptor), its not sticky
	- example: [[DNS]], a stateless protocol, DNS sends queryID in UDP to identify queries, because when you send out a UDP datagram that has the DNS query you dont send it on a connection because UDP is connection-less, you simply say, hey I am going to this IP address, and this is the destination port, and you put your local port and IP address, and just send it, and since there is no connection we dont know which response to a UDP datagram is for which UDP request, thats why we use a queryID in DNS, (TCP has connections, so any response on the connections means the response is for this request), the DNS server is responsible once the reply comes saying that google.com is this IP here is the queryID this response is sent back to the port which actually sent the DNS resolution request, in this case the query id will come back and the UDP client will read the information and effectively the Linux OS will based on the destination port map it back to the application. But if the application dies then there is no one to accept this datagram, because the destination port no longer exists in the OS, so it can be argued that the DNS client-server is stateful but the protocol, which is UDP is stateless. 
	- [[QUIC]] sends conenctionID to identify connection, but QUIC is stateful, acts like TCP, but because it uses UDP which is stateless, it has to always send the same connectionID with every UDP packet
## 🔹 Stateful vs Stateless Protocols

When we talk about **protocols**, we mean _how two systems (client & server) communicate and whether they keep "memory" of the conversation_.

### **Stateless Protocol**

- Each request/response pair is **independent**.
    
- The server does not remember anything about the client between requests.
    
- If the server crashes and restarts, the client can just send the same request again.
    
- Example: **HTTP (without cookies/sessions)**
    
    - You send: `GET /products`
        
    - Server replies with a list.
        
    - Server doesn’t remember who you are afterward.
        

👉 This is why HTTP is naturally **stateless**, but we “add state” via cookies, sessions, tokens.

---

### **Stateful Protocol**

- The client and server **maintain memory** of the conversation.
    
- This memory is essential to continue communication.
    
- If either side loses the state, the communication breaks.
    

Example: **TCP**

- TCP is not just sending packets; it’s maintaining a **connection** with rules and state machines.
    

---

## 🔹 TCP as a Stateful Protocol

TCP is stateful because **both client and server keep track of connection state**.

### Key TCP State Information:

1. **Connection state**
    
    - Closed → SYN_SENT → ESTABLISHED → FIN_WAIT → TIME_WAIT, etc.
        
    - Both client and server track this handshake/teardown progress.
        
2. **Sequence numbers**
    
    - Every byte of data is numbered.
        
    - The receiver acknowledges which byte ranges it has received.
        
    - If you lose the state (sequence number), you can’t reconstruct the stream.
        
3. **Acknowledgements (ACKs)**
    
    - Client: “I got up to byte #1500.”
        
    - Server updates its state and knows it can stop retransmitting those bytes.
        
4. **Flow control**
    
    - Each side maintains a **window size** → how much data it can receive at once.
        
    - This prevents overwhelming the receiver.
        
5. **Congestion control**
    
    - TCP tracks congestion windows, slow start, etc.
        
    - If the state is lost, congestion handling breaks.
        

👉 **Why is this stateful?**  
Because if either the client or server forgets any of this (sequence #, ACKs, window sizes, etc.), the TCP connection is **useless**. They’d have to tear it down and start over.

---

## 🔹 Example to Contrast

- **HTTP (stateless)**:  
    Client: `GET /page1` → Server responds. Done.  
    Next request: `GET /page2` is independent.
    
- **TCP (stateful)**:
    
    - Client: "I’ve sent bytes 1–1000."
        
    - Server: "I got up to 1000, send 1001–2000."
        
    - Both sides rely on this memory.  
        If the client crashes and forgets, the server won’t know where to resume → connection broken.
        

---

✅ So, when the speaker says **[[TCP]] is stateful**, they mean:

- It requires **connection state** on both ends.
    
- The state includes **sequence numbers, ACKs, windows, congestion control, timers**.
    
- Losing that state invalidates the connection.

---
## 🔹 1. Why TCP is Stateful (quick recap)

- TCP keeps **lots of memory ("state") in the OS kernel** per connection:
    
    - Sequence numbers (to reassemble data in order).
        
    - ACK numbers (to confirm delivery).
        
    - Flow control (receiver’s buffer size).
        
    - Congestion control (network health).
        
    - Connection state (SYN_SENT, ESTABLISHED, etc).
        
- If the OS forgets this (crash or restart) → **connection dies**.
    

So TCP = **stateful protocol**.

---

## 🔹 2. UDP: Stateless Protocol

UDP is much simpler:

- No handshake. No connection.
    
- You just say: “Send this message to IP:port X” → it goes out as a **datagram**.
    
- The receiver gets the datagram, but:
    
    - There’s no guarantee it arrived.
        
    - No order guarantee.
        
    - No retransmission.
        
- UDP doesn’t **remember** anything once the packet is sent.
    

That’s why we call UDP **stateless**.

👉 The only "state" is the **file descriptor** (like a door you use to send/receive packets), but the OS doesn’t track sequence/order/etc.

---

## 🔹 3. DNS Example (stateless protocol built on UDP)

- DNS often uses UDP for queries (fast, lightweight).
    
- Problem: If I send **multiple DNS queries** from the same client, how do I know which response belongs to which query?
    
    - Solution → add a **queryID** (random number) inside the DNS request.
        
    - When the DNS server replies, it **copies that ID** into the response.
        
- Client checks: _“Ah, response ID=123, I sent request ID=123 → this reply is for me.”_
    

📌 Note: DNS client app is stateful (it remembers which IDs it sent), but **UDP itself isn’t**. If the app dies, the OS discards the response because there’s no one listening on that port anymore.

---

## 🔹 4. QUIC (TCP-like behavior on UDP)

QUIC is a modern protocol designed by Google, used in HTTP/3.

Problem it solves:

- UDP is stateless (no connection), but QUIC wants features like TCP (ordering, reliability, congestion control, encryption).
    
- So QUIC **builds state on top of UDP**:
    
    - Every packet carries a **Connection ID**.
        
    - QUIC keeps its own state (sequence numbers, retransmissions, encryption keys) at the application layer.
        

So:

- UDP = stateless.
    
- QUIC = **stateful protocol built on top of UDP** (kind of "re-implementing TCP-like features").
    

This allows QUIC to do things TCP can’t (like very fast connection setup, and surviving IP changes e.g., moving from Wi-Fi to 4G without disconnecting).

---

## 🔹 Summary Table

|Protocol|Stateful / Stateless|Why|
|---|---|---|
|**TCP**|Stateful|Keeps seq numbers, ACKs, window sizes, connection states in OS.|
|**UDP**|Stateless|Just sends datagrams, no memory of order, delivery, or connections.|
|**DNS (over UDP)**|Stateless protocol, but client adds queryID|Uses IDs to map replies to requests.|
|**QUIC (over UDP)**|Stateful|Implements seq, ACKs, retransmission, connectionID in app layer.|

---

👉 The big insight from the lecture:

- **Stateless protocols** → simple, lightweight, but the app has to carry context (like DNS query IDs).
    
- **Stateful protocols** → heavier, but much easier for apps since the protocol handles ordering, reliability, etc.