Related: [[TCP]]
## 🔹 1. What is a “Connection” in TCP?

- A **TCP connection** = a _virtual link_ between two applications (identified by IP+port on each side).
    
- It’s **not a wire**; it’s just a **shared state in both OS kernels**:
    
    - Sequence numbers, ACKs, timers, etc.
        
- Created via the **3-way handshake**:
    
    1. Client → SYN → Server
        
    2. Server → SYN-ACK → Client
        
    3. Client → ACK → Server
        

After this, both ends agree on:

- Initial sequence numbers.
    
- Window sizes.
    
- Ready to transfer bytes reliably.