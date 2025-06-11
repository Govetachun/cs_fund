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

“I measure SQL complexity using EXPLAIN to inspect the query plan and cost. Complexity is mainly driven by how many rows are processed, how joins are done, and whether indexes are used. SQL optimizers try to minimize cost using techniques like index usage, join reordering, and filter pushdown. I always check query plans and tune indexes or rewrite queries for better performance.”

## Compare `WHERE id = 'a' AND id = 'b' AND id = 'c'` vs `WHERE id in (a, b, c)`?

In MySQL, the values in the IN list will be sorted and MySQL will use binary search to check if the current value is in the list. So the check operator will cost O(log N) complexity, with N being the number of elements that need to be compared.


## Complexity of this query `SELECT * FROM abc ORDER BY name LIMIT 10 OFFSET 1000000` // SELECT 10 record from offset 10^6 after sort by name (which is a char)? How to optimize it?

O(N log N) with N being the total number of records.

We can optimize the query by indexing the column name.

## What is the complexity of COUNT(\*) query?

In MySQL, it depends on the storage engine that the table uses. For MyISAM, the total number of rows is stored with each table so the complexity is O(1), but for InnoDB, it is O(N).

## How to write query to avoid full table scan?

We need to write a query that takes advantage of index if any.

## Complexity of JOIN, INNER JOIN, OUTER JOIN?


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

##
- `INNER JOIN` lấy dòng trùng giữa hai bảng, nếu có index thì nhanh O(n + m), nếu không thì O(n × m).
- `LEFT/RIGHT JOIN` giống `INNER JOIN` nhưng giữ tất cả dòng từ một phía, thêm `NULL` nếu không có match.
- `FULL OUTER JOIN` lấy hết từ cả hai bảng, tốn RAM và CPU hơn.
- Tối ưu bằng cách đặt index lên cột `JOIN`, tránh `JOIN` nhiều bảng không cần thiết.

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


## 🔒 What Is an Isolation Level?

> Isolation level controls **how much a transaction can see/interfere with others**.
> It balances:

* ✅ **Data consistency**
* ✅ **Performance / concurrency**

---

## 🧪 SQL Isolation Levels (From Weakest to Strongest)

| Level                | What It Guarantees                                            | What It Allows (Trade-off)                     |
| -------------------- | ------------------------------------------------------------- | ---------------------------------------------- |
| **READ UNCOMMITTED** | Can read uncommitted (dirty) data                             | ❌ Dirty reads, ❌ non-repeatable reads          |
| **READ COMMITTED**   | Can only read committed data                                  | ❌ Non-repeatable reads, ✅ prevents dirty reads |
| **REPEATABLE READ**  | Rows read stay consistent during the transaction              | ❌ Phantom reads possible                       |
| **SERIALIZABLE**     | Fully isolated (acts like transactions run one after another) | ❌ Poor concurrency, high locking               |

---

## 🔍 Dirty Read / Non-repeatable / Phantom — What Are They?

| Problem            | Example                                                                                           |
| ------------------ | ------------------------------------------------------------------------------------------------- |
| **Dirty Read**     | T1 reads data changed by T2 that isn't committed yet                                              |
| **Non-repeatable** | T1 reads a row, T2 updates it, T1 reads again → sees different data                               |
| **Phantom Read**   | T1 queries rows with `WHERE x > 10`, T2 inserts a new row matching it, T1 reruns → gets extra row |

---

## ✅ Isolation Levels with Examples

### 1. **READ UNCOMMITTED**

> Fastest, least safe. **Can read dirty data.**

```sql
-- T1: UPDATE accounts SET balance = balance - 100 WHERE id = 1;
-- T2: SELECT * FROM accounts;  -- Sees uncommitted balance
```

* 🔥 Use: Never in production unless you **don’t care about consistency** (e.g., log streaming)
* ✅ Max concurrency, ❌ max risk

---

### 2. **READ COMMITTED** (Default in PostgreSQL, Oracle)

> Only reads **committed** data. Each query sees latest committed data.

```sql
-- T1 reads, then T2 commits a change, then T1 reads again → new value!
```

* ✅ Avoids dirty reads
* ❌ Non-repeatable reads possible
* ⚡ Good default for read-heavy systems (e.g., dashboards)

---

### 3. **REPEATABLE READ** (Default in MySQL InnoDB)

> All reads **within a transaction are consistent** (as of first read).

```sql
-- T1 reads row → T2 updates it → T1 re-reads → sees old version
```

* ✅ Prevents dirty + non-repeatable reads
* ❌ Allows phantom reads (INSERTs not detected)
* ⚡ Best for **banking, inventory**: reads must not change mid-transaction

---

### 4. **SERIALIZABLE**

> Transactions run as if **completely sequential** (full isolation)

* ✅ Most consistent
* ❌ Worst concurrency (lots of locks or aborts)
* ⚠️ Can cause contention in high-throughput apps

