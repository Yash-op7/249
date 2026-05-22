Rough Notes

- Most applications are built by layering one data model on top of another, key question being how is each layer represented in terms of the next lower layer  
- There are many different kinds of data models, the choice of which determines what the app can or cant do, because each data model makes assumptions on how it will be used, some usage patterns will be easy others wont be supported, some transformations feel natural others awkward, some ops perform fast, others badly.  
- Here we compare relational, document and graph based data models  
- Relational vs document  
- Birth of nosql \= not only sql, driving forces:  
  - Need for greater scalability than relational dbs, v large datasets and v high write throuput  
  - Preference for open source  
  - Special query ops not supported well by relational model  
  - More dynamic and expressive data model, less restrictive than relational schemas  
- The object relational mismatch  
  - Impendance mismatch, the disconnect between application level object model and db level relational model, partial solutions: ORM frameworks  
- LinkedIn profile example as a relational schema  
  - Identifying one to one and one to many relationships using user\_id as primary key, first\_name, positions  
  - 3 ways to represent one to many:  
    - Normalized model (separate tables) \- db understands structure deeply  
    - Structured types inside one row, using json or arrays as column values \- db partly understands structure  
    - Plain text JSON \- only app understands structure

## Side note: 

- self contained document: All needed data sits together → fewer joins → simpler reads, 

  ## **Use document model when:**

* data naturally belongs to one owner  
* most reads fetch whole object together, example shopping cart object, API fetch, one webpage’s data  
* few cross-entity relationships  
* nested variable fields are common  
* Because document DB strength is: **locality of related data**

Example:

* LinkedIn profile  
* shopping cart  
* blog post with comments 

#     **Use relational model when:**

* many entities reference each other  
* many-to-many relationships exist  
* consistency across entities matters: referential integrity  
* joins are common

Example university DB, dense relationships:

* students ↔ courses  
* courses ↔ instructors  
* assignments ↔ submissions  
* grades ↔ students

\[Another SIDE NOTE\] Explanation of dense relationships:

Let’s define main tables:

* students  
* courses  
* instructors  
* assignments  
* Grades

Then:

## **One-to-many**

### One student → many assignments submitted

One student has many submissions.

Each submission belongs to one student.

### One course → many assignments

courses → assignments

One course creates many assignments.

## **Many-to-one**

Same relationship from opposite side:

### Many submissions → one student

### Many students → one department

# **Many-to-many (important dense relationship)**

## **Students ↔ Courses**

A student takes many courses.

A course has many students.

Cannot store directly.

Need junction table:

## **enrollments**

| student\_id | course\_id |

This creates:

students ↔ enrollments ↔ courses

Very common join:

SELECT \*

FROM students

JOIN enrollments

JOIN courses

# **Why this becomes dense**

Because one query may touch:

student \+ enrollment \+ course \+ instructor \+ grades

That means multiple joins.

This is where relational shines.

Everything references everything.

That is **highly interconnected**.

# Choose model per access pattern, not per domain name

Even inside one product:

* profile → document  
* social graph → graph/relational  
* search index → inverted index

# **Standard internal split example**

Large systems often split storage by access pattern:

---

# **Profile → document**

Store:

{

 "name":"Yash",

 "headline":"SDE-1",

 "experience":\[...\]

}

Because profile page fetches whole thing together.

---

# **Social graph → graph / relational**

Store connections:

| user\_id | connected\_to |

Because query asks:

* mutual connections  
* 2nd degree network  
* suggested people

This is relationship-heavy.

---

# **Search index → inverted index**

Suppose search:

"React engineer Bangalore"

Need fast lookup:

## **inverted index**

react → \[user1,user7,user20\]

bangalore → \[user1,user3\]

This is neither relational nor document.

This is search engine style.

Often handled by Elasticsearch.

---

# **Practical rule**

## **If one screen/API usually loads everything together → document**

## **If multiple workflows independently modify pieces → relational**

# **Document example**

## **E-commerce cart**

When opening cart:

Need:

* all items  
* quantities  
* prices

Always together.

Store one document:

{  
 "user\_id":101,  
 "items":\[...\]  
}

Because reads happen together.

# **Relational example**

## **Orders system**

Need separately:

* update inventory  
* create payment  
* create shipment

These entities evolve independently.

So tables:

* orders  
* inventory  
* payments  
* shipments

Because writes happen separately.

# **Real engineering intuition**

Ask:

"Will this object grow independently in multiple directions?"

If yes → relational often wins.

If no → document often simpler.

