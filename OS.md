The **Operating System (OS)** is software that manages **hardware** (CPU, memory, storage, etc.) and provides a platform for **applications** to run.

Related pages:
- [[Linux File System]]
- [[virtual memory management**]]
- [[virtual machines]]
- [[Proccess & threads]]
- [[Multithreading]]

### Basic Concepts
### 🔧 Who makes the system call?

> **Q:** Is the system call made by the user program or by the OS?

➡️ **The system call is _initiated_ by the user program**, but the actual **implementation is in the kernel (OS)**.

Here's how it works:

1. Your program says: "I want to read a file."
    
2. It uses a system call like `read()` — this is a **special function that jumps into the kernel** using a CPU instruction (`syscall`, `int 0x80`, etc.).
    
3. The **OS takes over**, performs the actual read (e.g., from disk or buffer), and returns control and data back to your program.
## 📝 fstat(): Where Does File's metadata (size, permissions, etc) Come From?

> **Q:** How do we know the file is 33 bytes in size before calling `read()`?

We don’t know it by magic — we get it **from the filesystem**, using `fstat()`.

```c
struct stat info;
fstat(fd, &info);
printf("Size: %ld\n", info.st_size);
```

- `fstat()` fills in the `struct stat`, which contains file metadata (size, permissions, type, etc.).
- The kernel retrieves this data from the **inode** of the file (metadata stored by the filesystem).

# 💡 How **$ cat a.txt** works:

Here’s a simple C program that does the same as **$ cat date.txt**:

```c
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>

int main() {
    char buffer[4096];
    int fd = open("date.txt", O_RDONLY); // open file
    if (fd < 0) {
        perror("open failed");
        return 1;
    }

    int bytesRead;
    while ((bytesRead = read(fd, buffer, sizeof(buffer))) > 0) {
        write(1, buffer, bytesRead); // write to stdout
    }

    close(fd);
    return 0;
}
```


## 📦 In the above code why 4096, Buffer Size in C

> **Q:** How do we choose the buffer size if we don’t know the file size?

Exactly — most of the time, you **don’t know the file size ahead of time**.

So you choose a **reasonable buffer size**, usually:

- 4096 bytes = 4 KB (common page size on many systems)
    
- Or 8192, or more if performance matters.
    

```c
char buffer[4096];
```

Then you **loop until `read()` returns 0**, meaning **End of File (EOF)**:

```c
int bytesRead;
while ((bytesRead = read(fd, buffer, sizeof(buffer))) > 0) {
    write(1, buffer, bytesRead);
}
```

So even if the file is 100 MB — the program will just keep reading in 4 KB chunks until finished.

### ❓ Is buffer 4096 bytes or bits?

- `char buffer[4096]` = **4096 bytes** = 4 KB
    
- In C, `char` is always 1 byte.

## 📊 Final Analogy

Think of reading a file like scooping water from a bucket:

- The file is the **bucket**
    
- Your program is **scooping**
    
- The buffer is your **cup** (you decide how big it is — 4KB, 8KB, etc.)
    
- Each `read()` fills the cup
    
- You `write()` it to the screen (or anywhere else)
    
- When the cup comes up empty (`read() == 0`), you're done.
---

## 🔍 What Does `read()` Actually Do Internally?

> **Q:** How does `read()` work step-by-step?

Let’s go through the full flow from user program to the OS:

```c
int fd = open("date.txt", O_RDONLY);   // Step 1
char buffer[4096];                     // Step 2
int n = read(fd, buffer, 4096);        // Step 3
write(1, buffer, n);                   // Step 4
close(fd);                             // Step 5
```

### What Happens Internally:

#### 🔹 `open("date.txt", O_RDONLY)`:

1. Kernel looks up the file in the filesystem.
2. If it exists, it loads metadata ([[inode]]) into memory.
3. Allocates a file descriptor (e.g., `3`).
4. Stores the file's state in a kernel structure (`struct file`). (See below for struct definition)
5. Returns file descriptor `3` to the user program.

