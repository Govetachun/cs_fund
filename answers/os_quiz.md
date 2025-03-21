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

## 1. Definition of HTTP

**HTTP (Hypertext Transfer Protocol)** is a request–response protocol designed for distributed, collaborative, hypermedia information systems. It is the foundation of data communication on the World Wide Web. HTTP enables clients (like web browsers) to communicate with servers to request and deliver resources (HTML pages, images, etc.).

---

## 2. HTTP Versions: Definitions & Key Characteristics

### HTTP/0.9
- **Introduction:** The very first version (circa 1991).
- **Features:**
  - **Simplicity:** Supports only a single method (GET).
  - **No Headers:** No support for HTTP headers; responses contain only raw data.
  - **Stateless & Minimal:** Designed for simple hypertext retrieval.
- **Usage:** Obsolete; primarily of historical interest.

---

### HTTP/1.0
- **Introduction:** Published as RFC 1945 in 1996.
- **Features:**
  - **Request Methods:** Supports methods like GET, POST, and HEAD.
  - **Headers:** Introduction of HTTP headers to provide metadata (Content-Type, Content-Length, etc.).
  - **Stateless:** Each request-response pair is independent; typically opens a new TCP connection per request.
  - **Limitations:** Lack of persistent connections leads to overhead from establishing new TCP connections frequently.
- **Usage:** Early web communications; laid the groundwork for later improvements.

---

### HTTP/1.1
- **Introduction:** Published as RFC 2068 (1997) and refined in RFC 2616 (1999), with subsequent updates.
- **Features:**
  - **Persistent Connections:** Keeps the connection open for multiple requests/responses, reducing latency.
  - **Pipelining:** Allows multiple HTTP requests to be sent on a single TCP connection without waiting for the corresponding responses (though practical use is limited due to head-of-line blocking).
  - **Chunked Transfer Encoding:** Supports sending responses in dynamically produced chunks.
  - **Additional Headers:** Enhanced control with caching (Cache-Control), content negotiation, and more.
  - **Virtual Hosting:** Uses the Host header to serve multiple domains on a single IP.
- **Usage:** Remains widely used on the web; mature and supported by all modern browsers and servers.

---

### HTTP/2
- **Introduction:** Published as RFC 7540 in 2015.
- **Features:**
  - **Binary Protocol:** Instead of textual commands, HTTP/2 uses a binary framing layer which reduces parsing errors and improves efficiency.
  - **Multiplexing:** Multiple concurrent streams on a single TCP connection, eliminating head-of-line blocking inherent in HTTP/1.1 pipelining.
  - **Header Compression:** Uses HPACK to compress HTTP headers, reducing overhead.
  - **Server Push:** The server can send resources to the client proactively, potentially speeding up page loads.
- **Usage:** Increasingly adopted by web services to reduce latency and improve performance.

---

### HTTP/3
- **Introduction:** Currently evolving (with significant adoption ongoing) based on the QUIC protocol.
- **Features:**
  - **Transport over QUIC:** Uses QUIC (which runs over UDP) rather than TCP, offering faster connection establishment and improved performance in lossy networks.
  - **Elimination of TCP Head-of-Line Blocking:** Since QUIC manages streams independently, one lost packet does not block others.
  - **Enhanced Security:** QUIC integrates TLS 1.3 directly into its protocol.
  - **Better Mobility:** Supports connection migration, which is useful for mobile devices.
- **Usage:** Adoption is growing as browsers and servers roll out support; expected to become standard in high-performance web applications.

---

## 3. Comparison Table: HTTP Versions