\[SIDE NOTE FINISH\]

→ JSON model reduces impedance mismatch, but there problems with JSON as an encoding format

→ Lack of schema is often cited as an advantage

→ JSON has better locality

→ JSON makes the one to many tree structure explicit, where relational keeps it implicit

### Many to one and Many to many relationships:

→ There are advantages to standardize lists of geographic regions, industries, etc and letting users choose instead of using free-text fields

→ Where we store an ID or a text string is a question of duplication.

→  Removing such duplication is the key idea behind normalization in databases.

→ many to one relationships dont fit nicely into the doc model, and doc dbs dont support joins, so application code needs to make multiple queries to the db, join work is shifted from db to app

→ even if v1 of app fits in a join free doc model, data has a tendency of becoming more interconnected as features are added to applications(resume example, orgs as entities and recommendations,  fig 2.4 is important)

## Are doc dbs repeating history

Most important takeaway:

→ Document DB \= hierarchical storage returns because hardware improved and many app objects are naturally nested.

→ Relational DB still dominates when relationships become complex because joins \+ optimizer scale better intellectually and operationally.

One sentence summary:

Relational model won because it removed manual navigation of relationships and delegated it to the query optimizer.

## Relational Versus Document Databases Today

→ When comparing relational dbs to doc dbs, we focus on their fault-tolerance properties and concurrency handling.  
→ differences in data model:

- Favor of doc: schema flexibility, better perf due to locality, closer to app’s data structures  
- Favor of rel: support for: joins, many to one, many to many relationships

### Which data model leads to simpler application code?

* The relational technique of ***shredding*** (splitting a doc structure into multiple tables) can lead to difficult to manage and unnecessarily complicated app code.  
* Use document model when data is tree-like and loaded together.  
* Use relational when joins are frequent (in many to many, whose solution is denormalization or app level joins).  
* Denormalization shifts consistency burden to application.  
* Highly interconnected data often fits graph model best.

### Schema flexibility in the document model

* Document databases are not truly schemaless; they usually have an implicit schema enforced by application code.  
* Schema-on-read \= structure interpreted when reading data; schema-on-write \= structure enforced before writing data.  
* Schema-on-read is like dynamic typing; schema-on-write is like static typing.  
* Document databases make schema evolution easier because new and old document versions can coexist.  
* Relational schema changes usually require migrations, though modern ALTER TABLE is often fast.  
* Large data rewrites (UPDATE all rows) are expensive in any database, regardless of model.  
* Schema-on-read works well for heterogeneous or externally changing data.  
* Schema-on-write is valuable when records are expected to follow one stable structure.  
* Flexible schema shifts responsibility from DB to app.

### Data locality for queries

* Document storage gives locality: related data stored together makes full-object reads faster.  
* Document locality helps only when large parts of the document are read together.  
* Reading a small field from a large document can be wasteful because the whole document is usually loaded.  
* Document updates often rewrite the whole document, so large or growing documents are costly.  
* Keep documents small and avoid frequent size-changing updates.  
* Data locality is not unique to document databases; relational systems can also group related rows physically. Examples: Google’s Spanner DB, Oracle’s *multi-table index cluster tables*, *column-family* concept in Bigtable data model used in Cassandra and HBase all have a similar purpose of managing locality  
* Relational databases now support JSON/XML, allowing document-like storage inside relational models.  
* Document databases are adding joins and relational-style querying.  
* Relational and document models are converging into hybrid systems.

### Query Languages for Data

* SQL is declarative: you specify what data you want, not how to retrieve it.  
* Imperative code specifies exact execution steps; declarative queries specify result conditions.  
* The query optimizer chooses indexes, join order, and execution strategy automatically.  
* Declarative queries are shorter and hide storage/engine details from application code.  
* Because queries avoid low-level assumptions (like order of rows), databases can optimize internally without breaking applications.  
* Declarative languages are easier to parallelize because execution order is not fixed by the application.  
* Declarative queries shift execution responsibility from developer to database optimizer.  
* The true power of declarative systems is: **abstraction enables future optimization**  
  This idea later appears everywhere:  
* SQL  
* distributed schedulers  
* stream processing  
* query planners   
* Example: We write: SELECT \* FROM users WHERE country='India'  
  and database decides:  
* full scan?  
* index scan?  
* parallel scan?  
* cache hit?  
  **You don't rewrite app code when engine improves.**

### Declarative Queries on the Web

