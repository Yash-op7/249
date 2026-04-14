[]()[]()## find command

`find . -name "split-examples.js"`
find the location of a file in the current directory, for example:
```sh
/app/dist # find . -name "split-examples.js"
./src/helpers/split-examples.js
```
 `grep -A3` -> display 3 lines of context after each match
### `xargs` for parallel commands
`echo {1..10} | xargs -n1 -P5 -I{} bash -c 'echo "Job {} started"; sleep 3; echo "Job {} finished"`

Delete pods parallely: `gp | awk 'NR > 1 && $1 ~ /worker-/ { print $1 }' | xargs -n1 -P5 kubectl delete pod`


#### . **`grep`**
- See [[grep]]
- **`grep`** is used to search through the contents of files or output for patterns.
    
    **Flags**:
    
    - `-i`: Case-insensitive search.
        
    - `-r`: Recursively search directories.
        
    - `-v`: Invert the match, showing lines that **do not** match the pattern.
        
    - `-n`: Show line numbers.
        
    - `-l`: Show only the filenames containing matches.
    - `-E` — Extended regex (MUST-KNOW)
	    - Enables `|`, `()`, `+`, `?`
	- `-w` — Match whole word
		- `grep -w 'user'` -> Avoids matching username, superuser.
	- `-o` - Show only the matched part
		- `grep -o -E 'AIC-[0-9]+'` (not very usefull)
	- `-c` -> Count matches
		- `grep -c 'ERROR' app.log`
	- `-l` / `-L` — List matching / non-matching files
		- `grep -l 'TODO' *.js     # files WITH TODO`
		- `grep -L 'TODO' *.js     # files WITHOUT TODO`
	- `--include` / `--exclude`
		- `grep -r 'console.log' . --include='*.js'`
	- Context Around Matches: `-A`, `-B`, `-C`: `grep -C 3 'ERROR' app.log`
		- - `-A 3` → 3 lines **After**
		- `-B 3` → 3 lines **Before**
		- `-C 3` → both
    **Example**:
    
```sh
grep -i 'error' /var/log/syslog
grep -r 'timeout' /var/log/
glp | grep -E 'AIC-(5462|5642)' # Extended regex flag
```

## Useful regex patterns
## 🔹 OR

`grep -E 'foo|bar'`

## 🔹 Starts / ends with

```sh
grep '^ERROR' app.log   # starts with
grep 'failed$' app.log # ends with
```

## 🔹 Numbers

`grep -E '[0-9]{4}'`

## 🔹 Word boundaries

`grep -E '\buser\b'`

It matches a position where:

- a **word character** (`[A-Za-z0-9_]`)
    
- meets a **non-word character** (space, dash, dot, start/end of line)
Because `\b` enforces **exact word matching**, not substrings.
---

# 5️⃣ Real-World `grep` Use Cases

## 🔥 Git log filtering (your case)

### Only ticket commits

`git log --oneline | grep -E 'AIC-(5462|5642)'`

### Exclude merges

`git log --oneline | grep -v 'Merge pull request'`

---

## 🔥 Find TODO / FIXME

`grep -R -n 'TODO\|FIXME' .`

Means:

> “Search this directory **and all subdirectories**”

### Difference between `-r` and `-R`

|Flag|Meaning|
|---|---|
|`-r`|Recursive, ignores symlinks|
|`-R`|Recursive, **follows symlinks**

---

## 🔥 Log analysis

### Errors but not warnings

`grep -i 'error' app.log | grep -v 'warning'`

### Count errors per file

`grep -c 'ERROR' *.log`

---

## 🔥 Extract values

`grep -o -E 'userId=[0-9]+' access.log`

### Break it down

#### `-E`: Extended regex

#### `[0-9]+`: One or more digits

#### `-o`: **Output only the matched part**, not the whole line

---

### Example `access.log`

```sh
GET /api?userId=123&token=abc
POST /login userId=456
ERROR userId=789 failed auth
```
### Command

`grep -o -E 'userId=[0-9]+' access.log`

### Output

```sh
userId=123
userId=456
userId=789
```

🔥 This is **data extraction**, not just searching.
---

## 🔥 Find unused code

`grep -R 'oldFunctionName' src/`

---

# 6️⃣ grep vs friends (quick comparison)

|Tool|When to use|
|---|---|
|`grep`|Fast line matching|
|`ripgrep (rg)`|**Best modern replacement**|
|`sed`|Transform text|
|`awk`|Field-based processing|

📌 On macOS, install `ripgrep`:

`brew install ripgrep`

Then:

`rg 'AIC-(5462|5642)'`

(Faster, smarter, ignores `.git` automatically)

---

# 7️⃣ Mental Model (important)

> **`grep` answers one question:**  
> “Does this line contain a match?”

It doesn’t understand:

- words vs fields
    
- git commits
    
- logs vs code
    

That’s your job via regex.

---
## ### 🟡 `sed`

[[sed]] = **Stream editor**

- Modifies text
    
- Replaces, deletes, transforms lines
    

Think:

> “Take text → change it → output new text”

### 🔵 `awk`

**Field-based processor**

- Understands columns
    
- Can do math, conditions, formatting
    

Think:

> “Split lines into fields → operate on them”



## Git tricks

`git log -L 120,140:features/Inquiry\ Creation.feature --oneline`

See file history of one file within line number bounds