| **Feature**             | **HTTP/0.9**                | **HTTP/1.0**                             | **HTTP/1.1**                                  | **HTTP/2**                                   | **HTTP/3**                                   |
|-------------------------|-----------------------------|------------------------------------------|-----------------------------------------------|----------------------------------------------|----------------------------------------------|
| **Release Date**        | ~1991                       | 1996                                     | 1997 (and updated later)                      | 2015                                         | ~2020+ (emerging standard)                   |
| **Protocol Type**       | Text-based, Minimal         | Text-based                               | Text-based                                    | Binary                                       | Binary (built on QUIC over UDP)              |
| **Request Methods**     | Only GET                    | GET, POST, HEAD                          | GET, POST, HEAD, etc.                         | GET, POST, etc.                              | GET, POST, etc.                              |
| **Connection Handling** | New connection per request  | New connection per request               | Persistent connections                        | Single connection with multiplexed streams   | Multiplexed streams over a QUIC connection   |
| **Header Support**      | None                        | Basic HTTP headers                       | Extensive headers (caching, negotiation, etc.)| Header compression (HPACK)                   | Header compression (improved over HTTP/2)    |
| **Multiplexing**        | No                          | No                                       | Limited (via pipelining)                      | Yes                                          | Yes                                          |
| **Security**            | Not inherent                | Not inherent (TLS separate setup)         | Not inherent (often used with TLS as HTTPS)   | Typically used with TLS                      | Integrated TLS (TLS 1.3 in QUIC)             |
| **Server Push**         | No                          | No                                       | No                                            | Yes                                          | Yes                                          |
| **Latency Improvements**| N/A                         | Higher latency due to new connections    | Reduced due to persistent connections         | Further reduced via multiplexing             | Further improvements via QUIC’s optimizations|

---

## 4. Technical Considerations & Trade-offs

- **HTTP/1.x (0.9, 1.0, 1.1)**
  - **Pros:**
    - Simplicity
    - Mature and broadly supported
  - **Cons:**
    - Overhead from text-based protocol
    - Multiple TCP connections needed for concurrent requests

- **HTTP/2**
  - **Pros:**
    - Reduced latency via multiplexing
    - Header compression
    - Server push capabilities
  - **Cons:**
    - Still reliant on TCP (possible head-of-line blocking at transport layer)

- **HTTP/3**
  - **Pros:**
    - Eliminates TCP head-of-line blocking by using QUIC
    - Faster connection establishment
    - Better performance over unreliable networks
  - **Cons:**
    - Requires adoption of QUIC
    - Still maturing in terms of ecosystem and tooling

# 1. Making UDP "Guarantee" Reliability

## 1.1 Can We Make UDP Guarantee Delivery?
- **Default UDP Behavior**: UDP (User Datagram Protocol) is a connectionless, best-effort protocol. It does **not** guarantee:
  - Delivery
  - Ordered reception
  - Duplicate suppression

- **Application-Layer Reliability**: You *can* implement reliability mechanisms on top of UDP. For instance:
  - Sequence numbers in each UDP payload to detect order and loss
  - Acknowledgments and retransmission strategies (similar to TCP’s sliding window concept)
  - Forward error correction codes (e.g., Reed-Solomon) to recover from packet loss

**Conclusion**: UDP itself cannot provide guaranteed delivery, but you can *build* such guarantees in the application layer. This approach is common in low-latency streaming protocols (e.g., video games, VoIP) where partial or custom reliability is desired.

---

# 2. QUIC Versions

**QUIC** is a modern transport protocol initially designed by Google and later adopted by the IETF.

- **Google QUIC**: Early, proprietary version used by Google in Chrome.
- **IETF QUIC**: Standardized QUIC.
  - **Draft versions**: Ranged from draft-01 through draft-34 (and so on).
  - **QUIC v1 (RFC 9000)**: The first official IETF standard.
  - Subsequent versions continue to evolve, adding features such as multipath, improved congestion control, and various extensions.

**Key Features** of QUIC:
- Runs over UDP but includes congestion control and reliability built in
- TLS 1.3 integrated for security (no separate handshake step)
- Faster connection establishment compared to TCP+TLS

---

# 3. Firewall: Software or Hardware?

- **Hardware Firewall**:
  - A physical device placed at the network’s perimeter.
  - Performs packet inspection, network address translation (NAT), and other security checks at or near the router.
  - Usually used to protect entire networks or data centers.

- **Software Firewall**:
  - A piece of software running on the host OS (e.g., Windows Firewall, iptables on Linux).
  - Monitors and filters incoming/outgoing packets on that single system.
  - Often part of endpoint security solutions.

In practice, large organizations may have **multiple layers**: hardware firewalls at the perimeter and software firewalls on each server/client.

---

# 4. Blob Database

**Blob** stands for **Binary Large Object**. A “Blob database” typically refers to:
- Storing large unstructured data (e.g., images, videos, documents) in a dedicated type or field.
- Common in **NoSQL** or specialized object storage systems (e.g., AWS S3, Azure Blob Storage).
- Some relational databases (e.g., PostgreSQL, MySQL) provide BLOB data types to store binary data directly.

