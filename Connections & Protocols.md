# Connections & Protocols — deep dive

---

## 1) TCP vs UDP — stateful vs stateless, trade-offs & examples

### What they are, simply

- **TCP (Transmission Control Protocol)**
    
    - _Connection-oriented, reliable, ordered, flow- & congestion-controlled._
        
    - The OS kernel keeps per-connection state (sequence numbers, ACKs, windows, timers).
        
- **UDP (User Datagram Protocol)**
    
    - _Connection-less, best-effort datagrams._
        
    - No ordering, no retransmission, no built-in congestion control. Very small protocol header and little overhead.
        

### Why TCP is stateful and UDP is stateless

- **TCP**: requires a handshake, keeps sequence numbers and windows; both endpoints must remember these values. If that state is lost, the connection breaks.
    
- **UDP**: each packet is independent; the protocol doesn’t track conversation state. The only “state” is ephemeral routing in the OS (ports), but not the protocol-level stream state.
    

### Trade-offs

- **TCP**: + reliability, ordered delivery, easy for developers (streams) — but heavier, higher latency on start (handshake), more CPU and memory for state.
    
- **UDP**: + minimal overhead, low latency — but app must handle loss, reordering, retransmission if needed.
    

### Typical applications

- **TCP**: HTTP (web), SMTP, FTP, SSH, databases, anything where correctness matters.
    
- **UDP**: DNS (queries), real-time voice/video (VoIP, WebRTC media), online games, broadcasting, QUIC (HTTP/3 uses UDP as transport but implements reliability).
    

---

## 2) HTTP/1.1 vs HTTP/2 vs HTTP/3 — key differences

### Core goals across versions

- Reduce latency, make better use of connections ([[connections in tcp]]), reduce head-of-line ([[TCP HOL]]) problems, compress headers, and allow multiple concurrent logical streams.
    

### HTTP/1.1

- Text-based, one request at a time per TCP connection (pipelining exists but is rarely used because of issues).
    
- Browsers open multiple TCP connections per origin (commonly ~6) to parallelize downloads.
    
- **Problem:** head-of-line blocking at the connection level (one lost TCP packet blocks everything on that connection).
    

### HTTP/2

- Binary framing, multiplexing many streams over a single TCP connection.
    
- Header compression (HPACK) reduces overhead.
    
- Server push possible.
    
- **BUT** it still runs over TCP → TCP-level head-of-line (HOL) blocking remains: if a TCP packet is lost, **all** HTTP/2 streams multiplexed on that TCP connection wait while TCP recovers the lost packet.
    

### HTTP/3 (QUIC)

- Uses **QUIC** (user-space protocol over UDP).
    
- Multiplexing implemented in QUIC itself so streams are independent: **per-stream loss recovery** → no TCP HOL at the transport level.
    
- QUIC integrates TLS 1.3 for encryption and handshake.
    
- Faster connection setup (0-RTT/1-RTT) and connection migration (works across IP/port changes like switching networks).
    

### Summary table

| Feature            | HTTP/1.1            | HTTP/2           | HTTP/3 (QUIC)           |
| ------------------ | ------------------- | ---------------- | ----------------------- |
| Framing            | Text                | Binary           | Binary (QUIC)           |
| Multiplexing       | No (per-connection) | Yes (over TCP)   | Yes (built into QUIC)   |
| Header compression | No                  | HPACK            | QPACK (HTTP/3)          |
| Transport          | TCP                 | TCP              | UDP (QUIC)              |
| HOL blocking       | Yes                 | Yes (TCP-level)  | No (per-stream in QUIC) |
| TLS                | Optional            | Usually with TLS | Integrated TLS 1.3      |

---

## 3) Head-of-Line (HOL) blocking — why it matters

- **HOL at TCP level:** TCP delivers an ordered byte stream. If a packet is lost, later bytes must wait until the lost packet is retransmitted and received. For multiplexed protocols running on top of TCP (HTTP/2), that means one lost TCP packet stalls _all_ logical streams on that TCP connection.
    
- **QUIC fixes it** by handling streams separately: loss on stream A does not stall stream B.
    

---

## 4) QUIC — why invented and major advantages

- Built to avoid TCP HOL for multiplexed web traffic, reduce setup latency, and let connections survive client network changes.
    
- Key features:
    
    - Multiplexed streams with independent reliability.
        
    - Connection IDs for migration (move IPs/ports transparently).
        
    - Integrated TLS 1.3 (crypto + transport in one handshake).
        
    - User-space implementation (faster innovation, not stuck in kernel).
        
    - 0-RTT resumption for faster reconnects.
        

---

## 5) Typical interview points for HTTP versions

- Explain multiplexing.
    
- Why HTTP/2 improved over 1.1 but still had TCP HOL.
    
- Why QUIC in HTTP/3 improves latency and connection robustness.
    