---

## 🔄 Summary Table

| Level            | Dirty Read | Non-Repeatable | Phantom Read | Performance | Use Case                              |
| ---------------- | ---------- | -------------- | ------------ | ----------- | ------------------------------------- |
| READ UNCOMMITTED | ✅ Yes      | ✅ Yes          | ✅ Yes        | 🚀 Fastest  | Rare, debug only                      |
| READ COMMITTED   | ❌ No       | ✅ Yes          | ✅ Yes        | ✅ Good      | Dashboards                            |
| REPEATABLE READ  | ❌ No       | ❌ No           | ✅ Yes        | ⚠️ Moderate | Banking, counters                     |
| SERIALIZABLE     | ❌ No       | ❌ No           | ❌ No         | 🐢 Slow     | Financial ledgers, legal transactions |

---

## 🔧 Best Practices

| Goal                            | Recommended Level  |
| ------------------------------- | ------------------ |
| Max speed, tolerate dirty reads | `READ UNCOMMITTED` |
| Good balance for most cases     | `READ COMMITTED`   |
| Consistent reads during tx      | `REPEATABLE READ`  |
| Absolute correctness required   | `SERIALIZABLE`     |

---

## 🧠 Bonus: PostgreSQL vs MySQL Defaults

| DB             | Default Level   |
| -------------- | --------------- |
| PostgreSQL     | READ COMMITTED  |
| MySQL (InnoDB) | REPEATABLE READ |



## How transaction work when there are many concurrent requests?

- When many concurrent requests happen, transactions use isolation levels and locking mechanisms to ensure data integrity. The database transaction manager handles these requests using ACID properties (Atomicity, Consistency, Isolation, Durability). Isolation is managed through techniques like pessimistic locking (locking rows until a transaction completes) or optimistic locking (checking for conflicts before committing). 
- Different isolation levels (READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE) control how transactions see uncommitted changes from others. If conflicts occur (e.g., two transactions updating the same row), the database may rollback one of them or use MVCC (Multi-Version Concurrency Control) to allow non-blocking reads. This ensures that concurrent transactions execute safely without corrupting data.
Here's a structured deep dive into **how transactions work under concurrent requests**, with emphasis on ACID properties, isolation levels, and concurrency control mechanisms:

### 🔁 How Transactions Work Under Concurrent Requests

### 1. **Transaction Lifecycle and ACID Guarantees**

Every database transaction follows the **ACID** principles:

* **Atomicity**: All or nothing – either all changes are committed, or none are.
* **Consistency**: The DB remains valid after transaction.
* **Isolation**: Concurrent transactions don’t interfere.
* **Durability**: Once committed, changes persist even if the system crashes.

---

### 2. **Concurrency and Isolation**

When **multiple transactions run at the same time**, the database must isolate them to avoid:

* **Dirty reads** (reading uncommitted data),
* **Non-repeatable reads** (reading different values in the same transaction),
* **Phantom reads** (rows appearing/disappearing between queries).

This is managed by **isolation levels**:

| Isolation Level      | Dirty Read | Non-Repeatable Read | Phantom Read |
| -------------------- | ---------- | ------------------- | ------------ |
| **READ UNCOMMITTED** | ✅          | ✅                   | ✅            |
| **READ COMMITTED**   | ❌          | ✅                   | ✅            |
| **REPEATABLE READ**  | ❌          | ❌                   | ✅            |
| **SERIALIZABLE**     | ❌          | ❌                   | ❌            |

* Higher isolation → fewer anomalies but worse performance (more locking/blocking).

---

### 3. **Concurrency Control Mechanisms**

#### a. **Pessimistic Locking**

* Lock acquired **before** data is read or written.
* Prevents other transactions from accessing the data until lock is released.
* Good for high-conflict environments.

```sql
-- Example: SELECT ... FOR UPDATE (locks rows)
BEGIN;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```

#### b. **Optimistic Locking**

* No locks during reads. Data is modified if **version/token** hasn’t changed.
* Conflict detection at commit.
* Suitable for low-conflict, high-read workloads.

```sql
-- Uses versioning
UPDATE accounts
SET balance = balance - 100, version = version + 1
WHERE id = 1 AND version = 5;
```

#### c. **MVCC (Multi-Version Concurrency Control)**

* Readers don’t block writers, and writers don’t block readers.
* Each transaction sees a **snapshot** of the DB.
* Widely used in **PostgreSQL**, **MySQL InnoDB**, **Oracle**.

```text
Transaction A           Transaction B
--------------          --------------
Read balance = 100      
                        UPDATE balance = 90
                        COMMIT
Read balance = 100 (sees old snapshot)
```

---

### 4. **Conflict Resolution & Rollback**

