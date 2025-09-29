Related pages:
- [[inode]]
## 🔐 File Permissions: What Does `0644` Mean?

In Linux, file permissions are stored as a **3-digit octal (base-8)** number. Example:
```sh
Owner: 6 → read + write
Group: 4 → read
Others: 4 → read
```

Octal values:

- `4 or 100` = read
    
- `2 or 010` = write
    
- `1 or 001` = execute

So,

```sh
0644 octal → binary → rw-r--r--

Octal digits:    6     4     4
Binary:        110   100   100
                ↑     ↑     ↑
              Owner Group Others

Owner (6 = 110) = read + write
Group (4 = 100) = read only
Others (4 = 100) = read only
```

## 2. What are Owner, Group, and Others?

### Owner:

- The **user** who owns the file.
- Usually the person who created the file.
### Group:

- A **set of users** grouped together.
- Groups allow managing permissions for multiple users collectively.
- Example: a `developers` group with many users.

### Others:

- Everyone **not the owner and not in the group**.
- Public permissions.
## 📂 File Types

In UNIX, every file has a **type**:

| Type         | Code/Macro | Meaning           | Example                         |
| ------------ | ---------- | ----------------- | ------------------------------- |
| Regular file | `S_IFREG`  | Regular File      | Normal file                     |
| Directory    | `S_IFDIR`  | DIRectory         | A folder                        |
| Symlink      | `S_IFLNK`  | Symbolic Link     | A symbolic link                 |
| Pipe         | `S_IFIFO`  | FIFO (named pipe) | For inter-process communication |
| Socket       | `S_IFSOCK` | Socket            | Network communication           |
| Block device | `S_IFBLK`  | Block device      | e.g. hard drive                 |
| Char device  | `S_IFCHR`  | Character device  | e.g. keyboard                   |

So `S_IFREG` just tells us this is an ordinary file, not a directory or device.

