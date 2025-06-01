# Topic: DATABASE

## Compare Relational DB (SQL) vs NoSQL. It's also really nice to know about newSQL (a kind of auto sharding DB which support SQL stuff but scale like NoSQL)

Relational DB is organized by schema, table. All records in a table must be in the same structure, follow some constraints. Relational DB guarantees ACID characteristics so it is safe and stable.

NoSQL is something that is not relational DB. Currently, it contains 4 main kinds:

- The first kind is Document. In this kind, each piece of data is a JSON document.
- The second is Key-Value DB. Simply, it is like a hash map that maps one key to one value.
- The third is Wide Column DB. Records in a table can have a different number of columns.
- The last one is Graph which is suitable for some graph-like data, for example, connections of people on a social network.

## How these 2 things can scale up?

Usually, people scale up Relational DB vertically. Add more CPU, more RAM, more storage. An additional thing we can do is sharding, that device data into different clusters, different nodes. Due to ACID characteristics, relational DB needs to do more work when writing and reading data to ensure those characteristics.
Cause NoSQL sacrifices one or some ACID characteristics, it can be easily scaled horizontally just by simply adding nodes.

## 3 normal forms in DBMS

The first normal form is about making everything atomic. It means a column stores a single value, not multiple values.

The second normal form is about removing partial dependencies. It means no column depends only on a subset of the candidate key of the relation.

The third normal form is about removing transitive dependencies. It means there is no relationship that column A depends on column B, and column B depends on column C.

## ACID of SQL and BASE of NoSQL? Why NoSQL is eventual consistency?


## **ACID - Relational Databases**
ACID ensures **data consistency** in SQL databases through strict rules.

1. **Atomicity**:  
   - All operations in a transaction are **all-or-nothing**.
   - Example: Money transfer completes fully or not at all.

2. **Consistency**:  
   - Transactions take the database from one **valid state** to another.
   - Example: Prevents negative bank balances.

3. **Isolation**:  
   - Concurrent transactions do not affect each other.
   - Example: Prevents **dirty reads** (reading uncommitted changes).

4. **Durability**:  
   - Once committed, data remains **permanent**, even after failures.
   - Example: Power loss won’t erase completed transactions.

---

## **BASE - NoSQL Databases**
BASE provides **high availability and scalability** at the cost of strict consistency.

1. **Basically Available**:  
   - Guarantees availability even if some nodes fail.

2. **Soft State**:  
   - The system state **can change over time**, even without input.

3. **Eventual Consistency**:  
   - Data updates **propagate gradually** across nodes.
   - Example: **Leader-Follower Architecture**  
     - **Leader (Primary Node)** updates first.  
     - **Followers (Replicas)** sync **eventually**, causing temporary **stale reads**.

---

## **Why NoSQL Uses Eventual Consistency?**
- **Distributed Architecture**: Data is spread across multiple servers.  
- **Scalability**: No need for immediate synchronization across nodes.  
- **Performance**: Allows fast reads and writes with some delay in updates.  
- **Example**: Twitter’s follower count **may be delayed** due to replication lag.

### **Trade-offs of RDBMS (SQL)**
✅ Strong consistency  
✅ ACID transactions  
❌ Hard to scale horizontally  

### **Trade-offs of NoSQL**
✅ High availability & scalability  
✅ BASE model supports large distributed systems  
❌ Can return **stale data** temporarily (eventual consistency)

**Conclusion**:  
Use **SQL (ACID)** for **banking & critical systems**.  
Use **NoSQL (BASE)** for **high-traffic, distributed apps** like social media.

## NoSQL database type
# **Types of NoSQL Databases**

NoSQL databases are designed for scalability, high availability, and flexible schema management. They fall into four main categories:

## **1. Key-Value Stores**
- **Definition**: Data is stored as key-value pairs.
- **Use Case**: Caching, session management, real-time analytics.
- **Examples**: 
  - Redis
  - Amazon DynamoDB
  - Riak

---

## **2. Document Stores**
- **Definition**: Stores data as JSON-like documents with flexible schemas.
- **Use Case**: Content management, e-commerce, catalogs.
- **Examples**: 
  - MongoDB
  - CouchDB
  - Firebase Firestore

---

## **3. Column-Family Stores**
- **Definition**: Data is stored in columns rather than rows, optimized for fast queries.
- **Use Case**: Big data applications, real-time analytics.
- **Examples**: 
  - Apache Cassandra
  - HBase
  - ScyllaDB

---