* If two transactions update the same row:

  * **Pessimistic**: Second one waits (or deadlock detected & rolled back).
  * **Optimistic**: Second fails on commit (version mismatch).
  * **MVCC**: Reader doesn’t see uncommitted write; writer may conflict at commit time.

---

### 5. **Best Practices**

* Use **lower isolation** (e.g., `READ COMMITTED`) for better performance unless strong consistency is needed.
* Use **retry logic** for transient failures (e.g., optimistic lock version mismatch).
* Monitor and tune for **deadlocks**, **lock contention**, and **long-running transactions**.




## How to avoid race condition in DB? Read/Write lock?

To avoid race conditions in a DB, I use transactions and either pessimistic locking (SELECT ... FOR UPDATE) or optimistic locking (with a version column) depending on conflict likelihood. I may also adjust isolation levels (like using SERIALIZABLE for critical operations) or leverage MVCC in systems like PostgreSQL to allow safe concurrent access without blocking readers.

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

## Đánh index nhiều bị gì?
Đánh nhiều index sẽ làm chậm thao tác ghi, tốn dung lượng, query planner dễ chọn sai index, làm tăng chi phí bảo trì và có thể gây lock contention. Chỉ nên đánh index cho các cột thực sự cần thiết để tối ưu truy vấn thường dùng.

## Dựa vào dữ kiện gì để chọn đánh index 1 cột, có đánh foreign key không?
Chọn cột để đánh index dựa trên tần suất xuất hiện trong WHERE, JOIN, ORDER BY, GROUP BY và độ selectivity cao. Đánh index trên foreign key giúp tối ưu truy vấn join và đảm bảo ràng buộc toàn vẹn dữ liệu, thường phải tự tạo index trên foreign key ở bảng con.

## Có nên đánh index trên FOREIGN KEY không?
A. Foreign Key là gì?
Là ràng buộc giữa 2 bảng, đảm bảo tính toàn vẹn dữ liệu (một trường của bảng A phải tham chiếu giá trị tồn tại ở bảng B).

B. Index trên FOREIGN KEY có cần không?
Nên đánh index trên cột là foreign key.

Lý do:

- Giúp truy vấn nhanh hơn khi thực hiện JOIN giữa 2 bảng (parent & child).

- Giúp thực hiện các thao tác xóa/cập nhật (ON DELETE, ON UPDATE) nhanh hơn và tránh full table scan.

- Một số DBMS (MySQL InnoDB) tự động tạo index trên primary key của bảng cha, nhưng không tự động trên foreign key của bảng con (bạn phải tự tạo)

## Các cách tối ưu query ngoài index
- Refactoring query (tránh SELECT *, hạn chế hàm trên cột, dùng EXISTS,…)
- Tối ưu JOIN / Subquery (chọn đúng loại JOIN, replace nested subquery, CTE)
- Partitioning (theo range, hash)
- Materialized View / Denormalization (tạo bảng tóm tắt, tóm lược kết quả)
- Caching (Redis, Memcached; query cache)
- Thống kê và EXPLAIN (ANALYZE, EXPLAIN ANALYZE để hiểu execution plan)
- LIMIT / Pagination (keyset pagination)
- Thiết kế schema (Balance normalization vs. denormalization, composite index, thứ tự cột)
- Hardware & cấu hình DB (RAM, SSD, buffer pool, các tham số)
- Monitoring & Profiling (slow query log, Performance Schema, pg_stat_statements)

## Biến subquery thành bảng tạm thế nào (để tối ưu query)
#### Khi nào nên dùng?

- Subquery phức tạp, lồng nhiều tầng, hoặc dùng nhiều lần trong 1 truy vấn JOIN/phép tính.
- Subquery đó phải scan nhiều dữ liệu, join nhiều bảng hoặc có phép tính nặng (aggregation).
- Tránh việc DBMS thực thi lặp lại subquery nhiều lần (với correlated subquery hoặc re-use subquery).

#### Lợi ích:

- Giảm tải cho planner.
- Có thể tạo index trên bảng tạm.
- Tăng hiệu năng, dễ debug.

Để tối ưu subquery phức tạp hoặc tái sử dụng nhiều lần, bạn chuyển subquery thành bảng tạm qua CREATE TEMPORARY TABLE ... AS (subquery), sau đó dùng bảng tạm này trong các truy vấn tiếp theo. Có thể tạo index trên bảng tạm nếu cần, giúp tăng hiệu năng và dễ bảo trì.

### TRUNCATE vs DELETE
- `DELETE` dùng để xóa từng hàng (có/không điều kiện), log đầy đủ, giữ `AUTO_INCREMENT`, kích hoạt trigger, có thể rollback.
- `TRUNCATE` dùng để xóa nhanh toàn bộ bảng, minimal logging, reset `AUTO_INCREMENT`, không kích hoạt trigger, thường không thể thực thi nếu còn FK, và đôi khi auto-commit (tùy engine).

