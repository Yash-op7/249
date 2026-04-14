### SSL Certificates

Used for:

`HTTPS encryption`

Without SSL:

- Passwords visible
    
- Tokens leak
    
- MITM attacks
    

Security Monkey checks:

- Certificate expired?
    
- Expiring soon?
    
- Wrong domain?
    
- Weak encryption?
    

If expired → users see security warnings → production outage.