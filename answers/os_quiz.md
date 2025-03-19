# **Process Context Switching vs Thread Context Switching**

## **1. Process Context Switching**
- Happens **when the CPU switches from executing one process to another**.
- Involves **saving and restoring** the **entire process state**, including:
  - **Registers**
  - **Program Counter (PC)**
  - **Memory (Page Tables, Stack, Heap)**
- More **expensive** than thread switching due to full memory state change.

## **2. Thread Context Switching**
- Occurs **within the same process**, switching between threads.
- **Shares the same memory space** (heap, global variables).
- Only **registers and stack need to be switched**, making it **faster**.
- **User-level threads** are faster to switch than **kernel-level threads**.

---

# **Can Threads in the Same Process Run on Multiple Cores?**
✅ **Yes, they can**.  
- **Multi-threaded programs** can run on multiple cores if the system supports **Symmetric Multiprocessing (SMP)**.
- The **OS scheduler** assigns threads to different cores dynamically.
- **Example**: In Python, the **GIL (Global Interpreter Lock)** prevents true parallel execution in CPython, but languages like **Java, C++** allow true multi-core execution.

---

# **Garbage Collector (GC) - What & How It Works?**
## **1. What is Garbage Collection?**
- **GC automatically manages memory**, reclaiming unused objects.
- Prevents **memory leaks** by freeing objects no longer referenced.

## **2. How GC Detects Unused Variables?**
- Uses **Reference Counting** and **Graph Traversal (Mark and Sweep)**:
  - **Reference Counting**: Object is deleted when the reference count reaches **zero**.
  - **Mark and Sweep**: Finds objects **not reachable** from **root references (e.g., stack, global variables, active threads)** and deletes them.
  - **Generational GC**: Objects are categorized as **young, old, and permanent generations** for efficient memory cleanup.

---