* Declarative thinking is valuable beyond databases; CSS is a declarative query language for document structure.  
* CSS specifies what pattern to match, not how to traverse the DOM.  
* Imperative DOM manipulation requires manual traversal, state handling, and update logic.  
* Declarative rules automatically react when underlying data/state changes.  
* Declarative languages let engines improve performance without requiring application rewrites.  
* CSS vs JavaScript mirrors SQL vs imperative database access: concise intent vs manual execution.  
* Declarative systems encode intent; imperative systems encode procedure.  
* SQL succeeded for the same reason CSS succeeded.  
  Because developers write:  
* **desired result**  
* not traversal logic  
  and engine owns optimization.  
* **better abstraction \= long-term flexibility**

### MapReduce Querying

* MapReduce is a distributed bulk-processing model used by some NoSQL databases for read-only aggregation queries.  
* Think of **MapReduce** as a *primitive toolkit* for distributed computing.  
* It sits between declarative and imperative: query logic is written as reusable ***map*** and ***reduce*** functions.  
* ***map*** emits key-value pairs; ***reduce*** aggregates values for each key.  
* MapReduce functions must be pure: no side effects, no extra DB queries, deterministic execution.  
* Purity allows distributed execution, retries, and parallel processing across machines.  
* MapReduce is powerful but low-level and harder to write than declarative queries.  
* A SQL query can be **translated into multiple MapReduce steps**, which together form a pipeline.  
* *“MapReduce doesn’t have a monopoly on distributed query execution”*  
  * MapReduce was **one popular approach**, but it’s not the only or the best way  
  * **SQL** \= high-level way to express queries  
  * You *can* implement SQL using MapReduce  
  * But modern systems often use **better alternatives**  
* Declarative query languages give optimizers more freedom than MapReduce code.  
* Because of this, NoSQL systems like MongoDB introduced higher-level aggregation pipelines.  
* Modern NoSQL systems often evolve toward SQL-like declarative querying.

### Graph-Like Data Models

* Graph models become natural when many-to-many relationships are dense and deeply interconnected.  
* A graph consists of vertices (entities) and edges (relationships).  
* Document model fits trees; relational model fits moderate joins; graph model fits relationship-heavy traversal.  
* Graphs naturally model social networks, web links, transport networks, and recommendation systems.  
* Graph databases allow heterogeneous entities in one model (people, places, events, comments, etc.).  
* Edges carry semantic meaning such as lives\_in, married, born\_in, within. See Fig: 2-5  
* Graph queries focus on traversing relationships rather than joining tables repeatedly.  
* Common graph models include property graphs (implemented by Neo4j) and triple stores(implemented by Datomic)  
* When relationships become first-class citizens, graph becomes the natural data model.  
* Tree → Document  
* Moderate joins → Relational  
* Dense traversal → Graph

### Property Graphs

* A property graph stores data as vertices (entities) and edges (relationships), both carrying properties.  
* Vertices have id, incoming/outgoing edges, and key-value properties.  
* Edges have id, tail vertex, head vertex, relationship label, and key-value properties.  
* A property graph can be represented internally using two relational tables: vertices and edges.  
* Indexes on tail\_vertex and head\_vertex make graph traversal efficient in both directions.  
* Any vertex can connect to any other vertex; graph schema is highly flexible.  
* Edge labels allow multiple relationship types to coexist in one graph cleanly.  
* Graphs handle irregular, evolving, and heterogeneous relationships better than rigid relational schemas.  
* Graphs are highly evolvable because new entity types and relationships can be added without schema redesign.  
* Property graphs make relationships explicit, typed, and traversable.

### The Cypher Query Language

* Cypher is a declarative query language for property graphs, designed for pattern matching over vertices and edges.  
* CREATE defines graph data by declaring vertices and labeled relationships.  
* MATCH expresses graph traversal as patterns rather than manual step-by-step navigation.  
* Variables bind vertices so the same entity can participate in multiple relationship conditions.  
* Relationship labels (e.g. BORN\_IN, LIVES\_IN, WITHIN) encode semantic meaning directly in queries.  
* Variable-length traversal (\*0..) allows following chains of edges across hierarchy levels.  
* Cypher expresses relationship patterns directly, avoiding multiple explicit joins.  
* Like SQL, Cypher is declarative: optimizer chooses traversal direction and execution strategy.  
* Cypher queries describe graph patterns, not traversal algorithms.  
* (person)-\[:BORN\_IN\]-\>()-\[:WITHIN\*0..\]-\>(US) means: **Find a person whose birthplace eventually belongs inside US.**  
* Here The “`*0..”` means follow zero or more `WITHIN` edges: City → State → Country → Continent, whereas Relational version would need repeated joins.  
* If the same query can be written in 4 lines in one query language but requires 29 lines in another, that just shows that different data models are designed to satisfy different use cases. It’s important to pick a data model that is suitable for your application