## **4. Graph Databases**
- **Definition**: Uses nodes and edges to represent relationships between data.
- **Use Case**: Social networks, recommendation engines, fraud detection.
- **Examples**: 
  - Neo4j
  - ArangoDB
  - Amazon Neptune

---

## **Comparison Table**

| Type               | Structure        | Use Case Example | Examples |
|--------------------|----------------|----------------|----------------|
| **Key-Value**     | Key-Value Pairs | Caching, Sessions | Redis, DynamoDB |
| **Document Store** | JSON/BSON Docs  | Content, E-commerce | MongoDB, CouchDB |
| **Column-Family**  | Column-based    | Big Data, Analytics | Cassandra, HBase |
| **Graph**         | Nodes & Edges   | Social Networks, Recommendations | Neo4j, ArangoDB |

---

## **Choosing the Right NoSQL Database**
- ✅ **Need high-speed caching?** → Use **Redis** (Key-Value).  
- ✅ **Handling unstructured JSON data?** → Use **MongoDB** (Document Store).  
- ✅ **Need scalable big data processing?** → Use **Cassandra** (Column-Family).  
- ✅ **Managing complex relationships?** → Use **Neo4j** (Graph).  

Each NoSQL type serves a specific purpose, making it essential to choose the right one based on the use case.

## CAP theorem in this case. [This is a so nice graph](http://blog.nahurst.com/visual-guide-to-nosql-systems)

In CAP theorem:

- C stands for Consistency
- A stands for Availability
- P stands for Network Partition

It's kinda confused when people say we can only choose 2 out of three things because network partition is not something we can choose in a distributed system.
In the good condition, when there is no network partition, a system can guarantee both consistency and availability. But when the network fault comes, we have to choose one thing between Consistency and Availability to sacrifice.
# **CAP Theorem (Consistency, Availability, Partition Tolerance)**

## **1. What is CAP Theorem?**
The **CAP theorem** states that in a **distributed system**, you can only guarantee **two** out of the following three properties at the same time:
- **Consistency (C)**: Every read receives the latest written data.
- **Availability (A)**: Every request gets a response (success/failure).
- **Partition Tolerance (P)**: The system remains functional even if network partitions occur.

In distributed databases, **partition tolerance is a must**, meaning we must choose between:
- **CP (Consistency + Partition Tolerance)**: Prioritizes **data accuracy** over availability.
- **AP (Availability + Partition Tolerance)**: Ensures the system stays **responsive**, even if data is stale.

---

## **2. Key Components of CAP**
### **Partition Tolerance (P)**
- The system continues operating **despite network failures**.
- Required in **distributed systems** where nodes are spread across different locations.

### **Consistency (C)**
- Every node sees the **same data** at any time.
- If an update happens on one node, all nodes must reflect the change **before returning a response**.

### **Availability (A)**
- Every request gets a **valid response**, even during failures.
- Some nodes may return outdated (stale) data.

---

## **3. Choosing Between Consistency vs Availability**
### **When to Prioritize Availability (AP)**
- Used in **social media, content delivery, and e-commerce**.
- Example: Twitter’s follower count may **lag** but the system remains **available**.

### **When to Prioritize Consistency (CP)**
- Used in **banking, financial systems, healthcare**.
- Example: A bank transaction **must be correct**, even if it delays the response.

---

## **4. CAP Theorem Visualization**
A distributed system **cannot have all three properties** at once:

Partition Tolerance (P) --- Availability (A)

- **CP (Consistency + Partition Tolerance)**: Ensures correctness but sacrifices availability.
- **AP (Availability + Partition Tolerance)**: Ensures availability but data might be inconsistent.

---
## What is parameterized statement (in Java it's prepared statement)? How does it work **internally**?

A parameterized statement is a statement that we can customize, like changing filter conditions.

## What is SQL injection? How to avoid it?

SQL injection is when the attacker sends a parameter that contains SQL queries and hopes this query will be executed in the system's database.
SQL injection can be avoided by escaping special characters in the params that users submit to the system.

## How many "requests" you have to send to Database in a single prepared statement query? // one for compile and one for execute

It includes 2 steps, compile and execute.

First, the database management system will compile the statement template then store the result without executing it.

Next, the necessary values will be bound into the compiled statement template, then the statement will be executed.

## Can you reuse the compiled query multiple times? (does it help to speed up your application?)

Yes, we can reuse the compiled query multiple times with different bound values. And yes it speeds up the application.

## How indexing works internally?

