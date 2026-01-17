- See also [[AWS]]

# 1️⃣ What is Amazon FSx for Lustre (in plain terms)

**FSx for Lustre** is a **managed, high-performance, distributed POSIX filesystem**.

Think of it as:

> “A very fast, shared Linux filesystem designed for parallel workloads.”

Every pod/node sees the same filesystem **at the same time**.

```bash/mnt/shared
├── job-123/
├── job-456/
└── cache/
```