### Cho 1 bảng có id và parent_id,  hỏi cách query đệ quy ra được tổ tiên id
Để truy vấn tất cả tổ tiên (ancestor) của 1 node trong bảng có id/parent_id, sử dụng CTE đệ quy với WITH RECURSIVE (hỗ trợ ở PostgreSQL, SQL Server, MySQL 8+). Bắt đầu từ node cần tìm, truy ngược parent qua join lặp lại, trả về danh sách id tổ tiên cho node đó.

```SQL
WITH RECURSIVE ancestors AS (
    -- Bước 1: Lấy node bắt đầu
    SELECT id, parent_id
    FROM categories
    WHERE id = 6  -- Thay bằng node bạn cần tìm

    UNION ALL

    -- Bước 2: Mỗi vòng truy ngược lên cha
    SELECT c.id, c.parent_id
    FROM categories c
    INNER JOIN ancestors a ON c.id = a.parent_id
)
SELECT * FROM ancestors
WHERE id != 6;  -- Loại bỏ node gốc (nếu chỉ lấy tổ tiên)
```
# Index vs. Clustered Index

## 🔹 1. Definition

| Index Type              | Description                                                                                                                                                         |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Clustered Index**     | Data rows in the table are **physically stored in order** of the index. A table can have **only one** clustered index.                                              |
| **Non-Clustered Index** | Stores pointers (row locators) to the actual data rows. Data is **not sorted physically** by this index. You can have **multiple** non-clustered indexes per table. |

---

## 🔹 2. Pros & Cons

### ✅ Clustered Index

| Pros                                  | Explanation                                                                             |
| ------------------------------------- | --------------------------------------------------------------------------------------- |
| 🔸 **Fast range queries**             | Since data is sorted, range scans (e.g., `BETWEEN`, `ORDER BY`, `LIMIT`) are efficient. |
| 🔸 **Faster for primary key lookups** | Data is stored in the same order as the primary key, reducing I/O.                      |
| 🔸 **Efficient I/O**                  | Sequential reads are faster due to page locality.                                       |

| Cons                          | Explanation                                                                |
| ----------------------------- | -------------------------------------------------------------------------- |
| 🔹 **Insert/update overhead** | New rows may need reordering to maintain sort order, causing page splits.  |
| 🔹 **Only one allowed**       | Limits indexing strategies — can't cluster by multiple columns.            |
| 🔹 **Slower random inserts**  | Especially with GUIDs or random IDs as primary key (non-monotonic values). |

---

### ✅ Non-Clustered Index

| Pros                               | Explanation                                                                 |
| ---------------------------------- | --------------------------------------------------------------------------- |
| 🔸 **Multiple indexes allowed**    | You can create many indexes tailored to different queries.                  |
| 🔸 **No impact on data layout**    | Doesn’t affect physical row storage order.                                  |
| 🔸 **Useful for covering indexes** | Index can store additional columns (include clause) to avoid table lookups. |

| Cons                                    | Explanation                                                                |
| --------------------------------------- | -------------------------------------------------------------------------- |
| 🔹 **Extra lookup (bookmark lookup)**   | Fetching data requires indirection, especially if index is not "covering". |
| 🔹 **Less efficient for range queries** | No benefit from data being unsorted.                                       |
| 🔹 **Higher storage overhead**          | Requires additional disk space for each non-clustered index.               |

---

## 🔹 3. Performance Considerations

| Query Type                          | Best Index Type                                     |
| ----------------------------------- | --------------------------------------------------- |
| Primary key or unique lookups       | **Clustered Index**                                 |
| Range queries (e.g., dates)         | **Clustered Index**                                 |
| Joins or WHERE with non-key columns | **Non-Clustered Index** (possibly covering)         |
| Analytical read-heavy queries       | Mix: Clustered for sort + Non-clustered for filters |

---

## 🔹 4. Visualization (Conceptual)

```text
[Clustered Index - Data is sorted with the index]

    Index:       Data:
    101          Row for 101
    102          Row for 102
    103          Row for 103
    ...          ...

[Non-Clustered Index - Index points to data in heap/table]

    Index:        Pointer:
    103           → Row Page 7, Slot 3
    101           → Row Page 3, Slot 1
    102           → Row Page 5, Slot 2
```

---

## 🔹 5. Real-World Example (SQL Server / MySQL)

```sql
-- Clustered Index (usually on Primary Key)
CREATE TABLE Orders (
    OrderID INT PRIMARY KEY,  -- Implicit clustered index
    CustomerID INT,
    OrderDate DATE
);

-- Non-Clustered Index
CREATE INDEX idx_customer_date ON Orders (CustomerID, OrderDate);
```

---

## 🔹 6. Best Practices

* Use **clustered index** on columns that:

  * Are **frequently searched**, **range-filtered**, or **sorted**
  * Are **monotonically increasing** (e.g., `AUTO_INCREMENT`, `created_at`)