**Blob Databases** vs. **File Storage**:
- **Blob DB** might integrate data and metadata in a single system with transactional guarantees (ACID in RDBMS).
- **File System** might rely on separate metadata (filenames, paths) with different concurrency guarantees.

---

# 5. Encryption

## 5.1 What Is Encryption?
Encryption is the process of converting **plaintext** into **ciphertext** using a cryptographic algorithm and a key, ensuring that only authorized parties can convert ciphertext back into plaintext.

1. **Plaintext**: Original readable data.
2. **Key**: A piece of information (secret) that controls the encryption/decryption process.
3. **Ciphertext**: The encrypted (unreadable) data.

---

## 5.2 Symmetric vs. Asymmetric Encryption

### Symmetric Encryption
- **Single Key**: The same secret key is used for both encryption and decryption.
- **Example Algorithms**: AES (Advanced Encryption Standard), DES (old), 3DES, Blowfish.
- **Advantages**:
  - Faster, efficient for bulk data encryption.
- **Disadvantages**:
  - Key distribution: securely sharing the key with each party can be challenging.

### Asymmetric Encryption
- **Key Pair**: Consists of a public key (for encryption) and a private key (for decryption).
- **Example Algorithms**: RSA, ECC (Elliptic Curve Cryptography).
- **Advantages**:
  - Simplified key distribution: You can share your public key openly.
  - Enables digital signatures.
- **Disadvantages**:
  - Slower than symmetric encryption, more computationally intensive.

---

## 5.3 Algorithm Examples (Conceptual, Not Shown)

---

## 5.4 What Is a Root Certificate?

- A **Root Certificate** is the anchor of trust in a Public Key Infrastructure (PKI).
- Issued by a **Certificate Authority (CA)** (e.g., DigiCert, Let’s Encrypt) and stored in **trusted root stores** (e.g., your operating system or browser).
- Certificates in the trust chain are validated by chaining back to this root.

---

# 6. Database Normalization

Normalization is a **systematic approach** of decomposing tables to minimize data redundancy and improve data integrity. Common forms:

## 6.1 First Normal Form (1NF)
- **Definition**:
  1. Each cell contains *atomic values* (no multiple values in a single cell).
  2. Each record is unique (no duplicate rows).

- **Example**:
  - **Violation**: A `phone_numbers` column that stores multiple phone numbers in a single field, like `123-4567, 234-5678`.
  - **Fix**: Separate those phone numbers into multiple rows or have a child table referencing them.

## 6.2 Second Normal Form (2NF)
- **Definition**: 1NF + all **non-key** columns depend on the **entire primary key**, not just part of it.
- **Applies** only when you have a **composite primary key** (i.e., a primary key with multiple columns).

## 6.3 Third Normal Form (3NF)
- **Definition**: 2NF + **non-key** columns must depend on **only the primary key** (no transitive dependency).
- **Transitive Dependency**: A non-key attribute depends on another non-key attribute.

---

# 7. Deadlock

A **deadlock** occurs when two or more processes are blocked forever, each holding a resource that the other wants, and no one can proceed.

**Example**:
1. **Process A** acquires **Resource 1**, waits for **Resource 2**.
2. **Process B** acquires **Resource 2**, waits for **Resource 1**.
3. Neither can proceed, resulting in deadlock.

**Deadlock Avoidance Techniques**:
- **Lock Ordering**: Enforce an order in which locks are acquired.
- **Deadlock Detection**: Periodically check for cycles in resource allocation graphs and abort/rollback one process if a deadlock is found.
- **Timeouts**: If a lock is not acquired within a set time, release locks and retry.

---

# 8. Overriding vs Overloading

## 8.1 Overriding
- **Context**: Inheritance/Polymorphism in OOP.
- **Definition**: A subclass provides a specific implementation for a method already defined in its superclass.
- **Key Points**:
  - Signature is the *same* as the superclass method (same name, parameters).
  - Different behavior at runtime (dynamic dispatch).

## 8.2 Overloading
- **Context**: Same class (or possibly related classes) but different parameter signatures.
- **Definition**: *Multiple methods* with the same name but different parameter lists (type/number/order).
- **Key Points**:
  - Compile-time polymorphism (the method is chosen at compile time).

---

# 9. Types of Cache

Caching is the practice of storing data in a **faster storage** medium to speed up future requests.