#### 🔹 `fstat(3, ...)`:

1. Program asks kernel: "Tell me about fd = 3."
2. Kernel retrieves metadata from inode: size, mode, type.
3. Fills in the `stat` struct. (See below for struct definition)

#### 🔹 `read(3, buffer, 4096)`:

1. Program provides:
    - File descriptor: `3`
    - Memory buffer: address of `buffer`
    - Max bytes to read: `4096`
2. Kernel checks if fd 3 is valid and points to a readable file.
3. Reads up to 4096 bytes from disk (or page cache).
4. Copies data into the user’s `buffer`.
5. Returns number of bytes read (e.g., `33`).

#### 🔹 `write(1, buffer, 33)`:

1. File descriptor `1` = stdout.
2. Kernel writes those 33 bytes to the terminal.

#### 🔹 `close(3)`:

1. Closes the file, cleans up kernel structures.

## 5. What is `struct file`?

In Linux kernel (written in C), `struct file` represents an **open file instance**.

### Responsibilities:

- Represents a file opened by a process.
    
- Keeps track of:
    
    - Current file position (offset for reading/writing)
        
    - Access mode (read/write)
        
    - Pointers to the inode and filesystem operations
        
    - Reference counts (how many processes are using it)
        

### Key members (simplified):

```c
struct file {
    struct inode *f_inode;      // points to inode of the file
    loff_t f_pos;               // current file offset
    const struct file_operations *f_op;  // pointer to file operations
    unsigned int f_flags;       // flags like O_RDONLY, O_WRONLY
    atomic_t f_count;           // reference count
    void *private_data;         // filesystem-specific data
};
```

---

## 6. What is `struct stat`?

Used to hold **file metadata** returned by `stat()` or `fstat()` system calls.

### Contains:

- File type and permissions (`st_mode`)
    
- Owner user ID (`st_uid`)
    
- Group ID (`st_gid`)
    
- File size in bytes (`st_size`)
    
- Timestamps (last access, modification, inode change)
    
- Number of hard links (`st_nlink`)
    

```c
struct stat {
    dev_t     st_dev;     /* Device ID */
    ino_t     st_ino;     /* Inode number */
    mode_t    st_mode;    /* File mode (permissions + type) */
    nlink_t   st_nlink;   /* Number of hard links */
    uid_t     st_uid;     /* Owner user ID */
    gid_t     st_gid;     /* Owner group ID */
    off_t     st_size;    /* File size in bytes */
    time_t    st_atime;   /* Last access time */
    time_t    st_mtime;   /* Last modification time */
    time_t    st_ctime;   /* Last status change time */
};
```

## ⭐⭐ Is the Linux Kernel Written in C?

**Yes**, almost entirely in **C**, with some parts (like boot code and architecture-specific bits) in assembly.

- The kernel uses many C features, including `struct`s.
    
- It carefully manages hardware resources, memory, and processes.

## 8. Kernel Structures in the File Read Process

### Step by step simplified:

1. **User program** calls `open()`.
    
2. Kernel:
    - Allocates `struct file`.
    - Finds inode on disk.
    - Initializes `struct file` with inode, sets offset to 0.
    
3. User program calls `read(fd, buf, count)`.
    
4. Kernel:
    - Checks file descriptor `fd`.
    - Uses `struct file` to find the inode and offset.
    - Reads data from disk or cache (using pages).
    - Copies data to user buffer.
    - Updates file offset.
    
5. User program calls `close(fd)`.
    
6. Kernel frees `struct file` when no longer used.

## 9. What are Pages and Page Size?

- A **page** is a fixed-length block of memory used by the OS to manage memory.
    
- Typical size on x86 systems: **4 KB (4096 bytes)**.
    
- Pages are the **basic unit of [[virtual memory management**]].
    
- When reading a file, data is often read in page-sized chunks and cached in memory for efficiency.