### Triple-Stores and SPARQL

* Triple-store and property graph models represent the same graph ideas using different terminology.  
* All data is stored as triples: *(subject, predicate, object)*, example: *(Jim, likes, bananas)*  
* Subject \= entity/vertex; predicate \= relationship/property name; object \= value or another entity.  
* If **object** is a primitive value, the triple represents a property.  
* If **object** is another entity, the triple represents an edge between vertices.  
* Triple-stores reduce all graph data into one uniform structure, regardless of entity type.  
* Turtle is a readable syntax for writing triples.  
* Semicolons in Turtle allow multiple predicates for the same subject, reducing repetition.  
* Triple-store turns every fact into a uniform sentence: subject → predicate → object.  
* Property graph says:  
  * Lucy \--bornIn--\> Idaho  
  * Lucy.age \= 33  
* Triple-store rewrites both uniformly:  
  * (lucy, bornIn, idaho)  
  * (lucy, age, 33\)  
* Same model, more uniform representation.  
* Property graph has two concepts:  
  * Vertices  
  * Edges  
* Triple-store reduces both into: **facts**

#### The semantic web

* Semantic web aimed to publish machine-readable data across websites using a shared triple format.  
* RDF was designed as a common format so data from many sources could combine into one web of data.  
* The semantic web vision was overhyped and saw limited practical adoption.  
* Despite that, triples remain useful as an internal application data model.  
* Triple-stores are valuable even without semantic web adoption.

#### The RDF data model

* RDF stores triples in standardized formats such as Turtle or RDF/XML.  
* Turtle is human-readable; RDF/XML is equivalent but much more verbose.  
* RDF often uses URIs for subjects, predicates, and objects to avoid naming conflicts across systems.  
* URIs act as namespaces, not necessarily resolvable web addresses.

#### The SPARQL query language

* SPARQL is a declarative query language for RDF triple-stores.  
* SPARQL matches graph patterns similarly to Cypher.  
* Properties and relationships use the same predicate syntax because RDF treats both uniformly.  
* Path expressions (/ and \*) allow multi-hop graph traversal concisely.  
* SPARQL is often more compact than Cypher for triple-based graph queries.  
* SPARQL is SQL-like pattern matching over triples.

### Graph Databases Compared to the Network Model (not important)

* Graph databases are not a return to CODASYL; they remove the rigidity that made network databases hard to use.  
* Graph databases allow any vertex to connect to any other vertex without schema restrictions.  
* Vertices can be accessed directly by ID or index, not only by predefined traversal paths.  
* Graphs do not impose physical ordering on relationships; ordering is applied only at query time.  
* Modern graph databases support declarative query languages like Cypher and SPARQL.  
* The key improvement over old network databases is flexible traversal without hardcoded access paths.  
* Graph databases keep relationship power but remove path rigidity.  
* ⭐CODASYL problem: application owned navigation logic  
* ⭐Modern graph DB improvement: database owns traversal optimization

### The Foundation: Datalog

* Datalog is a rule-based query language that underlies later graph query languages.  
* It represents facts as predicate(subject, object), similar to triples.  
* **Queries are built by defining reusable rules that derive new facts from existing facts.**  
* **Rules can be recursive**, making Datalog powerful for hierarchical and graph traversal problems.  
* A rule applies when all predicates on its right side match existing facts or derived facts.  
* Complex graph queries are built incrementally by composing simple predicates.  
* Datalog emphasizes **logical inference** rather than direct pattern matching.  
* Its strength is reusable rule composition across many queries.  
* **Datalog queries by teaching the database new logical rules.**

### Summary

* Hierarchical model handled trees well but failed for many-to-many relationships.  
* Relational model solved many-to-many relationships through tables and joins.  
* NoSQL diverged into two major directions: document for self-contained aggregates, graph for dense relationships.  
* Document, relational, and graph models each fit different access patterns; no single model fits all domains.  
* Schema flexibility usually shifts responsibility from database enforcement to application logic.  
* Every data model tends to develop its own query language optimized for its abstractions.  
* Declarative query languages consistently outperform imperative access patterns in long-term flexibility.  
* Specialized domains (genomics, physics, search) often require custom data models beyond general-purpose databases.