# **Garbage Collector Timing**
## **Normal Conditions**
- GC runs **periodically**, triggered based on:
  - **Heap usage threshold**.
  - **Time intervals** (e.g., Java JVM's GC runs based on minor and major GC cycles).
  - **Explicit request** (`System.gc()` in Java, `gc.collect()` in Python).

## **Peak Hour - High Connections & Memory Usage**
- **Adjust GC tuning**:
  - Increase **heap size** to reduce GC frequency.
  - Use **Concurrent GC** (e.g., **G1GC, ZGC** in Java) to minimize pause times.
  - **Optimize object allocation** to reduce short-lived objects.

## **Peak Hour - Too Many Long-Lived Connections**
- Use **manual GC tuning**:
  - Run **background GC** to avoid blocking requests.
  - Enable **lazy cleanup** for long-lived objects.
  - Consider **offloading some processing to a cache (e.g., Redis, Memcached)** to reduce memory load.

---

### **Conclusion**
- **Thread switching is faster** than process switching.
- **Multi-threaded apps can run on multiple cores**.
- **Garbage Collection manages memory automatically**, detecting unused objects.
- **GC tuning is critical** during **high load (peak hours)** to prevent slowdowns.


# **Database Index - Why B+ Tree Instead of Red-Black Tree?**
Most databases (e.g., MySQL, PostgreSQL) use **B+ Tree** instead of **Red-Black Tree** for indexing because:

✅ **Better Disk I/O Efficiency**:  
- **B+ Tree is optimized for disk-based storage** as it has a **high branching factor**, reducing the number of disk reads.
- Red-Black Tree has **more levels**, leading to **more disk accesses**.

✅ **Sequential Access Performance**:  
- B+ Tree **stores all values in leaf nodes** with linked pointers → **supports range queries efficiently**.
- Red-Black Tree requires **in-order traversal**, which is **not as optimized for range queries**.

✅ **Bulk Read Optimization**:  
- B+ Tree **retrieves multiple records in one disk read**, while Red-Black Tree requires **multiple searches**.

✅ **Better Cache Locality**:  
- B+ Tree nodes are **larger and fewer**, leading to **fewer cache misses** compared to Red-Black Trees.

---

# **What is a Hash Index? How is it Different from a B+ Tree Index?**
## **Hash Index**
- Uses a **hash function** to map keys to a fixed-size hash table.
- Provides **O(1) lookup time** for exact key lookups.
- **No range queries** (unordered storage).
- **Used in memory-based databases (e.g., Redis)**.

## **B+ Tree Index**
- Uses a **balanced tree structure**.
- Efficient for **range queries, sorted retrievals**.
- Works well for **disk-based databases**.

### **When to Use?**
| Feature          | Hash Index | B+ Tree Index |
|-----------------|-----------|--------------|
| **Lookup Speed** | O(1) (Exact match) | O(log N) |
| **Range Queries** | ❌ Not supported | ✅ Efficient |
| **Sorting Support** | ❌ No sorting | ✅ Ordered data |
| **Memory Usage** | Lower | Higher |

---

# **Design Pattern - What & How Have You Used It?**
## **What is a Design Pattern?**
A **design pattern** is a **reusable software solution** for common problems in software design.

## **How Have I Used It?**
### **1. Singleton Pattern**
- Used in **database connections** to ensure only one connection instance is created.

### **2. Factory Pattern**
- Applied in **creating objects dynamically** (e.g., different logging strategies).

### **3. Observer Pattern**
- Used in **event-driven systems** (e.g., pub-sub messaging in microservices).

---

# **What is Deadlock? How to Prevent It?**
## **What is a Deadlock?**
A **deadlock** occurs when two or more transactions **wait indefinitely** for each other to release locks.

## **How to Prevent Deadlocks?**
### ✅ **1. Lock Ordering** (Avoid Circular Wait)
- Always **acquire locks in a fixed order**.

### ✅ **2. Timeout Mechanism**
- Set a **timeout** for transactions to detect deadlocks early.

### ✅ **3. Use Deadlock Detection Algorithms**
- Databases like **MySQL & PostgreSQL** have built-in deadlock detection.

### ✅ **4. Reduce Lock Granularity**
- Use **row-level locks** instead of table locks to minimize contention.

### ✅ **5. Optimistic Locking**
- Use **versioning instead of locking** to avoid waiting.

---

### **Conclusion**
- **B+ Tree** is used for **indexing** because it is **disk-efficient** and **supports range queries**.
- **Hash Index** is great for **exact lookups but lacks sorting**.
- **Design Patterns** improve **code structure and reusability**.
- **Deadlocks** occur due to **circular waits**, and they can be avoided using **timeouts, lock ordering, and optimistic locking**.


# REST API vs gRPC

## 1. Comparison

| Feature        | REST API                          | gRPC                          |
|--------------|--------------------------------|------------------------------|
| **Protocol** | HTTP/HTTPS                     | HTTP/2                       |
| **Data Format** | JSON, XML                   | Protocol Buffers (binary)    |
| **Performance** | Slower (text-based)         | Faster (binary format)       |
| **Streaming** | Limited (WebSockets, SSE)    | Full-duplex streaming        |
| **Code Gen** | OpenAPI (optional)            | Auto-generate from `.proto`  |
| **Security** | OAuth, JWT, Basic Auth        | TLS, OAuth, mTLS             |
| **Use Case** | Web APIs, public services     | Microservices, high-speed RPC |

🔹 **Use REST API for**: Simplicity, web integration, wide compatibility.  
🔹 **Use gRPC for**: High performance, microservices, real-time communication.

---

# 2. REST API Structure

### **Common REST API Components**
- **Endpoints:** `/users`, `/orders/{id}`
- **HTTP Methods:** `GET`, `POST`, `PUT`, `DELETE`
- **Authentication:** OAuth, JWT, API Keys
- **Response Format:** JSON (e.g., `{ "id": 1, "name": "John Doe" }`)

---

# 3. What Happens When a Client Sends a Request?

1. **Client** sends an HTTP request (`GET /users/1`).
2. **Server (API Gateway or Load Balancer)** receives and routes the request.
3. **Middleware** handles authentication, logging, rate-limiting.
4. **Controller/Handler** processes the request.
5. **Database Query/Service Call** fetches data.
6. **Response** is formatted in JSON and returned to the client.

---

# 4. Class vs Interface

| Feature   | Class                             | Interface                        |
|-----------|----------------------------------|----------------------------------|
| **Definition** | Blueprint with implementation | Defines a contract (no logic) |
| **Usage** | Inheritance, object instantiation | Enforces structure for classes |
| **Supports Fields?** | Yes | No |
| **Multiple Inheritance?** | No | Yes (multiple interfaces) |

🔹 **Use Class when**: You need logic, behavior, and object instantiation.  
🔹 **Use Interface when**: You need a structure without implementation.


# Mutex vs Semaphore

| Feature      | Mutex (Mutual Exclusion) | Semaphore |
|-------------|-------------------------|-----------|
| **Definition** | A locking mechanism that allows only one thread at a time | A signaling mechanism allowing multiple threads |
| **Usage** | Thread synchronization | Process and thread synchronization |
| **Types** | Binary Mutex | Counting & Binary Semaphore |
| **Ownership** | Owned by a thread | No ownership concept |
| **Example** | `pthread_mutex_lock()` | `sem_wait()`, `sem_post()` |

🔹 **Use Mutex when**: Only one thread should access a resource.  
🔹 **Use Semaphore when**: Multiple threads can access a limited number of resources.

---

# Interface vs Abstract Class

| Feature | Interface | Abstract Class |
|---------|----------|---------------|
| **Definition** | Defines a contract (only method signatures) | Can have abstract and concrete methods |
| **Methods** | No implementation (Java 8+ allows default methods) | Can have both abstract & concrete methods |
| **Variables** | Only constants (static final) | Can have instance variables |
| **Multiple Inheritance** | Yes | No (but can extend one abstract class) |
| **Use Case** | When multiple unrelated classes need to follow a contract | When creating a base class with common behavior |

🔹 **Use Interface when**: You need a contract for multiple classes.  
🔹 **Use Abstract Class when**: You need shared code and behavior.

---

# SQL vs NoSQL

| Feature | SQL (Relational) | NoSQL (Non-Relational) |
|---------|------------------|------------------------|
| **Data Structure** | Tables with rows & columns | Key-Value, Document, Columnar, Graph |
| **Schema** | Fixed, predefined schema | Dynamic schema |
| **Scalability** | Vertical Scaling | Horizontal Scaling |
| **Transactions** | Strong ACID compliance | BASE (Eventual Consistency) |
| **Use Case** | Structured data, complex queries | Big data, high availability |

🔹 **Use SQL when**: You need structured data and strict consistency.  
🔹 **Use NoSQL when**: You need flexibility, high scalability.

---

# Array vs ArrayList vs LinkedList

| Feature | Array | ArrayList | LinkedList |
|---------|------|---------|-----------|
| **Size** | Fixed | Dynamic | Dynamic |
| **Memory** | Contiguous | Resizable array | Node-based (extra memory for pointers) |
| **Insert/Delete** | Expensive (shifting required) | Slow (shifting required) | Fast (O(1) if head/tail known) |
| **Random Access** | O(1) | O(1) | O(n) |

🔹 **Use Array when**: You need fixed-size, fast access.  
🔹 **Use ArrayList when**: You need dynamic resizing.  
🔹 **Use LinkedList when**: You need frequent insert/delete operations.

---

# Heap vs Stack in Java

| Feature | Heap | Stack |
|---------|------|------|
| **Memory Allocation** | Dynamic memory | Local method memory |
| **Size** | Large | Small (per thread) |
| **Speed** | Slower (GC overhead) | Faster (LIFO access) |
| **Lifetime** | Until GC | Until function ends |
| **Use Case** | Objects, Class Instances | Primitive variables, Method calls |

🔹 **Heap uses more memory** because objects persist longer and require garbage collection.  
🔹 **Stack is faster** because it follows LIFO, reducing allocation/deallocation overhead.

---

# Why is Hashing Time Efficient?

- **Time Complexity:** O(1) for search, insert, delete (on average).
- **Behind the Scenes:**
  - Uses **hash function** to convert keys → indices.
  - Stores key-value pairs in an **array (hash table)**.
  - Resolves **collisions** using chaining (linked lists) or open addressing.
- **Efficiency comes from:**
  - Direct indexing → No need to search entire data structure.
  - Fixed bucket lookup → Near-constant time complexity.

---

# Horizontal vs Vertical Scaling

| Feature | Horizontal Scaling | Vertical Scaling |
|---------|-------------------|-----------------|
| **Definition** | Adding more machines | Upgrading existing machine |
| **Cost** | Higher operational cost | Higher hardware cost |
| **Performance** | Better for distributed workloads | Limited by hardware |
| **Complexity** | More complex (load balancing) | Simpler (single system) |

🔹 **Use Horizontal Scaling** for distributed systems.  
🔹 **Use Vertical Scaling** for single powerful machine optimizations.

---

# Sharding vs Partitioning

| Feature | Sharding | Partitioning |
|---------|---------|-------------|
| **Scope** | Across multiple servers | Within a single database |
| **Purpose** | Distribute load | Organize data efficiently |
| **Example** | Splitting users by region into separate DBs | Splitting customer records by last name |

🔹 **Use Sharding when**: You need to distribute database load across multiple machines.  
🔹 **Use Partitioning when**: You need better query performance within a single database.
## Avoiding Race Conditions in a Multi-threaded Memory Allocator
Race conditions occur when multiple threads access and modify shared memory simultaneously. To prevent this:

Solutions:
- Mutex Locking: Protects memory allocation with ```std::mutex``` or ```std::lock_guard``` in C++.
- Thread-local Storage (TLS): Each thread manages its own memory pool.
- Atomic Operations: Use ```std::atomic``` to ensure safe access to memory metadata.
- Lock-free Algorithms: More complex but avoids mutex overhead.


# Deadlock

## **Definition of Deadlock**
A deadlock occurs when multiple threads are waiting on resources held by each other indefinitely, preventing progress.

## **Conditions for Deadlock (Coffman’s Conditions)**
1. **Mutual Exclusion** - Resources cannot be shared.
2. **Hold and Wait** - A thread holds a resource while waiting for another.
3. **No Preemption** - Resources cannot be forcibly taken.
4. **Circular Wait** - A cycle exists where each thread is waiting for a resource held by the next thread in the cycle.

## **Minimum Number of Threads for Deadlock**
- **Minimum = 2 Threads** (Thread A waits on a resource held by Thread B, and vice versa).

## **Can Deadlock Occur With Only One Thread?**
- **No**, because there is no circular dependency between multiple threads.

# Index vs. Clustered Index

- **Index**: A data structure that improves lookup speed. It stores pointers to the actual data without changing its order.
- **Clustered Index**: Sorts and stores the actual data rows in the index order. Only one per table.

📌 **Key Differences**
| Feature          | Index (Non-clustered) | Clustered Index |
|-----------------|----------------------|----------------|
| Data Storage    | Separate from data   | Data stored in index order |
| Number Allowed  | Multiple per table   | Only one per table |
| Speed           | Faster for reads     | Faster for range queries |

---

# Java vs. Python: Which to Choose?

| Feature       | Java                       | Python                |
|--------------|---------------------------|-----------------------|
| Performance  | Faster (compiled, JVM)    | Slower (interpreted)  |
| Development  | More verbose, strict typing | Faster, concise syntax |
| Use Cases    | Large-scale systems, Android, enterprise | AI/ML, scripting, web apps |
| Libraries    | Strong for backend & mobile | Strong for AI/ML, web |

📌 **Choose Java if**: You need performance, scalability, or mobile development.  
📌 **Choose Python if**: You need fast development, AI/ML, or scripting.

🔹 **Verdict**:  
- **For AI/ML, scripting** → Python 🐍  
- **For performance & large-scale apps** → Java ☕  


# 🔄 Garbage Collector (GC)

## What is Garbage Collector?
- GC automatically frees up memory by removing objects that are no longer needed.
- Prevents memory leaks by reclaiming unused objects.

## How does GC detect unreferenced variables?
- Tracks object references.
- If an object has **no references**, it's marked for collection.
- Common techniques:
  - **Reference Counting** (tracks ref count)
  - **Tracing (Mark-and-Sweep, Generational GC)** (marks reachable objects, sweeps the rest)

---

# ⏳ GC Timing Setup in Normal Conditions
- GC runs **automatically** based on JVM/Interpreter heuristics.
- Can be adjusted using:
  - **JVM Flags** (`-XX:+UseG1GC`, `-XX:+UseParallelGC`)
  - **Python** (`gc.set_threshold()`)

**Best Practice:**  
- Use **default GC settings** unless performance issues arise.

---

# 🚀 Handling GC in Peak Hours (High Connections, High Memory)
### Scenario 1: High Memory Usage
- **Trigger GC manually (if needed)**:  
  - **Java:** `System.gc()` (not always effective)
  - **Python:** `gc.collect()`
- **Tune GC for faster cleanup**:
  - Increase **heap size** (`-Xmx` in Java)
  - Optimize **young-gen size** (`-XX:NewRatio`)

---

# 🔗 Handling GC with Many Long-Lived Connections
### Scenario 2: Peak Hours + Long-Lived Connections
- **Problem**: Connections keep references alive → GC cannot collect them.
- **Solution**:
  - Use **Explicit Connection Pooling** (e.g., HikariCP for Java, SQLAlchemy for Python).
  - Optimize **GC tuning**:
    - **Java**: `-XX:+UseG1GC -XX:InitiatingHeapOccupancyPercent=50`
    - **Python**: Use `gc.collect()` after closing unused connections.
  - Monitor **GC logs** (`-XX:+PrintGCDetails` in Java) to adjust tuning.

📌 **Best Practice:**  
- Reduce **object retention** (close connections properly).
- Adjust **heap & GC settings** to prevent long pauses.

# 📌 Index in Database vs. Elasticsearch

| Feature          | Database Index (SQL)        | Elasticsearch Index  |
|-----------------|---------------------------|---------------------|
| Purpose        | Speed up queries (B-Trees, Hash Index) | Full-text search, analytics |
| Storage        | Table-based, structured | JSON docs, distributed shards |
| Query Speed    | Optimized for **structured** data | Optimized for **search queries** |
| Updates        | Slower (ACID compliance) | Faster indexing but eventual consistency |
| Use Cases      | Relational DBs (MySQL, PostgreSQL) | Search engines, logs, analytics |

**📌 When to use what?**  
- Use **DB Index** if you need **fast relational queries**.  
- Use **Elasticsearch Index** for **full-text search & analytics**.

---

# 🔄 Queue vs. Airflow

## ✅ **Queue (Message Queue)**
- **Purpose**: Asynchronous message passing between services.
- **Examples**: RabbitMQ, Kafka, AWS SQS.
- **Use Case**:
  - Microservices communication.
  - Event-driven systems (real-time processing).

## 🔀 **Apache Airflow**
- **Purpose**: Workflow orchestration (ETL, ML Pipelines).
- **Features**:
  - DAGs (Directed Acyclic Graphs) to schedule tasks.
  - Handles dependencies & retries.
  - Supports parallel execution.

### **📌 Key Differences**
| Feature       | Queue (RabbitMQ, Kafka) | Airflow |
|--------------|------------------------|--------|
| Use Case    | Real-time messaging     | Workflow scheduling |
| Processing  | Event-driven, streaming | Batch processing |
| Scalability | High (distributed)      | Scalable, but task-based |

**📌 When to use what?**  
- Use **Queue** for real-time **messaging & event-driven systems**.  
- Use **Airflow** for **workflow orchestration & task scheduling**.


# 🔒 Preventing Race Conditions in LRU Cache

### 🚩 Problem:
- **Race condition** occurs when multiple threads simultaneously access or modify the same LRU cache.

### ✅ Solution:
- Use **Locks** or **Mutexes** to synchronize cache access.

# CI/CD for Production Pipeline

Continuous Integration (CI) and Continuous Deployment (CD) are key practices for ensuring smooth and efficient production pipelines.

### 📦 **CI (Continuous Integration)**
- **Purpose**: Automatically integrate code changes into a shared repository to catch errors early.
- **Steps**:
  1. **Push code** to version control (GitHub, GitLab).
  2. **Run tests** (unit, integration) on each change.
  3. **Build** application after successful tests.
  4. **Static code analysis** (e.g., linting, security scans).

### 🚀 **CD (Continuous Deployment)**
- **Purpose**: Automatically deploy code to production after CI.
- **Steps**:
  1. **Build the artifact** (Docker image, binary, etc.).
  2. **Deploy** to staging/production environments.
  3. **Run end-to-end tests** in staging.
  4. **Monitor** deployment for issues.
  
---

### 🛠️ **Tools for CI/CD Pipeline**
1. **Version Control**: Git, GitHub, GitLab.
2. **CI Tools**: Jenkins, GitHub Actions, GitLab CI.
3. **CD Tools**: Spinnaker, Argo CD, AWS CodePipeline.
4. **Containerization**: Docker, Kubernetes (for orchestration).
5. **Infrastructure**: Terraform, Ansible.