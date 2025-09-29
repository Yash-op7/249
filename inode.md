## 4. What is an Inode?

- **Inode (index node)** is a data structure in a filesystem.
    
- Stores **metadata** about a file but **not the file name or contents**.
    
- Metadata includes:
    - File size
    - Permissions
    - Owner user ID (UID)
    - Group ID (GID)
    - Timestamps (created, modified)
    - Pointers to data blocks on disk

### Inode summary:

|Item|Meaning|
|---|---|
|Metadata|Permissions, owner, size, timestamps|
|Pointers|Locations of actual file data on disk|
|Unique ID|Every file has a unique inode number|

**File names live in directory entries**, which point to inodes.