* Use **non-clustered index** to:

  * Support **alternate query paths**
  * Optimize **JOINs**, **WHERE filters**, or **SELECT projections**
* Consider **covering indexes** (with `INCLUDE` or extra columns) to avoid lookups

---

## 🔹 7. Related Concepts

| Concept        | Clustered Index                 | Non-Clustered Index                   |
| -------------- | ------------------------------- | ------------------------------------- |
| Heap Table     | ❌ No index, just unordered rows | ✅ Underlying layout for non-clustered |
| Covering Index | ❌ Usually not possible          | ✅ Common and useful                   |
| Index Scan     | ✅ Efficient for ranges          | ✅ Works but may need extra reads      |

---

## 🔹 Summary Table

| Feature                | Clustered                       | Non-Clustered               |
| ---------------------- | ------------------------------- | --------------------------- |
| Data ordering          | Yes                             | No                          |
| # of indexes           | 1                               | Many                        |
| Storage overhead       | Lower                           | Higher                      |
| Lookup cost            | Low (direct)                    | Higher (needs pointer)      |
| Insert/update overhead | Higher                          | Lower                       |
| Best for               | Range scans, primary key lookup | Filtering, covering indexes |


When **designing a database**, choosing which columns to index is critical for query performance, especially for **read-heavy** or **analytical systems**. Below is a **step-by-step guide**, followed by **column selection criteria**, **index types**, and **best practices**.

---

## ✅ Choose Columns to Index

### Step 1: **Understand Your Workload**

* 🔹 OLTP (transactions): many small reads/writes → index should support point lookups and UPDATE efficiency.
* 🔹 OLAP (analytics): large scans, aggregations → indexes should support GROUP BY, JOINs, filters.

### Step 2: **Examine Query Patterns**

Ask:

* What columns appear in `WHERE`, `JOIN`, `ORDER BY`, or `GROUP BY` clauses?
* What are the most frequent queries? Use `EXPLAIN` or logs to profile them.

### Step 3: **Choose Index Types**

* **Primary Key (Clustered Index)** → always indexed
* **Foreign Keys** → usually good candidates
* **Filter Conditions (WHERE)** → especially for high-selectivity columns
* **Join Columns** → frequently used in JOINs
* **Sorting / Range** → e.g., `ORDER BY created_at`
* **Covering Indexes** → include columns used in `SELECT`

---

## 🔍 Indexing Decision Table

| Column Use                              | Index?                    | Reason                                   |
| --------------------------------------- | ------------------------- | ---------------------------------------- |
| Primary key                             | ✅ Yes (automatically)     | Fast unique lookup                       |
| Foreign key                             | ✅ Yes                     | Efficient JOIN and referential integrity |
| Column in `WHERE` with high selectivity | ✅ Yes                     | Narrows rows quickly                     |
| Column in `JOIN`                        | ✅ Yes                     | Speeds up join lookup                    |
| Column in `ORDER BY` / `GROUP BY`       | ✅ Yes                     | Avoids sorting                           |
| Frequently updated field                | ⚠️ Maybe not              | Index maintenance overhead               |
| Low-cardinality column (e.g. gender)    | ❌ No (or bitmap index)    | Index not useful                         |
| Computed expressions                    | ⚠️ Use functional indexes | e.g., `LOWER(email)`                     |

---

## 🧠 Selectivity Rule of Thumb

> **Selectivity = # of distinct values / total rows**

| Selectivity                       | Index Suitability     |
| --------------------------------- | --------------------- |
| High (e.g., email, user\_id)      | ✅ Excellent candidate |
| Medium (e.g., zip code, category) | ⚠️ Depends on usage   |
| Low (e.g., gender, boolean)       | ❌ Usually not helpful |

---

## 🛠 Example Scenarios

### Example 1: **E-commerce Orders Table**

```sql
CREATE TABLE orders (
  order_id     BIGINT PRIMARY KEY,
  user_id      BIGINT,
  product_id   BIGINT,
  status       VARCHAR(20),
  created_at   TIMESTAMP,
  updated_at   TIMESTAMP
);
```

Recommended indexes:

```sql
-- Lookup by user
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- Query recent orders
CREATE INDEX idx_orders_created_at ON orders(created_at DESC);

-- Filter by status (if highly selective)
CREATE INDEX idx_orders_status ON orders(status);

-- JOIN optimization
CREATE INDEX idx_orders_product_id ON orders(product_id);
```

### Example 2: **Search by name or email**

```sql
-- If frequent text search
CREATE INDEX idx_users_lower_email ON users (LOWER(email));
```

---

## 🧱 Composite (Multi-Column) Index Tips

Use composite indexes **when queries filter on multiple columns**.

