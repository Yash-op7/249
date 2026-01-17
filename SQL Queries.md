# **Topic 2: SQL Queries (Rewritten – Clear + MCQ-focused)**

---

## **1. SELECT**

1. **Used to retrieve data** from one or more tables.
    
2. **Execution order (important)**:
    
    - FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY
        
3. **SELECT ***:
    
    - Fetches all columns (inefficient but valid).
        
4. **WHERE**:
    
    - Filters individual rows _before grouping_.
        
5. **DISTINCT**:
    
    - Removes duplicate result rows.
        
6. **ORDER BY**:
    
    - Sorts final output (ASC default).
        
7. **Aliases (AS)**:
    
    - Temporary names for columns/tables.
        
8. **Expressions allowed**:
    
    - salary * 12, marks + 5.
        
9. **Multiple tables**:
    
    - Used with JOIN.
        
10. **MCQ trap**:
    

- WHERE cannot use aggregate functions.
    

---

## **2. VIEW**

1. A **virtual table** created using a SELECT query.
    
2. **Does not store data**, only query definition.
    
3. Used to:
    
    - Hide complexity
        
    - Restrict access to data
        
4. **Changes in base table**:
    
    - Reflected automatically in view.
        
5. **Simple views**:
    
    - Can be updated.
        
6. **Complex views**:
    
    - Usually not updatable.
        
7. **Materialized view**:
    
    - Stores actual data (faster read).
        
8. **Security**:
    
    - Users can access view, not table.
        
9. **Storage**:
    
    - Less than table.
        
10. **MCQ trap**:
    

- View ≠ copy of table.
    

---

## **3. DELETE vs TRUNCATE**

### **DELETE**

1. Removes rows **one by one**.
    
2. **WHERE clause allowed**.
    
3. Can be **rolled back**.
    
4. Fires **triggers**.
    
5. Slower for large tables.
    

### **TRUNCATE**

6. Removes **all rows at once**.
    
7. **No WHERE clause**.
    
8. **Cannot rollback**.
    
9. Faster, uses minimal logging.
    
10. **MCQ favorite**:
    

- TRUNCATE is DDL, DELETE is DML.
    

---

## **4. UPDATE**

1. Modifies **existing rows**.
    
2. **SET** defines new values.
    
3. **WHERE controls scope**.
    
4. Without WHERE → updates all rows.
    
5. Can use **subqueries**.
    
6. Affects table data, not structure.
    
7. Triggers executed.
    
8. Can be rolled back.
    
9. Aggregate functions not allowed directly.
    
10. **MCQ trap**:
    
    - UPDATE cannot add new rows.
        

---

## **5. ALTER**

1. Used to **change table structure**.
    
2. Can:
    
    - ADD column
        
    - DROP column
        
    - MODIFY datatype
        
3. Does not modify individual rows.
    
4. Changes are **permanent**.
    
5. No rollback.
    
6. Data loss possible.
    
7. Affects metadata.
    
8. Slower on large tables.
    
9. ALTER ≠ UPDATE.
    
10. **MCQ favorite**:
    
    - ALTER is DDL.
        

---

## **6. INNER JOIN**

1. Returns rows **only when match exists**.
    
2. Matching based on join condition.
    
3. Non-matching rows are discarded.
    
4. Most commonly used join.
    
5. Equivalent to **intersection conceptually**.
    
6. JOIN condition written using ON.
    
7. Multiple joins allowed.
    
8. Column ambiguity resolved using table alias.
    
9. Produces fewer rows than outer joins.
    
10. **MCQ trap**:
    
    - INNER JOIN ≠ FULL JOIN.
        

---

## **7. OUTER JOINS**

### **LEFT OUTER JOIN**

1. All rows from left table.
    
2. Matching rows from right.
    
3. NULLs where no match.
    

### **RIGHT OUTER JOIN**

4. All rows from right table.
    

### **FULL OUTER JOIN**

5. All rows from both tables.
    
6. NULLs for unmatched rows.
    