Indexing adds a data structure with columns for the search conditions and a pointer. The pointer is the address on the memory disk of the row with the rest of the information. The query looks for the specific row in the index; the index refers to the pointer which will find the rest of the information.

## What algorithm and data structure indexing used? And why?

Index can use a hash table or B-Tree. [The index is decided to use depending on the data type and how you query the data](https://dev.mysql.com/doc/refman/8.0/en/index-btree-hash.html).

## How composite indexing works?

It's like a single index but the key is a compound key, which is the combined values of these columns that are indexed.

Let’s say we have an index defined on 4 columns — col1, col2, col3, col4. With a composite index, we have search capability on col1, (col1, col2) , (col1, col2, col3) , (col1, col2, col3, col4). So we can use any left side prefix of the indexed columns, but we can’t omit a column from the middle & use that like — (col1, col3) or (col1, col2, col4) or col3 or col4 etc. These are invalid combinations.

## How to know your query is using index?

By adding "explain" in front of the query, we will get how the query ran and if it uses index or not.

The query optimizer will use index automatically if possible and chose the index that it thinks is the best. And we can force our query to use an index by specifying it in the query.

## How index work in this case: `WHERE age = 5` and `Where age > 5`? The complexity to go to the next record?

It depends on the index type is hashing or B-Tree. If the index type is B-Tree, there is no difference between these two cases. The complexity to go to the next record is O(1) because all the leaves are linked together as a doubly linked list.

## Indexing with char?

For text fields, index on that column only is used when filter condition filters a prefix of this column.

## The complexity of SQL query? How to measure it? How SQL optimize a query?

To be defined.

## Compare `WHERE id = 'a' AND id = 'b' AND id = 'c'` vs `WHERE id in (a, b, c)`?

In MySQL, the values in the IN list will be sorted and MySQL will use binary search to check if the current value is in the list. So the check operator will cost O(log N) complexity, with N being the number of elements that need to be compared.

Some links to read more:

- https://www.postgresql.org/message-id/12553.1135634231@sss.pgh.pa.us

- https://www.cybertec-postgresql.com/en/postgresql-indexing-index-scan-vs-bitmap-scan-vs-sequential-scan-basics/

- https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781785284335/11/ch11lvl1sec104/running-bitmap-heap-and-index-scan

- https://www.oreilly.com/library/view/high-performance-mysql/9780596101718/ch04.html"

## Complexity of this query `SELECT * FROM abc ORDER BY name LIMIT 10 OFFSET 1000000` // SELECT 10 record from offset 10^6 after sort by name (which is a char)? How to optimize it?

O(N log N) with N being the total number of records.

We can optimize the query by indexing the column name.

## What is the complexity of COUNT(\*) query?

In MySQL, it depends on the storage engine that the table uses. For MyISAM, the total number of rows is stored with each table so the complexity is O(1), but for InnoDB, it is O(N).

## How to write query to avoid full table scan?

We need to write a query that takes advantage of index if any.

## Complexity of JOIN, INNER JOIN, OUTER JOIN?

## ✅ Short Answer: JOIN & Complexity (for Interview)

**Q: Giải thích các loại JOIN và độ phức tạp?**

- `INNER JOIN`: lấy các dòng có match ở cả hai bảng.
- `LEFT JOIN`: lấy tất cả từ bảng bên trái, nếu không khớp thì điền `NULL`.
- `RIGHT JOIN`: như LEFT nhưng là bảng bên phải.
- `FULL OUTER JOIN`: lấy tất cả từ cả hai bảng, chỗ không match điền `NULL`.

**Complexity (tùy strategy DBMS chọn):**
- `Hash Join`, `Merge Join`: **O(n + m)** (nhanh, cần index hoặc sort).
- `Nested Loop Join`: **O(n × m)** (nếu không có index).
- `FULL OUTER JOIN`: tốn memory hơn vì merge + NULL padding.

**Tối ưu JOIN bằng cách:**
- Index đúng cột join.
- Tránh join nhiều bảng không cần thiết.
- Dùng `EXPLAIN` để kiểm tra execution plan.

`INNER JOIN` lấy dòng trùng giữa hai bảng, nếu có index thì nhanh O(n + m), nếu không thì O(n × m).
`LEFT/RIGHT JOIN` giống `INNER JOIN` nhưng giữ tất cả dòng từ một phía, thêm `NULL` nếu không có match.
`FULL OUTER JOIN` lấy hết từ cả hai bảng, tốn RAM và CPU hơn.
Tối ưu bằng cách đặt index lên cột `JOIN`, tránh `JOIN` nhiều bảng không cần thiết.

## What is Database Replicating? When do we need it?

Database replicating is the act of making one or many copies of a database to increase the availability of data.

## What is bin log? How Master DB sync with Slave DB?

When data is written into DB, first it will be stored in Write Ahead Logs (bin logs). Master DB send these log to Slave DB so Slave DB can also have those changes.

## Can a Slave DB be a slave of another Slave DB (we do not need to sync from Master DB directly)?

Yes, a Slave DB can be a slave of another Slave DB, a setup known as cascading replication or chained replication. In this architecture, a secondary (slave) database receives updates from the primary (master) database, and then another tertiary (slave) database replicates from this secondary slave instead of the master. This approach reduces the load on the master database and distributes replication traffic efficiently, especially in large-scale systems. However, it introduces additional replication lag since updates must propagate through multiple layers. This is commonly used in MySQL, PostgreSQL, and MongoDB replica sets to improve scalability and fault tolerance.

## What is Database Sharding? When we need it?

Sharding happens when a database is too big so the query time increases. We shard database to improve query time and also increase the availability of the system. If one shard is down, there is only a portion failure.

### Type of sharding
- Range-based sharding
- Hash-bsed sharding
- Geographical sharding
- Directory sharding
## Which rule we can apply to DB Sharding?

- Ensure unique key for the whole system.

- Distribute load.

## How to ensure primary key is globally unique when sharding?

We can have a key generating system that generates keys for all servers. But this server can be a central point of failure. To improve that, we can have multiple servers to generate keys, each server is in charge of some range of keys.

## How we can shard a table to multiple tables (same server) and multiple DB (multiple servers)?

Sharding a table across multiple tables (on the same server) or multiple databases (across multiple servers) can be done using horizontal partitioning, where rows are distributed based on a sharding key. 

- On the same server, sharding can be implemented by creating multiple tables (e.g., users_1, users_2), each holding a subset of data, with queries routed based on a hashing/modulo function of the sharding key (e.g., user_id % 2). 

- For multi-server sharding, data is partitioned across different databases on separate servers, where a shard lookup service (or consistent hashing) determines which server holds the required data. This improves performance and scalability but requires careful handling of cross-shard queries, joins, and resharding when scaling out. Commonly used in MySQL (ProxySQL), PostgreSQL, and NoSQL databases like MongoDB and Cassandra.
## How query can work when we sharding (the data might be in the same or different tables/dbs)?

For a query that we might know exactly where data is using its key, we can detect which shard is holding necessary data and query on this shard.

For a query that data might be on a lot of shards, we can execute the query on all possible shards then aggregate data later.

## What is database transaction?

Relational DB ensures Atomicity by Transaction. A transaction is a collection of operations. Either all of those operations are executed, or non of them is executed. It ensures there is no time when something is failed in the middle and we have no idea when or how to roll back.

## How rollback works internally?

# **How Rollback Works Internally?**

## **What is a Rollback?**
A **rollback** is the process of undoing changes made during a transaction if an error or failure occurs, ensuring the database remains in a consistent state.

## **Rollback Mechanism in Databases**
Rollbacks are managed using **ACID (Atomicity, Consistency, Isolation, Durability)** properties in relational databases.

### **1. Transaction Lifecycle**
A transaction follows these steps:
1. **BEGIN TRANSACTION** → Start a new transaction.
2. **Perform Operations** → Insert, Update, Delete, etc.
3. **COMMIT** → Permanently save changes if successful.
4. **ROLLBACK** → Undo all changes if an error occurs.

### **2. How Rollback Works Internally**
- **Transaction Log (WAL - Write-Ahead Logging)**:  
  - Before executing any change, the DBMS logs it in the **redo/undo logs**.
  - If a failure happens, the undo log is used to restore the previous state.
  
- **Savepoints**:  
  - Intermediate points allow partial rollback instead of rolling back the entire transaction.

- **Locking Mechanisms**:  
  - Ensures other transactions do not read/write uncommitted data.

### **3. Example: SQL Rollback**
```sql
BEGIN TRANSACTION;

UPDATE accounts SET balance = balance - 500 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 500 WHERE account_id = 2;

-- Suppose an error occurs here
ROLLBACK; -- Undo all updates

SELECT * FROM accounts; -- No changes will be reflected
```

## What is dirty read, dirty write, read skew, phantom read, write skew, lost update?

Dirty read is when transaction A is allowed to read a value that has been modified by transaction B but has yet been committed. This may cause a problem when transaction B fails and rollback happens, so the value that A reads is not the value in database.

Dirty write is when transaction A is allowed to write a value that has been modified by transaction B but has yet been committed.

Read skew is when someone needs to read one value multiple times in a transaction. But the result each time is different.

Phantom read is when a transaction needs to get a collection of records multiple times in a transaction. But the number of records each time is different.

Write skew is when someone needs to read some object then update some object based on the result of the read value.

Lost update is a special case of write skew when read and update happen with the same object.

Read committed is a weak isolation level that is used to guarantee no dirty read and no dirty write. It assures that all object that can be read is already committed and no write can happen to an uncommitted object.

2 stronger isolation levels are snapshot isolation and serializable snapshot isolation. For snapshot isolation, a transaction can read the old version of a value while other transactions are writing. For serializable snapshot isolation, a transaction cannot read while other transactions are writing. It is called 2PL - two-phase locking.

## How transaction work when there are many concurrent requests?

- When many concurrent requests happen, transactions use isolation levels and locking mechanisms to ensure data integrity. The database transaction manager handles these requests using ACID properties (Atomicity, Consistency, Isolation, Durability). Isolation is managed through techniques like pessimistic locking (locking rows until a transaction completes) or optimistic locking (checking for conflicts before committing). 
- Different isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE) control how transactions see uncommitted changes from others. If conflicts occur (e.g., two transactions updating the same row), the database may rollback one of them or use MVCC (Multi-Version Concurrency Control) to allow non-blocking reads. This ensures that concurrent transactions execute safely without corrupting data.