```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

Rules:

* The order of columns **matters** (left-most prefix rule)
* Best when used with multiple filters: `WHERE user_id = ? AND status = ?`
* Avoid if only filtering by the second column (`status`) alone

---

## 📉 Don’t Over-Index

* **Too many indexes slow down writes** (INSERT/UPDATE/DELETE must update all indexes)
* **Duplicates** and **unused indexes** waste space
* Always **monitor usage** with `pg_stat_user_indexes` (PostgreSQL), `SHOW INDEX` (MySQL), or `sys.dm_db_index_usage_stats` (SQL Server)

---

## 🔄 Index Tuning Cycle

1. Profile queries with `EXPLAIN` / `ANALYZE`
2. Add or adjust indexes
3. Measure latency and I/O
4. Drop unused indexes
5. Revisit after schema or workload changes

---

## ✅ Summary Cheat Sheet

| Column Role    | Index?     | Type                      |
| -------------- | ---------- | ------------------------- |
| Primary Key    | ✅ Yes      | Clustered / Unique        |
| Foreign Key    | ✅ Yes      | Standard                  |
| Search Filters | ✅ Yes      | High-selectivity only     |
| Join Fields    | ✅ Yes      | Standard                  |
| Sorting Fields | ✅ Yes      | ASC/DESC as needed        |
| Aggregations   | ✅ Yes      | If used in GROUP BY       |
| Update-heavy   | ⚠️ Caution | Skip if not queried often |
| Boolean/Gender | ❌ No       | Low-cardinality           |

---

Would you like me to analyze a **specific schema** or **real-world query** to suggest indexes?


Here’s a **concise yet practical guide** to crafting a powerful **index strategy in a relational database**, with focus on:

* ✅ **When & what** to index
* ✅ Index **types & patterns**
* ✅ Real-world **query optimization**
* ✅ Best **practices** and **pitfalls**

---

## 🧠 Why Indexing Matters

Indexes **speed up data access**, especially for **WHERE**, **JOIN**, **ORDER BY**, and **GROUP BY** clauses — at the cost of:

* More disk space
* Slower **INSERT/UPDATE/DELETE** (write penalty)

So indexing is about **balance**.

---


#  General Indexing Strategy

### ✅ Index Columns That Are:

| Criteria                                  | Reason                           |
| ----------------------------------------- | -------------------------------- |
| Used in `WHERE`, `JOIN`                   | Faster filtering & joining       |
| Used in `ORDER BY`, `GROUP BY`            | Avoids extra sorting/aggregation |
| Frequently queried                        | High ROI for reads               |
| High **selectivity** (many unique values) | Reduces scanned rows             |

---

## 🛠️ 1. Index Types & When to Use

| Index Type                           | Use Case                                               |
| ------------------------------------ | ------------------------------------------------------ |
| **Primary/Unique Index**             | Enforce uniqueness, fast lookup by key                 |
| **Composite Index**                  | Multi-column filters and joins                         |
| **Partial Index** (e.g., PostgreSQL) | Index only active rows (e.g., `WHERE status='active'`) |
| **Covering Index**                   | Include all queried columns to avoid table access      |
| **Full-Text Index**                  | For `LIKE` / natural language search                   |
| **GIN/GiST/Bitmap**                  | Special cases: array/JSON, fuzzy search                |

---

## 📏 2. Composite Index Strategy

```sql
CREATE INDEX idx_user_status ON users (user_id, status);
```

### 🔑 Left-to-right Rule:

* Query must **start with the left-most** columns of the index.
* Supports:

  * ✅ `WHERE user_id = ...`
  * ✅ `WHERE user_id = ... AND status = ...`
  * ❌ `WHERE status = ...` alone (not fully used)

---

## 🔍 3. Covering Index (read optimization)

> Add columns to the index that appear in `SELECT`, so no table lookup is needed.

```sql
-- SELECT email FROM users WHERE user_id = ?
CREATE INDEX idx_user_id_email ON users (user_id, email);
```

* ✅ Avoids "bookmark lookup"
* ✅ Big win in high-read systems

---

## ⚙️ 4. Real Query Patterns and Matching Indexes

| Query                                           | Recommended Index                     |
| ----------------------------------------------- | ------------------------------------- |
| `SELECT ... WHERE user_id = ?`                  | `INDEX(user_id)`                      |
| `SELECT ... WHERE user_id = ? AND status = ?`   | `INDEX(user_id, status)`              |
| `JOIN orders ON users.user_id = orders.user_id` | Index `user_id` in both tables        |
| `ORDER BY created_at DESC`                      | `INDEX(created_at DESC)`              |
| `SELECT ... WHERE email LIKE 'abc%'`            | `INDEX(email)` (left-anchored `LIKE`) |

---

## 📉 5. Don’t Index...

| Column Type                           | Why Not                  |
| ------------------------------------- | ------------------------ |
| Low-selectivity (e.g., boolean)       | Doesn't reduce row scans |
| Frequently updated fields             | Slows down writes        |
| Redundant indexes (e.g., same prefix) | Waste of space and CPU   |

---

## 📌 6. Maintenance & Monitoring

* Use **`EXPLAIN`** to inspect query plans.
* Use **auto-analyze/vacuum** to keep stats up to date.
* Monitor usage (e.g., `pg_stat_user_indexes`, `SHOW INDEX`).
* Periodically **drop unused indexes**.

---

## ✅ Indexing Best Practices

| Tip                                      | Why It Matters                      |
| ---------------------------------------- | ----------------------------------- |
| Start with query profiling               | Optimize what matters               |
| Use composite indexes smartly            | Avoid index bloat                   |
| Leverage covering indexes                | Reduce I/O                          |
| Tune indexes per workload (OLTP vs OLAP) | Different patterns, different needs |
| Test before adding                       | Every index has a write cost        |

---

## 🔁 Index Tuning Cycle

```text
[Log Queries] → [Profile Slow Queries] → [Design Index] →
[Test w/ EXPLAIN] → [Deploy] → [Monitor Impact]
```

---

## 🧰 Tooling Support

* PostgreSQL: `EXPLAIN ANALYZE`, `pg_stat_statements`
* MySQL: `EXPLAIN`, `SHOW INDEX`, `Performance Schema`
* SQL Server: Query Store, Index Tuning Advisor

# B-Tree vs B+Tree

Here’s an **easy-to-understand breakdown** of **B-Tree vs B+Tree**, focusing on:

* ✅ Structure differences
* ✅ Pros and cons
* ✅ Why most modern databases (e.g., MySQL, PostgreSQL) use **B+Tree**

---

## 🌳 What Is a B-Tree?

> A **self-balancing tree** where **both data and keys** are stored in **internal + leaf nodes**.

```text
B-Tree structure:
        [30]
       /    \
   [10]    [40, 50]
