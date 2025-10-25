## 🔹 0. A Basic Crash Course of TCP

Think of TCP as a **reliable courier service** that runs _on top of IP_.

- **IP**: delivers raw packets (like postal service, but sometimes loses/misorders them).
    
- **TCP**: adds reliability, ordering, and connection semantics.
    

Key features of TCP:

- **Connection-oriented** (handshake before sending).
    
- **Reliable** (lost packets retransmitted).
    
- **Ordered delivery** (packets arrive in the right sequence).
    
- **Flow control** (don’t overwhelm the receiver).
    
- **Congestion control** (don’t overwhelm the network).
    

👉 Applications don’t deal with packets. They just “send a stream of bytes,” and TCP ensures it gets there safely and in order.

## 🔹 5. Who Maintains TCP State? (OS vs Application)

### Application’s job

- Your code just calls:
    
    - `send(socket, data)` → “please send this”
        
    - `recv(socket, buffer)` → “please give me received data”
        

That’s it. Applications don’t deal with packets, retransmissions, sequence numbers, etc. They only see a **clean byte stream**.

### OS Kernel’s job

- The **TCP implementation lives in the OS kernel** (Linux, Windows, macOS).
    
- Each connection is represented by a **socket** (file descriptor).
    
- The kernel manages:
    
    - **Retransmission** → if no ACK arrives for a segment, resend it.
        
    - **Flow control** → use the receiver’s _window size_ to avoid sending too much.
        
    - **Congestion control** → algorithms like Reno, Cubic, BBR slow down/speed up sending rate to avoid congesting the network.
        
    - **Timers** → for retransmissions, keepalive, connection timeouts.
        
    - **Handshake** (SYN, SYN-ACK, ACK) and teardown (FIN, RST).
        

So when we say _“TCP remembers state”_, it means **the kernel stores per-connection metadata** in memory (sequence numbers, buffers, timers, etc.).

👉 This is why losing OS memory (like a crash or reboot) breaks all TCP connections: the state machine is gone.

---
## 🔹 6. The TCP State Machine

TCP is modeled as a **finite state machine (FSM)**:

- Each connection has a current **state** (like `ESTABLISHED`, `LISTEN`, `CLOSED`).
    
- Events (like receiving a SYN, or the app calling `close()`) cause **transitions**.
    

Simplified flow:

1. **LISTEN** → waiting for new connections (server socket).
    
2. **SYN_SENT** → client sent SYN, waiting for SYN-ACK.
    
3. **SYN_RECEIVED** → server got SYN, replied with SYN-ACK, waiting for ACK.
    
4. **ESTABLISHED** → handshake complete, data can flow.
    
5. **FIN_WAIT / CLOSE_WAIT / LAST_ACK** → states for graceful shutdown.
    
6. **TIME_WAIT** → safety period after close to ensure stray packets don’t confuse future connections.
    

👉 Each of these states corresponds to a step in the **connection lifecycle**.

---
## 🔹 7. Connection States (Common Ones)

Here’s a rundown of the famous ones you’ll see in `netstat` or `ss`:

- **LISTEN** → server is passively waiting for incoming connections.
    
- **SYN_SENT** → client has sent SYN, waiting for response.
    
- **SYN_RECEIVED** → server has sent SYN-ACK, waiting for final ACK.
    
- **ESTABLISHED** → connection is live, data transfer happening.
    
- **FIN_WAIT_1** → one side has sent FIN, waiting for ACK.
    
- **FIN_WAIT_2** → FIN was ACK’d, waiting for other side’s FIN.
    
- **CLOSE_WAIT** → peer sent FIN, waiting for app to call `close()`.
    
- **LAST_ACK** → app closed, waiting for ACK of FIN.
    
- **TIME_WAIT** → (important!) after close, wait 2×MSL (max segment lifetime) to ensure delayed packets don’t interfere with new connections.
    
- **CLOSED** → no connection exists.