## How to avoid race condition in DB? Read/Write lock?

To be defined.

## Distributed transaction? How to make a transaction when a query needs to access multiple DB?

A distributed transaction is needed when data that suppose to be in a transaction is stored in different databases. There is a technique called 2 phase commit (2PC) that helps to resolve this situation.

There will be a coordinator which orchestrates the whole transaction. When the transaction begins, the coordinator generates a global transaction ID for all participants of this transaction. Each participant will execute their own transaction that attaches to the global transaction. When all local transactions are done, the coordinator sends the prepare request for all participants. This is the first phase - the prepare phase in 2PC. Participants can respond yes or no, indicate if they can commit this transaction or not. And the "yes" answer is the promise that it will be able to commit this transaction no matter what. If all participants respond "yes", the coordinator can decide to commit this transaction and send the commit request to all participants. This is the second phase of 2PC, the commit phase. Once the commit decision is made by the coordinator, it is irreversible. It will keep resending commit requests to participants no matter what. If the coordinator fails, the participant must keep the status of being able to commit this global transaction no matter what.

## What is Try-Confirm Cancel?

To be defined.
# **Databases vs Object Storage - Key Differences**

## **1. Databases**
- Stores **structured data** (e.g., text, numbers, relationships).
- Uses **schemas, tables, rows, and indexes** for efficient querying.
- Best for **transactional data, user records, financial data**.
- **Not optimized** for storing large files like images/videos.
- Examples: **MySQL, PostgreSQL, MongoDB**.

