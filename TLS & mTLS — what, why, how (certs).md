see backlink

## TLS Handshake Steps

1. **Client Hello**
    
    - Client sends supported TLS versions, cipher suites, and a random value.
        
2. **Server Hello**
    
    - Server chooses TLS version & cipher suite and sends its random value.
        
3. **Server Authentication**
    
    - Server sends its **certificate** (proves its identity).
        
4. **Key Exchange**
    
    - Client and server securely exchange info to create a **shared secret**  
        (e.g., using Diffie-Hellman).
        
5. **Session Key Generation**
    
    - Both sides derive **symmetric session keys** from the shared secret.
        
6. **Handshake Finished**
    
    - Both confirm the handshake is complete.
        
7. **Secure Communication**
    
    - Encrypted data transfer begins using symmetric encryption.