```

* Keys and records are stored **together**
* Internal nodes hold both **keys and values**

---

## 🌲 What Is a B+Tree?

> A **variant of B-Tree** where **only keys are stored in internal nodes**, and **all data is stored in leaf nodes**.

```text
B+Tree structure:
        [30]
       /    \
   [10]    [40, 50]     <-- internal nodes (keys only)
   ↓         ↓
[data]   [data]         <-- leaf nodes (data stored here only)
```

* Leaf nodes are **linked like a linked list**
* Internal nodes = navigation only (no real data)

---

## 🔍 Key Differences

| Feature          | **B-Tree**                         | **B+Tree**                                |
| ---------------- | ---------------------------------- | ----------------------------------------- |
| Data location    | Both **internal** & **leaf** nodes | Only **leaf** nodes                       |
| Traversal speed  | Slower for range queries           | Faster for **range scans** via leaf links |
| Storage          | Less redundancy                    | Slightly more storage (duplicate keys)    |
| Lookup           | Slightly faster (fewer hops)       | Consistent lookup path (always to leaf)   |
| Range query      | ❌ No easy sequential access        | ✅ Very efficient (linked leaves)          |
| Use in databases | Rare                               | ✅ Used by MySQL (InnoDB), PostgreSQL      |

---

## ✅ Why B+Tree Is Used in Databases

### Advantages:

1. **Efficient Range Queries**

   * Leaf nodes are linked, so `BETWEEN`, `ORDER BY`, and `LIMIT` are fast
2. **Predictable Disk I/O**

   * All lookups go to leaf → ideal for caching pages
3. **Better Fan-out**

   * Internal nodes are smaller → more keys per page → shallower tree
4. **No rebalancing required at internal nodes with data changes**

---

## 🧪 Example: Searching in B+Tree

```sql
SELECT * FROM users WHERE age BETWEEN 30 AND 40;
```

In a B+Tree:

* Go down to first leaf node with `age=30`
* Sequentially read next leaf nodes up to `age=40`
  ✅ Efficient!

In a B-Tree:

* Might need recursive traversal or repeated searches

---

## 📉 Trade-offs

| Area                  | B-Tree            | B+Tree                  |
| --------------------- | ----------------- | ----------------------- |
| Fast for point lookup | ✅ Slightly better | ✅ Good                  |
| Fast for range scan   | ❌ No              | ✅ Very fast             |
| Data redundancy       | ✅ Less            | ❌ More (in leaves only) |
| Simplicity            | ✅ Simpler         | ❌ Slightly more complex |

---

## 📌 Summary

| Feature                  | B-Tree                 | B+Tree                        |
| ------------------------ | ---------------------- | ----------------------------- |
| Data in leaves           | ❌ No (internal + leaf) | ✅ Yes (leaf only)             |
| Linked leaves            | ❌ No                   | ✅ Yes                         |
| Ideal for                | File systems, caches   | Databases, disk-based storage |
| Used by MySQL / Postgres | ❌ No                   | ✅ Yes                         |

---

## 🧠 Mnemonic

> 🔹 B-Tree = **Both** data and keys
> 🔹 B+Tree = **Plus** linked leaves and **Only** leaf stores data

---

Would you like a diagram showing how `SELECT` and `RANGE` queries navigate through a B+Tree vs B-Tree?


# Index stregetry, internal multicolum index, how to read the explain analyze, mvcc, explain to easy to understand

---

## 🧠 1. Index Strategy (Basic to Advanced)

### ✅ Why use Indexes?
Indexes speed up reads (SELECT) but slow down writes (INSERT/UPDATE/DELETE).

**Analogy:** Like a book’s table of contents — you can jump straight to the topic instead of flipping every page.

### 🎯 Common Index Strategies:

| Strategy                         | Use Case Example                                           |
|----------------------------------|------------------------------------------------------------|
| **Single-column index**         | Search on `WHERE user_id = 123`                           |
| **Multi-column (composite) index** | Search on `WHERE (user_id, status)` or `ORDER BY user_id, status` |
| **Covering index**              | Index includes all queried columns → no need to access table rows |
| **Partial index**               | Index only subset of data: `WHERE is_active = true`       |
| **Unique index**                | Enforce no duplicate emails, usernames, etc.              |
| **GIN index (inverted)**        | For array, JSONB, or full-text search in PostgreSQL       |
| **Hash index**                  | Only for equality comparisons; faster but limited         |

---

## 🔀 2. Internal: How Multi-column Index Works

```sql
CREATE INDEX idx_user_status ON orders(user_id, status);
```

This index can help for:

- `WHERE user_id = 1` ✅
- `WHERE user_id = 1 AND status = 'paid'` ✅
- `WHERE status = 'paid'` ❌ (only if user_id is also filtered)

### 👉 B-tree Left-to-Right Prefix Rule:
Multi-column indexes are **ordered left to right**.

```txt
(user_id, status, created_at)
↑        ↑        ↑
L1       L2       L3
```

To be used **efficiently**, filters should start from **leftmost**:
- `WHERE user_id = 1` ✅
- `WHERE user_id = 1 AND status = 'pending'` ✅
- `WHERE status = 'pending'` ❌

---

## 🧪 3. How to Read `EXPLAIN ANALYZE`

Used to analyze query plans and performance:

```sql
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 1 AND status = 'shipped';
```

### Key Terms:

| Term               | Meaning |
|--------------------|---------|
| **Seq Scan**       | Full table scan (no index used) ❌ |
| **Index Scan**     | Uses index and reads actual rows ✅ |
| **Index Only Scan**| Uses index alone (no need to fetch rows) ✅✅ |
| **Bitmap Index Scan** | Efficient for large results, merges multiple indexes |
| **Rows=...**       | Estimated number of rows |
| **Actual time=...**| Real execution time per step |
| **Loops=...**      | Number of times the node was executed |
| **Cost=...**       | Planner cost estimation (lower is better) |
| **Buffers=...**    | Number of disk/page reads |
| **Filter:...**     | Applied after index scan (not always optimal) |

### Example:
```txt
Index Scan using idx_user_status on orders  (cost=0.43..8.45 rows=10 width=64)
  Index Cond: (user_id = 1 AND status = 'shipped')
