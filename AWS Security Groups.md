### a) AWS Security Groups

They are **virtual firewalls** for servers.

Rules like:

```sh
Allow:
- Port 443 from 0.0.0.0/0
- Port 22 from company IP

Deny:
- Everything else
```
#### Bad example:

`Allow all ports from 0.0.0.0/0`

This exposes your server to the internet → hackers scan it.

Security Monkey detects:

- SSH open to the world
    
- DB ports public
    
- Too-wide IP ranges
    

Then:

- shuts down instance
    
- alerts service owner

## Side note: Public ports to expose

-> Port 443 and 22 are ==critical TCP ports used for secure network communication==. 
Port 443 is the default for HTTPS (secure web browsing/SSL/TLS), ensuring encrypted data transfer between browsers and servers. 
Port 22 is the default for SSH (Secure Shell), enabling encrypted remote management, file transfers, and secure command execution. 

**Key Differences and Uses**

- **Port 443 (HTTPS):**
    - **Purpose:** Secure web traffic, API services, and data in transit.
    - **Protocol:** SSL/TLS over TCP.
    - **Usage:** Essential for secure online commerce, banking, and data protection.
- **Port 22 (SSH):**
    - **Purpose:** Remote login, command-line access, and secure file transfer (SFTP/SCP).
    - **Protocol:** SSH (Secure Shell).
    - **Usage:** System administration, managing servers, and secure remote operations. 

**Security Considerations**

- **Port 443:** Considered essential to be open to the internet for web servers.
- **Port 22:** Often targeted by brute-force attacks. Best practices include using key-based authentication instead of passwords, changing the default port, or restricting access via IP white-listing.
## 🔐 Port 443 (HTTPS): “Open by Design”

**Why it’s considered essential to be open to the internet**

### Context

Port **443** is what allows **users anywhere on the internet** to securely access a website or API. If you’re running:

- A public website
    
- An online store
    
- A mobile app backend
    
- A public REST API
    

…then **Port 443 must be reachable from the internet**, or no one can connect.

### Why this is relatively safe

- HTTPS uses **TLS encryption**, so data is encrypted end-to-end
    
- Attackers can _see_ the traffic exists, but **cannot read or alter it**
    
- Authentication (cookies, tokens, OAuth, etc.) happens at the **application layer**, not the port itself

### Example

A bank website:

- Opens **Port 443** to everyone
    
- Relies on:
    
    - TLS certificates
        
    - Login systems
        
    - Rate limiting
        
    - Web Application Firewalls (WAFs)
        

Even though the port is open, **access is still controlled**.

### Key idea

> Port 443 is exposed because it _has to be_ — security is enforced **inside the application**, not by hiding the port.

---

## ⚠️ Port 22 (SSH): “Open = Attack Magnet”

**Why it’s dangerous to leave open without restrictions**

### Context

Port **22** provides **direct administrative access** to a server:

- Full command-line control
    
- Ability to read/write files
    
- Ability to install software, create users, or shut the system down
    

That makes it **extremely valuable to attackers**.

### What happens if Port 22 is open to the internet

The moment a server goes online:

- Automated bots start scanning for open SSH ports
    
- They attempt **thousands of logins per hour**
    
- Common usernames tried:
    
    - `root`, `admin`, `ubuntu`, `ec2-user`
        
- Common passwords:
    
    - `password`, `123456`, `admin`, leaked credentials
        

This is called a **brute-force attack**.

### Real-world example

A cloud VM with:

- Port 22 open to `0.0.0.0/0` (everyone)
    
- Password-based SSH enabled
    

Within minutes, logs will show:

`Failed password for root from 185.x.x.x Failed password for admin from 103.x.x.x`

Eventually, a weak password = **server compromise**.

---

## 🛡️ Best Practices Explained (with Examples)

### 1. 🔑 Key-based authentication (instead of passwords)

**Why it helps**

- Uses cryptographic keys (much harder to crack)
    
- Bots can’t guess private keys
    

**Example**

- You generate an SSH key pair:
    
    - Private key → stays on your laptop
        
    - Public key → stored on the server
        
- SSH login works **only** if the key matches
    

Even if attackers try a million passwords — **they get nowhere**.

---

### 2. 🔄 Changing the default port

**Why it helps**

- Reduces noise from automated attacks
    
- Bots mostly scan Port 22 by default
    

**Example**

- Move SSH from `22` → `2222`
    
- Bots keep attacking Port 22
    
- Your server sees far fewer attempts
    

⚠️ Important:

> This is **security by obscurity**, not real protection — it helps, but **should never be the only defense**.

---

### 3. 📍 IP whitelisting (strongest control)

**Why it’s effective**

- SSH is accessible **only from trusted locations**
    
- Everyone else is blocked at the firewall
    

**Example**

- Allow SSH only from:
    
    - Your office IP
        
    - Your VPN IP
        
- Firewall rule:
    
    `Allow TCP 22 from 203.0.113.5 Deny all others`
    

Even if attackers know your credentials:

- They **can’t even reach the SSH service**
    

---

## 🔍 Side-by-side mindset

|Port|Exposure|Risk|Protection Style|
|---|---|---|---|
|443|Public|Low|TLS + app-level security|
|22|Admin access|High|Restrict, hide, harden|

---

## 🧠 Bottom line

- **Port 443** is meant for public consumption — secure it at the application layer.
    
- **Port 22** is a master key to your server — expose it as little as possible.
    
- Treat SSH like a **locked door**, not a welcome mat.
### TL;DR

- 🔐 Both 443 and 22 encrypt traffic
    
- 🧠 Difference is **what you access after auth**
    
- 🌍 HTTPS → app sandbox, public by design
    
- 🧨 SSH → OS-level access, dangerous if exposed
    
- 🔑 Key-based SSH = unguessable, cryptographic proof
## 6️⃣ Key-based authentication — simple explanation

Let’s make this concrete and human.

### Password auth (weak model)

- Server asks: “What’s your password?”
    
- Attacker can guess it
    
- If guessed → access granted
    

---

### Key-based auth (strong model)

Instead of _something you know_, you use _something you have_.

#### Step 1: Generate an SSH key pair

On your laptop, you run:

`ssh-keygen`

This creates:

- 🔐 **Private key** (stays on your laptop)
    
- 🔓 **Public key** (safe to share)
    

Think of it like:

- Public key = **lock**
    
- Private key = **key**
    

---

#### Step 2: Install the public key on the server

You copy the **public key** into:

`~/.ssh/authorized_keys`

This tells the server:

> “Anyone who proves they have the matching private key is allowed in.”

---

#### Step 3: How login actually works

When you SSH:

1. Server sends a random challenge
    
2. Your laptop signs it using your **private key**
    
3. Server verifies the signature using the **public key**
    
4. If it matches → login succeeds
    

⚠️ Important:

- **Private key is never sent**
    
- Nothing to brute-force
    
- Bots can’t guess math
    

---

## 7️⃣ Why keys stop brute-force attacks completely

With keys:

- No password prompt
    
- No guessing
    
- No “try again”
    

An attacker sees:

`Permission denied (publickey)`

They can try forever — **it won’t help**.