## **2. Object Storage**
- Stores **unstructured data** as **objects** (data + metadata + unique ID).
- **No hierarchical structure** (unlike file systems).
- Scales easily due to its **flat architecture**.
- Used for **large files like images, videos, backups**.
- Accessed via **HTTP requests**, not direct queries.
- Examples: **AWS S3, Google Cloud Storage, Azure Blob Storage**.

---

## **3. Why Use Object Storage?**
- ✅ **Efficient for large files** (images, videos, backups).
- ✅ **Highly scalable** without hierarchical complexity.
- ✅ **Lower database load** by offloading large data storage.

### **Best Practice:**  
Store metadata (file reference) in a **database** and the actual file in **object storage**.

**Example Use Case:**  
A social media app stores user profiles in **PostgreSQL** and images in **AWS S3**.

| Feature | Database | Object Storage |
|---------|---------|---------------|
| **Data Type** | Structured | Unstructured |
| **Scalability** | Limited | Highly Scalable |
| **Optimized For** | Queries & Transactions | Large File Storage |
| **Access** | SQL Queries | HTTP API Calls |
| **Examples** | MySQL, MongoDB | AWS S3, Google Cloud Storage |

---

## **Conclusion**
- **Use databases** for **structured, transactional data**.
- **Use object storage** for **large, unstructured files** (videos, backups, etc.).
- Combining both ensures **optimized performance and cost-efficiency**.
