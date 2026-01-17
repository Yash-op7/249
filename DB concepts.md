# **Topic 1: Database Concepts**

---

## **Term 1: ER Model (Entity–Relationship Model)**

**MCQ-relevant core points:**

1. **Used at**: **Conceptual design stage** (before relational schema).
    
2. **Entity**: Real-world object → represented by **rectangle**.
    
3. **Attribute**: Property of entity → **oval**.
    
4. **Primary Key**:
    
    - Uniquely identifies an entity.
        
    - Shown as **underlined attribute**.
        
5. **Relationship**:
    
    - Association between entities.
        
    - Represented by **diamond**.
        
6. **Cardinality Ratios**:
    
    - 1:1, 1:N, M:N (**very common MCQ**).
        
7. **Participation**:
    
    - Total (mandatory) vs Partial (optional).
        
8. **Weak Entity**:
    
    - Has **no primary key** of its own.
        
    - Depends on strong entity.
        
9. **Identifying Relationship**:
    
    - Links weak entity to strong entity.
        
10. **ER → Table Mapping**:
    
    - M:N relationships become **separate tables**.
        

**Common traps:**

- ER model is **not logical or physical design**.
    
- Weak entity ≠ entity with NULL key.
    

---

## **Term 2: Relational Model**

1. **Data representation**: Tables (relations).
    
2. **Tuple**:
    
    - A row in a table.
        
3. **Attribute**:
    
    - A column in a table.
        
4. **Domain**:
    
    - Set of valid values for an attribute.
        
5. **Primary Key**:
    
    - Unique + NOT NULL.
        
6. **Candidate Key**:
    
    - All possible primary keys.
        
7. **Foreign Key**:
    
    - Refers to primary key of another table.
        
8. **Relation properties**:
    
    - No duplicate tuples.
        
    - Order of rows/columns does NOT matter.
        
9. **NULL value**:
    
    - Unknown / not applicable.
        
    - Not equal to 0 or blank.
        
10. **Schema vs Instance**:
    
    - Schema = structure
        
    - Instance = data
        

---

## **Term 3: Relational Algebra**

1. **Type**: **Procedural query language**.
    
2. **Input & Output**: Relations only (closure property).
    
3. **Selection (σ)**:
    
    - Filters rows.
        
4. **Projection (π)**:
    
    - Selects columns.
        
    - Removes duplicates.
        
5. **Union (∪)**:
    
    - Requires union compatibility.
        
6. **Set Difference (−)**:
    
    - Tuples in first but not second relation.
        
7. **Cartesian Product (×)**:
    
    - Combines all tuples.
        
8. **Join**:
    
    - Derived operation.
        
9. **Natural Join**:
    
    - Matches attributes with same name automatically.
        
10. **Used for**:
    
    - Query optimization & theoretical foundation.
        

**MCQ favorite**: Difference between **Selection vs Projection**.

---

## **Term 4: Tuple Relational Calculus**

1. **Type**: **Non-procedural (declarative)**.
    
2. **Focus**:
    
    - What to retrieve, not how.
        
3. **Based on**:
    
    - Predicate logic.
        
4. **Syntax form**:
    
    - `{ t | P(t) }`
        
5. **Tuple Variable**:
    
    - Represents a row.
        
6. **Predicate**:
    
    - Condition on attributes.
        
7. **Quantifiers**:
    
    - ∃ (exists), ∀ (for all).
        
8. **Safe expressions**:
    
    - Must produce finite results.
        
9. **Closer to SQL** than relational algebra.
    
10. **Key comparison**:
    
    - RA = procedural
        
    - TRC = declarative
        

---

## **Term 5: Integrity Constraints**

1. **Purpose**:
    
    - Maintain correctness of data.
        
2. **Domain Constraint**:
    
    - Valid attribute values.
        
3. **Entity Integrity**:
    
    - Primary key cannot be NULL.
        
4. **Referential Integrity**:
    
    - Foreign key must match primary key.
        
5. **Key Constraint**:
    
    - No duplicate primary key values.
        
6. **CHECK constraint**:
    
    - Custom condition.
        
7. **UNIQUE constraint**:
    
    - Ensures uniqueness (NULL allowed).
        
8. **NOT NULL constraint**:
    
    - Value must exist.
        
9. **ON DELETE options**:
    
    - CASCADE, SET NULL, RESTRICT.
        
10. **Very common MCQ area**.
    

---

## **Term 6: Normal Forms**

1. **Goal**:
    
    - Reduce redundancy & anomalies.
        
2. **1NF**:
    
    - Atomic values only.
        
3. **2NF**:
    
    - No partial dependency.
        
4. **3NF**:
    
    - No transitive dependency.
        
5. **BCNF**:
    
    - Stronger than 3NF.
        
6. **Functional Dependency (FD)**:
    
    - X → Y.
        
