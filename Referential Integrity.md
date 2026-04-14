Referential integrity in RDBMS is ==a set of rules ensuring that relationships between tables remain consistent, accurate, and reliable==. It mandates that a [foreign key](https://www.google.com/search?q=foreign+key&oq=what+is+referential+integrity+in+rdbms&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTILCAEQABgKGAsYgAQyCwgCEAAYChgLGIAEMgsIAxAAGAoYCxiABDIICAQQABgWGB4yCAgFEAAYFhgeMggIBhAAGBYYHjIICAcQABgWGB4yCAgIEAAYFhgeMgYICRAuGEDSAQg5OTkzajBqMagCCLACAfEFcSw6BQlKIPU&sourceid=chrome&ie=UTF-8&mstk=AUtExfCv-TnvuBI1VX7RB-9K89sQ6aOWj8ylU9p8mVCTCl0fuiOrLq2tUNJNuZ53TLqQSDJXVEpNZ5zqiifvE-bxAcktujQ0Zb1OWj929ZiFRdJxohPZc8aWhHXOe1a8NRmx-aYuDfUyvnG13lapdhLmdSkGli1J0vM45wNoYF7FFrsMpPE&csui=3&ved=2ahUKEwjnioPn98iTAxUhUGcHHeLuN88QgK4QegQIARAE) in a child table must match an existing [primary key](https://www.google.com/search?q=primary+key&oq=what+is+referential+integrity+in+rdbms&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTILCAEQABgKGAsYgAQyCwgCEAAYChgLGIAEMgsIAxAAGAoYCxiABDIICAQQABgWGB4yCAgFEAAYFhgeMggIBhAAGBYYHjIICAcQABgWGB4yCAgIEAAYFhgeMgYICRAuGEDSAQg5OTkzajBqMagCCLACAfEFcSw6BQlKIPU&sourceid=chrome&ie=UTF-8&mstk=AUtExfCv-TnvuBI1VX7RB-9K89sQ6aOWj8ylU9p8mVCTCl0fuiOrLq2tUNJNuZ53TLqQSDJXVEpNZ5zqiifvE-bxAcktujQ0Zb1OWj929ZiFRdJxohPZc8aWhHXOe1a8NRmx-aYuDfUyvnG13lapdhLmdSkGli1J0vM45wNoYF7FFrsMpPE&csui=3&ved=2ahUKEwjnioPn98iTAxUhUGcHHeLuN88QgK4QegQIARAF) in the parent table (or be `NULL`), preventing "orphaned" records. This ensures valid, linked data across tables.

**Key Aspects of Referential Integrity:**

- **Validity Constraint:** A foreign key value must either exist in the referenced table's primary key column or be `NULL`.
- **Relationship Enforcement:** It prevents inserting a record with a foreign key that does not exist in the parent table.
- **Cascade Actions:** When a primary key is updated or deleted in the parent table, referential integrity rules (like `CASCADE`, `SET NULL`, or `RESTRICT`) determine how the child table records are handled.
- **Prevents Deletion Errors:** You cannot delete a parent record if it is currently being referenced by a child record (unless configured otherwise).
**Why It Matters:**

- **Maintains Data Consistency:** Ensures data remains accurate and reliable.
- **Avoids Orphaned Records:** Prevents child records from pointing to non-existent parent records.
- **Strengthens Data Integrity:** Enforces logical connections, making the database reliable.