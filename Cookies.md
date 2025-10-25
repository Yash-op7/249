## 🔹 2. What are Cookies in Client–Server Context?

In **HTTP (stateless)**, the server forgets you after each request.

- ⭐ **Cookies** are little key–value pairs stored in the client’s browser, automatically attached to future requests to the same domain.
    
- Example:
    
    - First login: server says → `Set-Cookie: session_id=abc123`
        
    - Browser stores this.
        
    - Next request: browser automatically adds → `Cookie: session_id=abc123`.
        

> ⭐ 👉 Cookies provide a way to **re-identify the client** across otherwise independent HTTP requests.