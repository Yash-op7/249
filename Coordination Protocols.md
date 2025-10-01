The goal is simple:

> **Make sure all replicas either _all commit_ or _all abort_ a transaction. Never some commit and some don’t.**

Why is this hard?

- Machines crash
- Network messages get lost
- Some nodes might be slow or down

This is where **coordination protocols** like 2PC/3PC come in.