```
- ✅ Index is used (good)
- ✅ Query will be fast for small result sets

---

## 🔁 4. MVCC (Multi-Version Concurrency Control)

### 🤔 What is MVCC?

It’s how PostgreSQL and other DBs allow **readers and writers to work concurrently** without locking everything.

**Key idea:** Every transaction sees a *snapshot* of the data at a specific time.

### 📌 Example:
1. Transaction A starts and reads row X.
2. Transaction B updates row X → creates a new version of X.
3. A still sees old X; B sees new X.
4. When B commits, A still doesn’t see the change unless it restarts.

### 🌱 Benefits:
- Readers don't block writers.
- Writers don't block readers.
- No global locks = higher concurrency.

### 📉 Pitfalls:
- **VACUUM needed** to clean up dead rows.
- If not tuned properly, can lead to bloat and slowdowns.

---

## 🚦 Summary Table

| Concept              | Easy Explanation |
|----------------------|------------------|
| **Index Strategy**   | Use index for frequent search, sort, or filter columns |
| **Multi-column Index** | Useful when query starts from leftmost column(s) |
| **EXPLAIN ANALYZE**  | Tool to check if query uses indexes and estimate costs |
| **MVCC**             | Enables non-blocking reads/writes by versioning rows |

---

## 🧰 Best Practices

- Always check your query plan with `EXPLAIN ANALYZE`
- Avoid indexes on low-cardinality columns (`is_active`, `gender`)
- Periodically `VACUUM` / `ANALYZE` in PostgreSQL
- Use **partial** and **covering** indexes for performance-sensitive queries
- For OLAP: consider **columnar stores** like ClickHouse or DuckDB instead

