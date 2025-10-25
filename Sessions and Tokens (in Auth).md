## 🔹 3. Sessions and Tokens (Different Auth Models)

**Sessions (Server-side stateful auth)**

- The server stores session data in memory or a DB.
    
- The client holds only a **session ID** (in a cookie).
    
- **Pros: easy to invalidate sessions.**
    
- **Cons: doesn’t scale well without sticky load balancing or centralized session store.**
    

**Tokens (Stateless auth)**

- Instead of the server remembering, it gives the client a **self-contained token** (often a JWT).
    
- Client sends token with each request.
    
- Server only validates the token (e.g., using a secret or public key).
    
- **Pros: scales easily, server doesn’t store per-client data.**
    
- **Cons: harder to revoke early.**