7. Preserves non-matching rows.
    
8. FULL JOIN not supported in all DBs.
    
9. More rows than INNER JOIN.
    
10. **MCQ favorite**:
    
    - Identify preserved table.
        

---

## **8. GROUP BY**

1. Groups rows with same values.
    
2. Used with aggregate functions.
    
3. Each group produces one row.
    
4. Columns in SELECT must be:
    
    - Aggregated OR in GROUP BY.
        
5. WHERE filters rows _before grouping_.
    
6. HAVING filters _after grouping_.
    
7. GROUP BY reduces number of rows.
    
8. Common with COUNT, SUM.
    
9. Cannot group by aggregate result.
    
10. **MCQ trap**:
    
    - WHERE ≠ HAVING.
        

---

## **9. HAVING**

1. Filters **groups**, not rows.
    
2. Used with GROUP BY.
    
3. Can use aggregate functions.
    
4. Executed after GROUP BY.
    
5. Replaces WHERE for aggregates.
    
6. Without GROUP BY → works like WHERE.
    
7. Often misused in exams.
    
8. Slower than WHERE.
    
9. Used for group conditions.
    
10. **MCQ favorite**:
    
    - Aggregate → HAVING.
        

---

## **10. Aggregate Functions**

1. Operate on **multiple rows**.
    
2. COUNT(*) counts all rows.
    
3. COUNT(column) ignores NULLs.
    
4. SUM works on numeric data.
    
5. AVG = SUM / COUNT (non-null).
    
6. MIN / MAX find extremes.
    
7. Ignore NULL values.
    
8. Used with GROUP BY.
    
9. Nested aggregates not allowed.
    
10. **MCQ trap**:
    
    - COUNT(column) ≠ COUNT(*).
        

---

## **11. UNION vs UNION ALL**

### **UNION**

1. Combines results vertically.
    
2. Removes duplicates.
    
3. Slower due to sorting.
    

### **UNION ALL**

4. Keeps duplicates.
    
5. Faster.
    
6. Same number of columns required.
    
7. Compatible datatypes required.
    
8. ORDER BY only at end.
    
9. UNION ≠ JOIN.
    
10. **MCQ favorite**:
    
    - UNION ALL is faster.
        

---

## **12. INTERSECT**

1. Returns common rows only.
    
2. Set-based operation.
    
3. Removes duplicates.
    
4. Requires column compatibility.
    
5. Conceptually like INNER JOIN.
    
6. Vertical operation.
    
7. Order not preserved.
    
8. Not available in all DBs.
    
9. Rarely used.
    
10. **MCQ trap**:
    
    - INTERSECT ≠ INNER JOIN.
        

---

## **13. EXCEPT**

1. Returns rows in first query but not second.
    
2. Direction matters.
    
3. Removes duplicates.
    
4. Set-based.
    
5. Vertical operation.
    
6. Not supported in MySQL.
    
7. Alternative: NOT IN.
    
8. EXCEPT ≠ DELETE.
    
9. Column compatibility required.
    
10. **MCQ favorite**:
    
    - First – Second.
        

---

## **14. IN vs EXISTS**

### **IN**

1. Compares value list.
    
2. Subquery executed fully.
    
3. Sensitive to NULLs.
    
4. Simpler syntax.
    
5. Slower for large data.
    

### **EXISTS**

6. Checks existence only.
    
7. Stops on first match.
    
8. NULL-safe.
    
9. Faster for large datasets.
    
10. **MCQ favorite**:
    
    - EXISTS better for correlated queries.
        

---

## **15. Nested Queries**

1. Query inside another query.
    
2. Used in SELECT, WHERE, HAVING.
    
3. Non-correlated runs once.
    
4. Correlated runs per row.
    
5. Scalar subquery → single value.
    
6. Multi-row → IN.
    
7. EXISTS → correlated queries.
    
8. Can affect performance.
    
9. Increases query expressiveness.
    
10. **MCQ trap**:
    
    - Correlated depends on outer query.
        

---