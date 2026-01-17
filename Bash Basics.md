# **Topic 9: Shell Programming**

---

## **1. Shell Scripting Basics**

1. **Shell**:
    
    - Command-line interpreter.
        
2. **Shell script**:
    
    - File containing shell commands.
        
3. **Script extension**:
    
    - `.sh`
        
4. **Shebang**:
    
    - `#!/bin/bash`
        
5. **Execution**:
    
    - `./script.sh`
        
6. **Permissions**:
    
    - `chmod +x script.sh`
        
7. **Sequential execution**.
    
8. **Used for automation**.
    
9. **Interpreted, not compiled**.
    
10. **MCQ favorite**:
    
    - Shell ≠ kernel.
        

---

## **2. Shell Variables**

1. No datatype declaration.
    
2. Assigned without spaces:
    
    - `x=10`
        
3. Accessed using `$x`.
    
4. Variables are case-sensitive.
    
5. Default scope: global.
    
6. Local variables inside functions.
    
7. `readonly` makes variable constant.
    
8. `unset` removes variable.
    
9. Environment variables shared with child processes.
    
10. **MCQ trap**:
    
    - Space in assignment causes error.
        

---

## **3. Shell Script Arguments**

1. Arguments passed from command line.
    
2. `$0`:
    
    - Script name.
        
3. `$1, $2, ...`:
    
    - Positional arguments.
        
4. `$#`:
    
    - Number of arguments.
        
5. `$@`:
    
    - All arguments separately.
        
6. `$*`:
    
    - All arguments as one string.
        
7. Used for dynamic input.
    
8. Argument order matters.
    
9. Used in automation.
    
10. **MCQ favorite**:
    
    - `$#` gives argument count.
        

---

## **4. If Statement**

1. Used for decision making.
    
2. Syntax uses `if`, `then`, `fi`.
    
3. Conditions tested using `test` or `[ ]`.
    
4. `-eq, -ne, -gt, -lt` for numbers.
    
5. `=` for string comparison.
    
6. `-f` checks file exists.
    
7. `-d` checks directory.
    
8. Supports `elif`.
    
9. Indentation improves readability.
    
10. **MCQ trap**:
    
    - Spaces required inside `[ ]`.
        

---

## **5. Loops**

### **for loop**

1. Used for fixed iterations.
    
2. Iterates over list or range.
    
3. Syntax: `for var in list`.
    

### **while loop**

4. Runs while condition true.
    
5. Entry-controlled loop.
    

### **until loop**

6. Runs until condition becomes true.
    
7. `break` exits loop.
    
8. `continue` skips iteration.
    
9. Nested loops allowed.
    
10. **MCQ favorite**:
    
    - until loop runs while condition is false.
        

---

## **6. Return**

1. Used inside functions.
    
2. Returns **exit status**, not value.
    
3. Range: 0–255.
    
4. `0` means success.
    
5. Non-zero means failure.
    
6. Value accessed using `$?`.
    
7. Function output via echo.
    
8. Return ≠ exit.
    
9. exit terminates script.
    
10. **MCQ trap**:
    
    - return does not return data.
        

---

## **7. Basic UNIX Commands** ⭐⭐⭐

---

### **File & Directory Commands**

1. `ls`:
    
    - List files.
        
2. `pwd`:
    
    - Present working directory.
        
3. `cd`:
    
    - Change directory.
        
4. `mkdir`:
    
    - Create directory.
        
5. `rmdir`:
    
    - Remove empty directory.
        
6. `rm`:
    
    - Delete files/directories.
        
7. `cp`:
    
    - Copy files.
        
8. `mv`:
    
    - Move / rename files.
        
9. `touch`:
    
    - Create empty file.
        
10. **MCQ favorite**:
    
    - rm -r deletes directories.
        

---

### **File Viewing & Text Commands**

1. `cat`:
    
    - Display file content.
        
2. `more`:
    
    - Paged output.
        
3. `less`:
    
    - Advanced pager.
        
4. `head`:
    
    - First lines.
        
5. `tail`:
    
    - Last lines.
        
6. `wc`:
    
    - Word count.
        
7. `sort`:
    
    - Sort lines.
        
8. `grep`:
    
    - Search pattern.
        
9. `cut`:
    
    - Extract columns.
        
10. **MCQ trap**:
    
    - grep searches text, not files only.
        

---

### **Permissions Commands**

1. `chmod`:
    
    - Change permissions.
        
2. `chown`:
    
    - Change owner.
        
3. `rwx`:
    
    - Read, write, execute.
        
4. Numeric permissions:
    
    - 4 (r), 2 (w), 1 (x).
        
5. `chmod 755` meaning.
    
6. Owner, group, others.
    
7. Execute permission required to run script.
    
8. `ls -l` shows permissions.
    
9. Root has full access.
    
10. **MCQ favorite**:
    
    - 755 = rwx r-x r-x.
        

---

## **8. Input / Output Redirection**

1. `>`:
    
    - Output redirection.
        
2. `>>`:
    
    - Append output.
        
3. `<`:
    
    - Input redirection.
        
4. `|`:
    
    - Pipe output to another command.
        
5. Standard streams:
    
    - stdin, stdout, stderr.
        
6. Pipe connects commands.
    
7. Used for command chaining.
    
8. Common in scripting.
    
9. Improves automation.
    
10. **MCQ trap**:
    
    - Pipe ≠ redirection.
        

---

✅ **Topic 9 COMPLETE**

---

### 🎯 You have now covered **ALL syllabus topics**.
