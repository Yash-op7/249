- see [[useful shell commands]]
## What is `sed`?

`sed` = **s**tream **ed**itor

It:

- Reads text **line by line**
- Applies **rules (commands)** to each line
- Outputs the transformed text (or edits the file)

Think of it as:

> **“find → transform → output” for text streams**

It’s especially powerful because it works:

- In pipelines (`grep | sed | awk`)
    
- On files
    
- Without opening an editor
    

![[Screenshot 2026-01-19 at 6.26.25 PM.png]]

![[Pasted image 20260119182658.png]]
![[Pasted image 20260119182721.png]]

---

## The core mental model

Most `sed` usage revolves around **substitution**:

`sed 's/OLD/NEW/'`

Meaning:

> Replace `OLD` with `NEW` (once per line)

Example:

`echo "hello world" | sed 's/world/sed/'`

Output:

`hello sed`

---

## Anatomy of a sed command

Let’s break down this one:

`sed 's/^function /export function /'`

### 1️⃣ `s` — substitute

This tells `sed` you’re doing a **search & replace**.

### 2️⃣ `/^function /` — the pattern

This is a **regular expression**.

- `^` → start of line
    
- `function` → literal text
    
- space → literal space
    

So:

> Match lines that **start with** `function`

### 3️⃣ `/export function /` — replacement

What the match should become.

---

## Why `^` matters (very important)

Without it:

`sed 's/function/export function/'`

This would turn:

`export function foo()`

into:

`export export function foo()`

Using `^function` ensures:

- Only **non-exported**
    
- Top-level function declarations
    
- At the beginning of the line
    

This is exactly what you want as a TypeScript developer.

---

## Editing files vs previewing output

### Preview (safe)

`sed 's/^function /export function /' file.ts`

Just prints modified text.

---

### Edit in place (dangerous if you’re careless)

#### macOS (BSD sed)

`sed -i '' 's/^function /export function /' file.ts`

#### Linux (GNU sed)

`sed -i 's/^function /export function /' file.ts`

**Why the empty `''` on macOS?**

- BSD `sed` _requires_ a backup suffix
    
- `''` means “no backup file”
    

---

## Very useful sed flags

### Replace all matches on a line

`sed 's/foo/bar/g'`

`g` = global

---

### Only print changed lines

`sed -n 's/foo/bar/p'`

Useful for dry runs.

---

### Apply only to matching lines

`sed '/export function/ s/^/\/\/ /'`

> Comment out only exported functions

---

## Sed + grep = superpowers

Your earlier pipeline was perfect:

`grep "function" file.ts | grep -v "export function"`

Now adding `sed`:

`grep "function" file.ts \ | grep -v "export function" \ | sed 's/^function /export function /'`

This is the Unix philosophy:

> Small tools, composed well

---

## Common developer use cases (real-world)

### 1️⃣ Mass refactors

`sed -i '' 's/var /let /g' *.ts`

### 2️⃣ Fix imports

`sed -i '' 's/from "..\/utils"/from "@\/utils"/g' **/*.ts`

### 3️⃣ Add exports

`sed -i '' 's/^const /export const /' *.ts`

### 4️⃣ Strip console logs

`sed -i '' '/console\.log/d' *.ts`

### 5️⃣ Update environment variables

`sed -i '' 's/API_URL=.*/API_URL=https:\/\/prod.example.com/' .env`

---

## Sed vs other tools

|Tool|Best for|
|---|---|
|`grep`|Finding lines|
|`sed`|Simple transforms|
|`awk`|Structured data / columns|
|`perl`|Complex logic|
|Editor (VSCode)|Interactive refactors|

Rule of thumb:

- **One-line changes across many files → `sed`**
    
- **Complex syntax-aware changes → editor or codemod**
    

---

## Your exact command, fully decoded

`sed -i '' 's/^function /export function /' src/components/feature/Features/helper.ts`

Reads as:

> “Edit this file in place, and for every line that starts with `function` , replace it with `export function` .”

That’s a **clean, professional-grade refactor**.

---

## Final advice (this matters)

Before destructive `sed`:

`git status git diff`

After:

`git diff`

If you want next steps, I can:

- Teach `awk` next (great follow-up to `sed`)
    
- Show how to write **safe multi-file refactors**
    
- Compare `sed` vs `ripgrep + perl`
    
- Give you a “developer cheat sheet” for Unix text tools
    

Just say the word 🚀

![[Pasted image 20260119182830.png]]
![[Pasted image 20260119182843.png]]