A **phantom read** happens when:

> **A transaction sees new rows appear or disappear when it runs the same query again.**

## Simple Example

1. Transaction **T1** runs:
    
    `SELECT * FROM orders WHERE amount > 100;`
    
    → Gets 5 rows
    
2. Transaction **T2** inserts a new order with `amount = 150` and commits
    
3. Transaction **T1** runs the same query again  
    → Now gets **6 rows**
## Key point

- Happens in lower isolation levels
    
- Prevented by **SERIALIZABLE** isolation level

👉 The extra row is a **phantom**.

A **phantom read cannot occur with only one transaction.**

A phantom read requires:

- **At least two transactions**
    
- One transaction re-running a query
    
- Another transaction **inserting or deleting rows** in between
    

With **only one transaction**, no other transaction can change the data while it’s running, so the result set cannot change.