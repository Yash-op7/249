# **Topic 6: Networking Concepts**

---

## **1. ISO / OSI Stack (OSI Model)** ⭐⭐⭐

1. **OSI = Open Systems Interconnection** model.
    
2. **7-layer reference model** (conceptual, not implemented fully).
    
3. Layers (top → bottom):
    
    - Application
        
    - Presentation
        
    - Session
        
    - Transport
        
    - Network
        
    - Data Link
        
    - Physical
        
4. **Application layer**:
    
    - User-facing services (HTTP, FTP, SMTP).
        
5. **Presentation layer**:
    
    - Encryption, compression, data format.
        
6. **Session layer**:
    
    - Session establishment, maintenance, termination.
        
7. **Transport layer**:
    
    - End-to-end delivery (TCP, UDP).
        
8. **Network layer**:
    
    - Logical addressing & routing (IP).
        
9. **Data Link layer**:
    
    - Framing, MAC address, error detection.
        
10. **Physical layer**:
    
    - Bits, cables, voltages.
        

**MCQ favorite**: Map protocol → OSI layer.

---

## **2. LAN Technologies**

### **Ethernet**

1. Most widely used LAN technology.
    
2. Uses **CSMA/CD** (collision detection).
    
3. Star topology (modern).
    
4. Fast and scalable.
    
5. Uses MAC addresses.
    
6. IEEE standard: 802.3.
    
7. Collisions possible (legacy hubs).
    
8. Supports high data rates.
    
9. Cheap and reliable.
    
10. **MCQ trap**:
    
    - Ethernet ≠ token passing.
        

---

### **Token Ring**

1. Uses **token passing**.
    
2. No collisions.
    
3. Ring topology.
    
4. Token circulates continuously.
    
5. IEEE standard: 802.5.
    
6. Deterministic access.
    
7. Slower than Ethernet.
    
8. Rarely used today.
    
9. Requires token to transmit.
    
10. **MCQ favorite**:
    
    - Token ring avoids collisions.
        

---

## **3. TCP vs UDP**

### **TCP (Transmission Control Protocol)**

1. Connection-oriented.
    
2. Reliable data delivery.
    
3. Error detection + correction.
    
4. Uses acknowledgements.
    
5. Flow control.
    
6. Congestion control.
    
7. Slower but reliable.
    
8. Uses three-way handshake.
    
9. Ordered delivery.
    
10. Used in HTTP, FTP, SMTP.
    

---

### **UDP (User Datagram Protocol)**

1. Connectionless.
    
2. No reliability guarantee.
    
3. No acknowledgements.
    
4. Faster than TCP.
    
5. No flow control.
    
6. No congestion control.
    
7. Packets may be lost or reordered.
    
8. Lightweight protocol.
    
9. Used in DNS, streaming.
    
10. **MCQ favorite**:
    
    - UDP = fast but unreliable.
        

---

## **4. IP (Internet Protocol)**

1. Network layer protocol.
    
2. Provides **logical addressing**.
    
3. Responsible for routing packets.
    
4. Connectionless.
    
5. Best-effort delivery.
    
6. IPv4 uses 32-bit addresses.
    
7. IPv6 uses 128-bit addresses.
    
8. Does not guarantee delivery.
    
9. Works with TCP/UDP.
    
10. **MCQ trap**:
    
    - IP ≠ reliable protocol.
        

---

## **5. Switches**

1. Operate at **Data Link layer**.
    
2. Use MAC addresses.
    
3. Forward frames intelligently.
    
4. Reduce collisions.
    
5. Faster than hubs.
    
6. Maintain MAC address table.
    
7. Support full-duplex.
    
8. Used in LANs.
    
9. One collision domain per port.
    
10. **MCQ favorite**:
    
    - Switch ≠ router.
        

---

## **6. Routers**

1. Operate at **Network layer**.
    
2. Use IP addresses.
    
3. Connect different networks.
    
4. Decide best path.
    
5. Use routing tables.
    
6. Break broadcast domains.
    
7. Slower than switches.
    
8. Used in WANs.
    
9. Can run routing protocols.
    
10. **MCQ trap**:
    
    - Router ≠ switch (layer difference).
        

---

## **7. Gateways**

1. Connect **different types of networks**.
    
2. Can work at **multiple layers**.
    
3. Perform protocol translation.
    
4. More complex than routers.
    
5. Used between dissimilar systems.
    
6. Example: Email gateway.
    
7. Often implemented in software.
    
8. Slower than routers.
    
9. Used in enterprise networks.
    
10. **MCQ favorite**:
    
    - Gateway ≠ router.
        

---

## **8. Application Layer Protocols**

---

### **DNS (Domain Name System)**

1. Converts domain names to IP addresses.
    
2. Uses UDP (mostly).
    
3. Hierarchical system.
    
4. Faster lookup.
    
5. Caches results.
    
6. Port number: 53.
    
7. Distributed database.
    
8. Reduces human effort.
    
9. Domain → IP mapping.
    
10. **MCQ favorite**:
    
    - DNS uses UDP.
        

---

### **SMTP (Simple Mail Transfer Protocol)**

1. Used to **send emails**.
    
2. Push protocol.
    
3. Uses TCP.
    
4. Port 25.
    
5. Works with mail servers.
    
6. Does not retrieve emails.
    
7. Reliable delivery.
    
8. Text-based.
    
9. Works with POP/IMAP.
    
10. **MCQ trap**:
    
    - SMTP ≠ mail retrieval.
        

---

### **POP (Post Office Protocol)**

1. Used to **retrieve emails**.
    
2. Pull protocol.
    
3. Downloads mail to client.
    
4. Uses TCP.
    
5. Port 110.
    
6. Deletes mail from server (default).
    
7. Simple protocol.
    
8. Works offline after download.
    
9. Limited server storage.
    
10. **MCQ favorite**:
    
    - POP downloads emails locally.
        

---

### **FTP (File Transfer Protocol)**

1. Used for file transfer.
    
2. Uses TCP.
    
3. Connection-oriented.
    
4. Uses two connections (control & data).
    
5. Port 21 (control).
    
6. Not secure by default.
    
7. Supports authentication.
    
8. Stateful protocol.
    
9. Older protocol.
    
10. **MCQ trap**:
    
    - FTP ≠ HTTP.
        

---

### **HTTP (HyperText Transfer Protocol)**

1. Used for web communication.
    
2. Stateless protocol.
    
3. Uses TCP.
    
4. Port 80.
    
5. Request–response model.
    
6. Client-server based.
    
7. No session info by default.
    
8. HTTPS adds security.
    
9. Widely used.
    
10. **MCQ favorite**:
    
    - HTTP is stateless.
        

---

## **9. Firewalls**

1. Network security device.
    
2. Filters incoming/outgoing traffic.
    
3. Based on rules.
    
4. Protects internal network.
    
5. Can be hardware or software.
    
6. Works at multiple layers.
    
7. Blocks unauthorized access.
    
8. Allows authorized traffic.
    
9. First line of defense.
    
10. **MCQ trap**:
    
    - Firewall ≠ antivirus.