The **kernel** is the **core part of the [[OS]]**. It acts as a **bridge between applications and the physical hardware**.

##### Related documents:
- [[File descriptors & Buffers]]
- [[Buffer]]
- 
### 🎯 Responsibilities of the Kernel:

|Responsibility|Description|
|---|---|
|🧠 **Memory Management**|Controls and allocates RAM between applications|
|⚙️ **Process Management**|Starts, schedules, and stops programs (called processes)|
|💾 **File System Management**|Manages files and directories on storage|
|🖥️ **Device Management**|Talks to hardware (CPU, disk, network, etc.) via drivers|
|🔒 **Security & Permissions**|Controls user access to resources|

---
## 🧩 **How the Kernel Works**

Let’s walk through an example:

Say you run a Python app:

```bash
python myapp.py
```

Here’s what happens step by step:

1. The shell (e.g., bash) tells the **OS** to run a program.
    
2. The **kernel**:
    
    - Loads the Python interpreter into memory
        
    - Allocates memory
        
    - Gives it a **process ID**
        
    - Starts the program on a CPU core
        
    - Manages input/output (I/O), like reading a file or network
        

All of this is done **by the kernel**. Applications don’t talk to hardware directly — they **ask the kernel to do it** on their behalf.

---
## 🛠️ Two Key Modes: User Space vs Kernel Space

- **User Space**: Where applications like browsers, editors, games run.
    
- **Kernel Space**: Where the kernel runs. Has full access to hardware.
    

> Applications cannot directly access hardware — they must go through the **kernel** using **system calls**.

This protects the system from bugs or malicious code.

---

## 📞 What Are System Calls?

> A **system call** is how a program requests a service from the kernel.

Example: `read()`, `write()`, `open()`, `fork()` — all system calls.

When your program needs to:

- Open a file
    
- Allocate memory
    
- Send data over the network  
    It makes a **system call**, and the **kernel handles it** securely and efficiently.
    Each **system call** is a **separate request** to the kernel, and the kernel only grants access or performs the service **for that specific call**.

#### 🔁 System Calls Are One-Off, Not Persistent Access

System calls are **discrete**, one-time interactions with the kernel. Here's how it works:

1. Your app wants to read a file.
    
2. It issues a system call: `read(fd, buffer, size)`
    
3. The kernel checks permissions, reads the data, fills the buffer.
    
4. The call returns to the app with the result.
    

👉 If your app wants to read more data:

- It must **call `read()` again**.
    
- It does **not retain some persistent "open channel"** to the hardware outside of what the kernel explicitly allows through file descriptors. Example:
```cpp
int fd = open("file.txt", O_RDONLY);   // System call #1
read(fd, buffer, 100);                 // System call #2
read(fd, buffer, 100);                 // System call #3
close(fd);                             // System call #4
```

---

## 🧱 Kernel and Containers

Here’s why the kernel matters in the context of **Docker and containers**:

### ✅ Containers share the host OS kernel

Unlike Virtual Machines (which each have their own OS), **containers share the host’s kernel** — but run in **isolated environments**.

That’s made possible by two Linux kernel features:

### 1. **Namespaces** (Isolation)

Namespaces create isolated views of system resources, such as:

- Process IDs (`pid`)
    
- Network interfaces (`net`)
    
- Filesystems (`mnt`)
    
- Hostnames (`uts`)
    

Each container thinks it’s the only thing on the system — but it’s just a namespace.

### 2. **Control Groups (cgroups)** (Resource limits)

Cgroups limit how much CPU, memory, or disk I/O a container can use.

So even though all containers share the same kernel:

- They can’t see each other (thanks to **namespaces**)
    
- They can’t hog resources (thanks to **cgroups**)
    

---

## 🧠 Summary: OS Kernel in a Nutshell

|Concept|What it does|
|---|---|
|**Kernel**|Core of the OS that manages hardware and system resources|
|**System Call**|How apps talk to the kernel|
|**User Space**|Where applications run|
|**Kernel Space**|Where the kernel operates|
|**Namespaces**|Container isolation mechanism|
|**Cgroups**|Container resource control|
|**Containers**|Use these kernel features to be lightweight and isolated|

---

## 🧪 Want a Visualization?

Think of your computer like an apartment building:

| Layer         | Analogy             | Role                                                                                    |
| ------------- | ------------------- | --------------------------------------------------------------------------------------- |
| Hardware      | The building itself | Physical CPU, RAM, disk                                                                 |
| Kernel        | Building manager    | Gives tenants controlled access to water, power, elevators                              |
| User programs | Tenants             | Run inside their own apartments, query manager for one-time action via kernel mediation |
| Containers    | Rooms/apartments    | Isolated, but share same water/power (kernel)                                           |

---