7. **Insertion anomaly**:
    
    - Cannot insert data without other data.
        
8. **Deletion anomaly**:
    
    - Deleting data removes unintended info.
        
9. **Update anomaly**:
    
    - Multiple updates needed.
        
10. **MCQ focus**:
    

- Identify which normal form fixes which problem.
    

---

## **Term 7: File Organization**

1. **Definition**: How records are **stored on disk**.
    
2. **Heap (Unordered) File**:
    
    - Records placed anywhere.
        
    - Fast insert, slow search.
        
3. **Sequential File**:
    
    - Records stored in sorted order.
        
    - Good for range queries.
        
4. **Hash File Organization**:
    
    - Uses hash function on key.
        
    - Very fast equality search.
        
5. **Index File Organization**:
    
    - Uses index structure for access.
        
6. **Insertion cost**:
    
    - Lowest in heap files.
        
7. **Deletion cost**:
    
    - Costly in sequential files.
        
8. **Range queries**:
    
    - Efficient in sequential files.
        
9. **Equality search**:
    
    - Best with hashing.
        
10. **MCQ trap**:
    
    - Hashing is **not good for range queries**.
        

---

## **Term 8: Indexing**

1. **Purpose**:
    
    - Improve **search performance**.
        
2. **Index**:
    
    - Auxiliary structure mapping key → record.
        
3. **Primary Index**:
    
    - Built on primary key.
        
4. **Secondary Index**:
    
    - Built on non-key attribute.
        
5. **Dense Index**:
    
    - One index entry per record.
        
6. **Sparse Index**:
    
    - One entry per block.
        
7. **Clustering Index**:
    
    - Determines physical order of data.
        
8. **Index overhead**:
    
    - Extra storage + update cost.
        
9. **Index vs File**:
    
    - Index ≠ data storage.
        
10. **MCQ favorite**:
    
    - Dense vs Sparse difference.
        

---

## **Term 9: B-Tree**

1. **Type**:
    
    - Balanced multi-level index tree.
        
2. **All nodes**:
    
    - Store keys and data pointers.
        
3. **Height**:
    
    - Always balanced.
        
4. **Search time**:
    
    - O(log n).
        
5. **Internal nodes**:
    
    - Contain keys and pointers.
        
6. **Leaf nodes**:
    
    - Same level.
        
7. **Insertion**:
    
    - Causes node split if full.
        
8. **Deletion**:
    
    - Causes merge or redistribution.
        
9. **Used for**:
    
    - Databases & file systems.
        
10. **MCQ trap**:
    
    - Data **can be stored in internal nodes**.
        

---

## **Term 10: B+ Tree** ⭐ (VERY IMPORTANT)

1. **Variant of**:
    
    - B-Tree.
        
2. **Data storage**:
    
    - **Only in leaf nodes**.
        
3. **Internal nodes**:
    
    - Store **keys only**.
        
4. **Leaf nodes**:
    
    - Linked as **linked list**.
        
5. **Range queries**:
    
    - Very efficient.
        
6. **Height**:
    
    - Smaller than B-Tree.
        
7. **Disk access**:
    
    - Optimized for disk I/O.
        
8. **Search path**:
    
    - Always ends at leaf.
        
9. **Used in**:
    
    - Most DBMS indexes.
        
10. **Most common MCQ**:
    
    - Difference between **B-Tree vs B+ Tree**.
        

---

## **Term 11: Transactions**

1. **Transaction**:
    
    - Logical unit of work.
        
2. **ACID properties**:
    
    - Atomicity, Consistency, Isolation, Durability.
        
3. **Atomicity**:
    
    - All or nothing.
        
4. **Consistency**:
    
    - Valid state → valid state.
        
5. **Isolation**:
    
    - Transactions don’t interfere.
        
6. **Durability**:
    
    - Committed data persists.
        
7. **Commit**:
    
    - Makes changes permanent.
        
8. **Rollback**:
    
    - Undo changes.
        
9. **Partial failure**:
    
    - Transaction aborts.
        
10. **MCQ favorite**:
    
    - Match ACID property with meaning.
        

---

## **Term 12: Concurrency Control**

1. **Purpose**:
    
    - Handle simultaneous transactions.
        
2. **Problem without control**:
    
    - Inconsistent data.
        
3. **Lost update**:
    
    - One update overwrites another.
        
4. **Dirty read**:
    
    - Reading uncommitted data.
        
5. **Unrepeatable read**:
    
    - Same read gives different values.
        
6. **Serializability**:
    
    - Equivalent to serial execution.
        
7. **Locking**:
    
    - Shared (read), Exclusive (write).
        
8. **Two-Phase Locking (2PL)**:
    
    - Growing + shrinking phase.
        
9. **Deadlock**:
    
    - Transactions wait on each other.
        
10. **MCQ trap**:
    
    - Deadlock ≠ starvation.