---

## 6) Quick ASCII of TCP handshake & HTTP request (for context)

TCP 3-way handshake:

```cpp
Client                         Server
  SYN  -------------------->
                    <------  SYN+ACK
  ACK  -------------------->
Connection established

```

HTTP/1.1 request over TCP:

```cpp
[Client] --(SYN, SYN-ACK, ACK)--> [connected TCP]
[Client] --> GET / HTTP/1.1
[Server] --> HTTP/1.1 200 OK + body
[Client] consumes response
```

HTTP/2/3: multiple logical requests can flow concurrently after connection setup.

---

## 4) [[TLS & mTLS — what, why, how (certs)]]

### TLS — core idea

- ⭐Secure transport: **confidentiality**, **integrity**, and **server authentication**.
    
- Uses public-key cryptography for initial handshake and symmetric keys for bulk encryption.
    

### TLS handshake (simplified, TLS 1.3 style)

1. **ClientHello** (client proposes crypto suites, sends a random nonce).
    
2. **ServerHello** (server picks suite, sends certificate and server’s nonce).
    
3. **Client verifies certificate chain** → if valid, both derive shared symmetric keys.
    
4. **Encrypted handshake messages** complete and application data is sent encrypted.
    

- After handshake, both sides use symmetric keys (fast) to encrypt traffic.
    

### Certificates & CA chain

- Servers present an X.509 certificate signed by a Certificate Authority (CA).
    
- Client verifies the signature chain up to a trusted CA root in its trust store.
    
- Certificate binds a public key to an identity (domain). Validation includes expiry, chain, hostname match, revocation checks.
    

### mTLS (mutual TLS)

- **mTLS** = both client and server present certificates and validate each other.
    
- Use cases: internal microservices, zero-trust environments, service meshes.
    
- Benefits: strong mutual authentication (no shared secrets needed in requests).
    

### Extra notes (interview hooks)

- ==TLS 1.3 reduces round trips and removes outdated algorithms.==
    
- OCSP and OCSP stapling for revocation; CRLs are older.
    
- SNI (Server Name Indication) allows multiple hostnames on one IP with TLS.
    

---

## 5) [[DNS]] — recursive vs authoritative, caching, UDP vs TCP

### What DNS does

- Translates human names (e.g., `google.com`) to IP addresses.
    

### Resolver types

- **Recursive resolver** (often ISP or cloud provider): performs the work of querying the DNS hierarchy on behalf of the client and returns final answer.
    
- **Authoritative name server**: holds the definitive records for a domain (e.g., the DNS server that knows `example.com` records).
    

### Resolution flow (simplified)

1. Client asks recursive resolver: “what is A record for example.com?”
    
2. Resolver asks root nameserver → gets referral to `.com` TLD server.
    
3. Resolver asks `.com` TLD → gets referral to `example.com` authoritative server.
    
4. Resolver asks authoritative → gets IP, returns to client.
    

### Iterative vs recursive queries

- **Iterative**: DNS server returns best answer or referral; client follows referrals (rare in modern consumer setups — recursive resolvers do recursion).
    
- **Recursive**: recursive resolver does the entire chain and returns a single final answer to client.
    

### Caching

- DNS records have TTL. Resolvers and OS/browsers cache responses to reduce lookup overhead. TTLs control cache duration. Negative caching exists for NXDOMAIN.
    

### UDP by default

- DNS typically uses **UDP port 53** because queries and responses are usually small (fast, no handshake). UDP is low overhead and works because queries are small.
    
- **When TCP is used**:
    
    - If response is truncated (TC bit set) indicating it's too big for UDP.
        
    - Zone transfers (AXFR) between authoritative servers use TCP.
        
    - DNS over TLS (DoT) uses TCP/TLS; DNS over HTTPS (DoH) uses HTTPS over TCP or HTTP/3.
        

### Modern extensions

- **EDNS(0)** allows larger UDP payloads, reducing need to fall back to TCP.
    
- **DoH/DoT** add privacy (encrypted DNS queries).
    

---

# Interview-style short answers (ready to speak)

### Q: “Explain the difference between TCP and UDP with examples.”

Short answer you can memorize:

- **TCP** is connection-oriented and reliable; it establishes a session (three-way handshake), guarantees ordered delivery, retransmits lost packets, and does flow/congestion control. Use TCP for web (HTTP), file transfer, email, and anything that requires correctness.
    
- **UDP** is connectionless and minimal; it sends independent datagrams with no guarantee of delivery or order. Use UDP for DNS (fast queries), real-time media (video/voice), gaming, and protocols that implement their own reliability (QUIC).
    

If asked to expand, describe handshake, state, and use-case rationale (latency vs reliability).

---

### Q: “Why does HTTP/3 use QUIC?”

Memorized answer: