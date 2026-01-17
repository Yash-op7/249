# **Topic 8: Data Warehousing**

---

## **1. Data Warehouse**

1. Central repository of **integrated data**.
    
2. Used for **analysis and reporting**, not transactions.
    
3. Stores **historical data**.
    
4. Subject-oriented (e.g., sales, finance).
    
5. Read-optimized system.
    
6. Supports decision making.
    
7. Separate from operational databases.
    
8. Large volume of data.
    
9. Time-variant data.
    
10. **MCQ favorite**:
    

- Data warehouse ≠ OLTP database.
    

---

## **2. Data Extraction**

1. First step of ETL process.
    
2. Data taken from **source systems**.
    
3. Sources can be databases, files, APIs.
    
4. Extracts raw data.
    
5. Can be full or incremental.
    
6. Requires source connectivity.
    
7. Data not cleaned yet.
    
8. Performance-critical step.
    
9. Errors propagated if faulty.
    
10. **MCQ trap**:
    
    - Extraction ≠ transformation.
        

---

## **3. Data Cleaning**

1. Removes incorrect data.
    
2. Handles missing values.
    
3. Removes duplicates.
    
4. Fixes inconsistencies.
    
5. Corrects data formats.
    
6. Improves data quality.
    
7. Essential before analysis.
    
8. Time-consuming step.
    
9. Reduces noise.
    
10. **MCQ favorite**:
    
    - Cleaning improves accuracy.
        

---

## **4. Data Transformation**

1. Converts data into suitable format.
    
2. Applies business rules.
    
3. Normalization / aggregation.
    
4. Data type conversion.
    
5. Derived columns created.
    
6. Structure alignment.
    
7. Happens after cleaning.
    
8. Prepares for loading.
    
9. Ensures consistency.
    
10. **MCQ trap**:
    
    - Transformation ≠ cleaning.
        

---

## **5. Data Loading**

1. Final step of ETL.
    
2. Loads data into warehouse.
    
3. Can be batch or real-time.
    
4. Full load or incremental load.
    
5. Requires target schema.
    
6. Must preserve integrity.
    
7. Often scheduled.
    
8. Affects warehouse performance.
    
9. Uses bulk operations.
    
10. **MCQ favorite**:
    
    - Loading = writing to warehouse.
        

---

## **6. Metadata**

1. Data **about data**.
    
2. Describes structure and meaning.
    
3. Includes table definitions.
    
4. Includes column types.
    
5. Stores transformation rules.
    
6. Helps users understand data.
    
7. Used by ETL tools.
    
8. Improves data governance.
    
9. Stored separately.
    
10. **MCQ trap**:
    
    - Metadata ≠ actual data.
        

---

## **7. Data Cube**

1. Multi-dimensional data structure.
    
2. Used in OLAP.
    
3. Dimensions + measures.
    
4. Example dimensions:
    
    - Time, Product, Location.
        
5. Measures:
    
    - Sales, profit.
        
6. Enables fast analysis.
    
7. Supports slicing and dicing.
    
8. Improves query speed.
    
9. Built from fact tables.
    
10. **MCQ favorite**:
    
    - Data cube = multi-dimensional view.
        

---

## **8. Data Mart**

1. Subset of data warehouse.
    
2. Focused on one business area.
    
3. Smaller in size.
    
4. Faster to query.
    
5. Department-specific.
    
6. Can be dependent or independent.
    
7. Used by specific teams.
    
8. Lower cost than warehouse.
    
9. Improves performance.
    
10. **MCQ trap**:
    
    - Data mart ≠ data warehouse.
        

---

## **9. Data Models (Warehouse Context)**

1. Defines structure of warehouse.
    
2. Focused on analysis.
    
3. Different from OLTP models.
    
4. Uses denormalization.
    
5. Optimized for queries.
    
6. Based on facts and dimensions.
    
7. Supports reporting tools.
    
8. Easier to understand.
    
9. Schema-based.
    
10. **MCQ favorite**:
    
    - Warehouse models favor read efficiency.
        

---

## **10. Star Schema**

1. Fact table at center.
    
2. Dimension tables around it.
    
3. Simple structure.
    
4. Denormalized dimensions.
    
5. Faster queries.
    
6. Easy to understand.
    
7. More storage required.
    
8. Used widely.
    
9. Optimized for OLAP.
    
10. **MCQ trap**:
    
    - Star schema ≠ normalized.
        

---

## **11. Snowflake Schema**

1. Extension of star schema.
    
2. Dimension tables normalized.
    
3. More complex design.
    
4. Less redundancy.
    
5. More joins required.
    
6. Slower queries than star.
    
7. Less storage than star.
    
8. Harder to understand.
    
9. Used when storage is concern.
    
10. **MCQ favorite**:
    
    - Snowflake = normalized dimensions.
        

---

## **12. ETL Process (Very Common MCQ)**

1. **Extract** → **Transform** → **Load**.
    
2. Core of data warehousing.
    
3. Moves data from sources to warehouse.
    
4. Ensures data consistency.
    
5. Improves data quality.
    
6. Runs periodically.
    
7. Uses ETL tools.
    
8. Performance-critical.
    
9. Errors affect analytics.
    
10. **MCQ favorite**:
    
    - Correct ETL order.
        

---