### 9.1 CPU Cache
- **L1, L2, L3**: On-chip caches for CPU instructions and data. Very low latency.

### 9.2 Application/Software Cache
- **In-Memory Store**: Tools like Redis, Memcached.
- Used to store database results, session data, or any frequently accessed data.

### 9.3 Web Cache
- **Browser Caches**: Storing web pages, images to reduce network traffic.
- **CDN**: Geographically distributed caches.

### 9.4 Database Cache
- **Buffer Pools**: E.g., MySQL InnoDB buffer pool.
- Caching queries or data pages in memory to avoid disk I/O.



# 1. Core OS Abstraction Terms – Definitions

| Term             | Definition                                                                                                                       |
|------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| **Multitasking** | The ability of an OS to **switch between tasks rapidly**, giving the illusion that multiple tasks are executing at the same time. It uses **time-sharing** of CPU. |
| **Multithreading** | A technique where a **single process** contains multiple threads that can execute concurrently (e.g., background task + UI in the same app). Threads share memory. |
| **Multiprocessing** | Use of **multiple CPUs/cores** in a single system to execute multiple **processes** simultaneously. Each process is isolated.                             |
| **Parallelism** | Actual **simultaneous execution** of multiple computations. Can be achieved with multithreading (on multi-core) or multiprocessing.                              |
| **Concurrency** | A broader concept: multiple computations are in **progress at the same time**, possibly interleaved (not necessarily parallel). It focuses on **structure**, not execution timing. |
| **Context Switching** | Saving and loading the state of processes/threads so the CPU can switch between them. Overhead is higher in process switching than thread switching. |
| **Thread**       | The smallest unit of execution, sharing the same memory space of its parent process.                                                                 |
| **Process**      | An independent unit of execution with its own memory space. Can contain multiple threads.                                                              |
| **Scheduler**    | Part of the OS that decides which process/thread runs next on the CPU. Can use algorithms like Round Robin, FCFS, etc.                                   |

---

# 2. Comparison Table

| Feature              | Multitasking         | Multithreading           | Multiprocessing           | Parallelism            | Concurrency           |
|----------------------|----------------------|--------------------------|---------------------------|------------------------|-----------------------|
| **Execution Unit**   | Process             | Thread                   | Process                   | Any (process/thread)   | Any                   |
| **Sharing**          | Processes isolated  | Threads share memory     | Processes isolated        | Can share or not       | Can share or not      |
| **True Simultaneity**| No (illusion)       | Maybe (depends on cores) | Yes (multi-core CPU)      | Yes                    | Not required          |
| **Focus**            | Time-sharing        | Task concurrency in app  | System-level concurrency  | Speed-up via parallel  | Structure, responsiveness |
| **Overhead**         | Medium              | Low (shared memory)      | High (context switching)  | Depends               | Depends              |

---

# 3. Additional OS Abstraction Terms

### 3.1 Virtual Memory
- Abstraction where each process thinks it has its own large memory.
- OS maps virtual addresses to physical RAM using paging.

### 3.2 Paging & Segmentation
- **Paging**: Divides memory into fixed-size pages; enables non-contiguous memory allocation.
- **Segmentation**: Divides memory into logical segments (code, stack, heap).

### 3.3 Swapping
- Temporarily moving processes from RAM to disk (swap space) to free up memory.

### 3.4 Kernel/User Mode
- **Kernel Mode**: OS runs in full-privilege mode (can access all hardware).
- **User Mode**: User processes run with restricted privileges.

### 3.5 Daemon/Service
- Background process, often started at boot, running continuously (e.g., cron, systemd).

### 3.6 Fork and Exec (in UNIX)
- **Fork**: Creates a copy of the current process.
- **Exec**: Replaces the current process image with a new program.

### 3.7 Signals
- OS-level interrupts sent to processes (e.g., `SIGKILL`, `SIGTERM`) to control or communicate.

### 3.8 Locks/Mutex/Semaphores
- **Synchronization primitives** used in multithreaded programming to prevent race conditions.

### 3.9 IPC (Inter-Process Communication)
- Mechanisms for processes to communicate: shared memory, pipes, message queues, sockets.

### 3.10 Scheduling Algorithms
- Dictate which process/thread runs next:
  - FCFS (First-Come, First-Serve)
  - Round Robin
  - SJF (Shortest Job First)
  - Priority Scheduling
