
[[Linux FS]]
[[File descriptors & Buffers]]

### SIGTERM VS SIGKILL
`SIGTERM` and `SIGKILL` are both Unix/Linux signals used to stop processes, but they behave very differently.

---

## SIGTERM (Signal 15)

**Graceful termination**

- This is the **default signal** sent by the `kill` command.
    
- The process **can catch, handle, or ignore** it.
    
- It allows the program to:
    
    - Clean up resources
        
    - Save files or state
        
    - Close network connections
        
- Well-behaved programs are expected to exit when they receive it.
    

**Example**

`kill <pid> # or kill -TERM <pid>`

**Use when:**  
You want a process to shut down cleanly and safely.

---

## SIGKILL (Signal 9)

**Forceful termination**

- The process **cannot catch, block, or ignore** this signal.
    
- The OS immediately stops the process.
    
- No cleanup code runs.
    
- Can leave:
    
    - Temporary files
        
    - Corrupted data
        
    - Locked resources
        

**Example**

`kill -9 <pid> # or kill -KILL <pid>`

**Use when:**  
The process is frozen, misbehaving, or ignoring `SIGTERM`.

---

## Key Differences at a Glance

|Feature|SIGTERM|SIGKILL|
|---|---|---|
|Signal number|15|9|
|Can be handled|✅ Yes|❌ No|
|Can be ignored|✅ Yes|❌ No|
|Allows cleanup|✅ Yes|❌ No|
|Immediate stop|❌ Not guaranteed|✅ Yes|

---

## Best Practice

1. **Try `SIGTERM` first**
    
2. **Use `SIGKILL` only as a last resort**
    

This is why system tools (like service managers) usually send `SIGTERM` first and only escalate to `SIGKILL` if needed.

### Here-Dcoument (<<) in bash
A **here-document** (often called **heredoc**) in shell scripting is a way to pass **multi-line input** to a command directly within a script or the command line, instead of from a file or standard input typed interactively.

---

## What a Here-Document Is

A here-document lets you embed text **inline in a script** and feed it to a command’s **standard input (stdin)**.

Think of it as saying:

> “Use _this block of text right here_ as your input.”

---

## How It Is Denoted (Syntax)

```sh
cat <<EOF
Hello
This is a here-document
EOF

output:
Hello
This is a here-document

name="Alex"

cat <<EOF
Hello $name
Today is $(date)
EOF
```

