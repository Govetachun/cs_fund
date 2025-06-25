# Topic: OPERATING SYSTEM

## What is `process`, `thread`? What are the differences between them?

A process is like a program in execution. And a thread is a segment of a process. Each process has its own memory and is isolated. Meanwhile, threads of the same process can easily communicate with other threads, access common variables, memory.

### Why is a Thread Called a Lightweight Process?
A thread is called a lightweight process (LWP) because it shares most resources with other threads in the same process, reducing overhead
| Feature           | Process                  | Thread                     |
|------------------|-------------------------|----------------------------|
| **Memory Space**  | Separate address space  | Shared memory within a process |
| **Creation Cost** | High (requires new PCB, memory allocation) | Low (only needs new TCB) |
| **Context Switching** | Slow (requires switching memory mappings) | Fast (only registers & stack) |
| **Communication** | Uses IPC (slow) | Uses shared memory (fast) |
| **Execution** | Can run independently | Runs as part of a process |

## What data `process`, `thread` need to live? Why do they say that Thread is a lightweight process?

Thread is a lightweight process because it does not cost much CPU to switch between threads as between processes.

## How CPU switch (context switch) between processes/threads? How data is to ensure safety? (in case single CPU core and multiple CPU cores)

Context switching is the mechanism by which a CPU switches from executing one process (or thread) to another. This is essential for multitasking, allowing multiple processes or threads to share the CPU efficiently.

When process context switching happens, the state of the current process will be stored in Process Control Block (PCB), so this process can be resumed later. It includes the value of the CPU registers, the process state, and memory-management information. Data pages in memory of the current process can be replaced or not, depending on the availability of memory, and the memory-management method of the operating system.

When thread context switching happens, the state of the current thread will be stored in Thread Control Block (TCB), so the thread can be resumed later. It includes the value of the CPU registers, the thread state, a program counter, a stack pointer, and a pointer to the PCB of the process to which the thread belongs. There is one major difference in thread context switching compared to processes: the address space remains the same (the is no page replacement).

## Can threads in the same process run on different cores?

Yes, threads within the same process can run on different cores, depending on OS scheduling. This enables true parallelism, if language/runtime supports it. — unless the runtime (like CPython) introduces constraints like the GIL

## 🔒 Python’s GIL (Global Interpreter Lock)

* In **CPython**, the GIL allows only **one thread to execute Python bytecode at a time**.
* So, even if threads are scheduled on different cores, they **don’t execute Python code in parallel**.
* Solution: Use:

  * `multiprocessing` (different processes = real parallelism)
  * External C extensions (NumPy, etc.)
  * Other Python implementations (e.g., Jython, IronPython) or languages like **Go, Rust, C++**


## What is multi-process and multi-thread? When should we use which?

Multi-processing is when more than 2 processes are running simultaneously on a computer. This is probable in a multi-processor computer, where each processor handles a process.

Multi-threading is when more than 2 threads created by the same process are running simultaneously.

## Process has how many states? How does it change between each state?

Basically, a process has 5 states: new, ready, running, wait, and terminate. New is the status of the process when it is created. At the ready status, the process waits to be assigned to a processor. When the process is assigned to a processor, its status becomes running and the processor executes the process instructions. If the process need something like I/O, or the CPU is assigned for another process, process status will become waiting. When all the instructions are executed, or the process is terminated by OS, it moves to terminate state where it waits to be removed out of the main memory.

## Scheduling algorithm

There are many types of scheduling algorithms like first come first serve, shortest job first, shortest remaining time first, round-robin, etc.

## What will happen if a process is waiting? Or a thread is sleeping?

When a thread is sleeping, other threads might be running.

When a process is waiting, OS can run another process.

## How CPU detects that a thread is sleeping? Or detect when it wants to run?

OS has an OS scheduler that keeps track of the list of processes and their status. When the thread goes to sleep, it tells the scheduler that it gives up its time slide, and it shouldn't be wake up until a certain time has elapsed. The next time the scheduler looks at this process, if the time has elapsed, it will change this thread status as ready to run.

## What is thread-pool? How to use it? Describe how to create a thread-pool in your programming language

Thread pool is a design pattern that's used to manage concurrent execution. It is also called as worker model. A thread pool has a limited number of threads to allocate for concurrent execution.

## Can 2 different processes access or change data of each other address space? (this question may make you confuse with your knowledge about virtual memory)

Cause memory can be used by a process at this time and later by another process, yes an address space in general does not belongs to any process and can be changed by many processes, but in a certain time, an address space belongs to a process only and can only be modified by this process.

## Can 2 processes use the same library (for eg: libc is required to every process)? How?

It is possible if the library is a shared library, not a static one. Each process has its own virtual memory address that refers to the same physical memory address that stores the shared library.

## How does debugger work? How it can attach to a running process and change data of that process?

A debugger is a tool that helps developers analyze and control the execution of a program. It enables setting breakpoints, inspecting memory, modifying data, and stepping through instructions.

## How 2 processes can communicate with each other? (There are a lot of ways but focus on the OS's way)

It can use a shared resource or pass messages to other processes.

## What is child-process? How to create a child-process?

Child process is a process created by another process. Child process inherits most of properties from its parent, such as environment settings, process directory, from its parent. It does not inherit some properties like process id, lock...

## What data a child-process have when we create it?

As above.

## Can it read/write data on it's parent process?

No, it cannot. If it wants to communicate with its parents, it has to use a separate shared memory segment or use a socket.

## What is copy on write (COW), Dirty COW? **(this concept is important to understand OS)**

Copy on write is when multiple callers ask for a copy of a resource, OS can return the pointer to the old resource and only make the private copy for a caller when necessary, like when the caller wants to modify this resource. The advantage is if the caller makes no modification, no private copy needs to be created.

## What will happen when child-process changes a variable of parent process?

Child process cannot change variables of its parent.

## If file descriptor also be inherited by the child process. What if 2 processes can handle a same file descriptor or even a same socket?

2 processes can handle the same file descriptor/socket. To prevent other processes does further things with the socket that the current process wants to close, we can use shutdown function instead of close function to stop other processes from using this socket.

## Concurrency vs Parallels? (in case single CPU core and multiple CPU cores)

|Feature|	Concurrency|	Parallelism|
|----|-----|------|
|Definition|	Multiple tasks progress at the same time (but not necessarily executed at the same time).	|Multiple tasks run at the same time on different cores.|
|Execution Style	|Task switching (time-sharing).	|True simultaneous execution.|
|Single-Core CPU	|Can only achieve concurrency (by quickly switching between tasks).	|No true parallelism (because only one task runs at a time).|
|Multi-Core CPU	|Can achieve both concurrency and parallelism.	|Parallel execution (each core runs a separate task).|
|Analogy	|A single waiter serving multiple tables by switching between them.|	Multiple waiters serving multiple tables at the same time.


- Single-core CPUs → Can only do concurrency (task switching).
- Multi-core CPUs → Can do both concurrency and parallelism.
- Concurrency ≠ Parallelism, but they complement each other in modern computing.
    - 🚀 Concurrency = Managing multiple tasks efficiently.
    - 🚀 Parallelism = Doing multiple tasks at the exact same time.

## What is critical zone?

Critical zone or critical region is the segment of code where processes access shared resources.

## What is race condition and how to handle this case?

Race condition is when two threads access a variable at the same time. It can lead to data inconsistency if the operations are executed in a certain order. To prevent this we can take advantage of some locking mechanisms.

## What is locking mechanism? mutex? semaphore? spinlock? read lock vs write lock?

Mutex is a mutual exclusive flag to detect if the desired resource is free and claim it for use.

Semaphore is a generalized mutex, to control the number of available resources.

Spinlock is a mechanism in which a process keeps asking for the availability of the resource via polling.

## What is deadlock and how to avoid deadlock?

Deadlock is when there is a circle where some processes lock something and wait for something that other processes have already locked.

Deadlock happens when all of these conditions happen:

- Mutual exclusion: It means processes want to access the same resource.
- Hold and wait: When processes need more than one resource and it already has some, it will hold them and wait for the rest.
- No preemption: A process can not take away resource that is being held by other processes.
- Circular wait.

This can be avoided by making processes release their locked variable when they cannot get the other necessary resource, or granting some preemption permission for some processes.

## How does memory is managed in the OS?

| Component          | Description |
|--------------------|-------------|
| **Physical Memory (RAM)** | Actual memory available in hardware (DRAM modules). |
| **Virtual Memory** | Abstraction that allows more memory than physically available using paging/swapping. |
| **Page Tables** | Maps virtual addresses to physical addresses. |
| **Memory Protection** | Prevents one process from accessing another’s memory. |
| **Caching & TLB** | CPU caches frequently accessed memory addresses. |
| **Memory Allocators** | OS allocates and deallocates memory efficiently for processes and kernel. |


## What is virtual memory? Why do we need it? How does it work?

Virtual memory is an abstraction layer that allows a process to use more memory than is physically available in RAM. It provides an illusion of a large, continuous memory space, while the OS manages mapping between virtual and physical addresses.

### Key Features of Virtual Memory:
- Uses both RAM and disk storage (swap space).
- Each process gets its own address space (isolated from others).
- Allows efficient memory allocation and process execution.
- Uses paging to map virtual memory to physical memory.

Virtual memory is a memory management technique where secondary memory (eg disks) can be used as main memory like RAM. Virtual memory allows computers to operate quite normally when memory shortages happen.

When a process is in use, its data is stored in a physical address using RAM. If at any point, RAM space is needed for something more urgent, data can be swapped out of RAM into virtual memory and swapped back again when needed.
### Why Do We Need Virtual Memory?
-  Allows Running Large Programs
- Process Isolation (Security & Stability)
- Multitasking & Efficient Memory Usage
- Enables Memory Protection
## How large virtual memory is?

The size of virtual memory is unlimited. Actually, it is limited by the disk space but the disk space is quite large so we can consider it unlimited.

## What is paging?

Paging is the memory management mechanism that allows OS to get the processes from the secondary memory to the physical memory in form of fixed-size blocks called pages.

## Can 2 processes map to the same physical address? How and in which case?

Yes. Each process has its virtual memory address and can be mapped to the same physical memory address when it is processed by CPU. The condition is they need to take turns to be processed.

## What is heap space and stack space?

## **Stack vs Heap: Key Differences and Use Cases**  

### **1. Overview**  
Both the **stack** and **heap** are memory regions used in a program, but they serve different purposes.

| Feature  | **Stack** | **Heap** |
|----------|---------|--------|
| **Speed** | **Fast** (direct memory access, LIFO structure) | **Slower** (dynamic allocation, requires pointer management) |
| **Size Limit** | **Small** (limited per thread, ~1MB - 8MB) | **Large** (depends on system memory) |
| **Allocation Type** | **Automatic** (managed by function calls) | **Manual/Dynamic** (`malloc/free`, `new/delete`, or garbage collector) |
| **Access Pattern** | **LIFO (Last In, First Out)** | **Random access** (scattered in memory) |
| **Lifetime of Memory** | **Short-lived** (freed after function returns) | **Long-lived** (persists until explicitly freed) |
| **Memory Fragmentation** | **No fragmentation** (stack is continuous) | **Possible fragmentation** (memory blocks scattered) |
| **Typical Use Cases** | Function calls, local variables, control flow | Dynamic data structures (linked lists, trees, objects) |
| **Risk** | **Stack Overflow** (deep recursion, too many local variables) | **Memory Leaks** (if memory is not freed) |

---

### **2. When to Use Stack vs Heap?**
#### ✅ **Use Stack When:**
- You need **fast memory allocation/deallocation**.
- You have **small, short-lived variables** (e.g., function-local variables).
- You need **thread safety** (each thread has its own stack).

#### ✅ **Use Heap When:**
- You need **dynamic memory allocation** (e.g., linked lists, trees, large objects).
- You want **data to persist beyond function calls**.
- You have **large memory needs** that exceed stack limits.

---

### **3. Why Stack is Faster Than Heap?**
1. **Stack uses a contiguous memory block** → Faster cache access.
2. **Stack memory allocation is automatic** → No overhead of dynamic allocation.
3. **Heap requires memory management** → Slower due to fragmentation and pointer dereferencing.


## What will happen with memory when you open an application?

The application will be loaded to RAM when we open an application.

## What will happen when you call another function (with parameters) or return from a function?

When we call another function, local variables of the new function will be put to stack, and the stack register will be moved to the appropriate position. If the function takes parameters, it will create a duplicate version of those parameters (If the parameter is a pointer, it still creates a new pointer that points to the same address).

When a function returns, local variables will be pop out of stack and the stack pointers will also be moved to the appropriate position.

## What will happen with stack? (why we do not use heap here?)?

- 🚀 Stack is used because it’s fast, automatic, and efficient for short-lived data.
- 🚀 Heap is only used when we need dynamic, long-lived memory.

## What will happen with registers?

As above.

## What causes stack overflow?

Stack overflow happens when the stack memory runs out. The most common cause of stack overflow is infinite recursion.

## What is dynamic allocating? How does it work?

Dynamic allocation is the act of asking for a specific segment of memory from the OS.

## How does deallocation work?

Deallocation announces for the OS that this segment of memory is free now and can be used by other processes.

## What happens when your computer is full of memory?

When a computer runs out of memory (RAM), the operating system tries to free up space by moving data to swap on disk, but this makes the system much slower. If both RAM and swap are full, new programs may fail to start, applications can crash, or the OS might kill processes or become unresponsive.

Extra (if asked for solutions):
- Close unused apps.
- Add more RAM.
- Increase swap space (if possible).
- Optimize software usage.



## Why you do not need to "deallocate" local variable?

Cause local variable is allocated in stack and will be destroyed automatically after the function returns.

## How does Garbage Collection work? When it will be triggered?

Garbage Collector (GC) will detect which object in heap memory has no reference. It means this object cannot be used anymore and it's safe to sweep this object away. GC will run periodically for languages that have GC. Besides, languages like Java or Python have a mechanism to invoke GC manually.

## What is a pointer? What difference between pass by value and pass by reference?

Pointer is the address of a variable. Pass by value means passing only the value of the variable, while pass by reference means passing the address of this variable in the memory.

## Where global variable will be saved?

Process memory is divided into four sections:

- Text section: when the code is stored.
- Data section: when static and global variables are stored.
- Stack section: where local variables are stored.
- Heap section: where dynamically allocated variables are stored.

## Why in Linux everything is "file"?

It will create a uniform interface for operations on Linux systems. To be more exact, everything in Linux is a stream of bytes that is represented as a file descriptor.

## How does mouse/keyboard/monitor... communicate with your computer?

It streams a stream of bytes into our computer.

## What is file descriptor?

File descriptor is a unique identifier for a file or input/output resources.

## What is buffer? Why do we need buffer?

Buffer is a segment of memory that is reserved to store necessary data being processed. Buffer helps reduce the processing time.

## What will happen if 2 processes read/write to the same file?

If 2 processes read/write to the same file, it means they use the same file descriptor to access this file, also the file offset. So the changes that one process does with the shared file are visible to the other process. ([More](https://walkerlala.github.io/archive/what-if-write-to-the-same-file.html))

## What is system call (syscal)?

A syscall is a programmatic way that allows a computer program to request a service from the kernel of the operating system, on which the program will be executed.

## How to do a syscal?

Some libraries provide functions that allow us to interact with the kernel.

## What happens with CPU when we execute a syscal?

CPU will execute the system call handler, which will perform some action to fulfill the syscall. ([More](https://www.ibm.com/docs/en/aix/7.2?topic=calls-understanding-system-call-execution))

## What is user space and kernel space?

Memory is divided into kernel space and user space:

- Kernel space is where the code of the kernel is stored, and executed.
- User space is where the code of everything other than the kernel is stored and executed.

## What is in-memory cache? (memcached/redis)

In-memory cache is the data storage layer between the application and the database. It stores data in memory which has a much fast data access speed so it is used to speed up the application.

## LRU? implement LRU in your program language! (How about multi-thread?)

Least recently used is a strategy to invalidate cache. As the name already said, it will remove the least recently used data when the cache is full. We can implement LRU using a combination of hashmap and a doubly linked list.

## How to migrate Cache stampede?

Cache stampede happens when a lot of data need to be queried at the same time but most of them haven't been cached yet, so the number of queries that need to be executed on database is huge. We can mitigate cache stampede by warming cache, cache locking, or predicting when the cache is expired so we can update cache.

## Quicksort vs Merge sort. Which is faster? Why? How they use these 2 sorting algorithms real life?

To be defined.
## Synchronous vs Asynchronous
The terms synchronous (sync) and asynchronous (async) are fundamental in computing, networking, and programming. They describe how tasks or processes execute concerning time and waiting.

### 1️⃣ Synchronous (Sync)
Definition
In synchronous execution, tasks are performed sequentially, meaning that each task must complete before the next one starts.
The caller waits for a response before proceeding.

**Example in Networking**
#### Synchronous TCP communication:

- The client sends a request and waits for the server's response before doing anything else.
- Blocking Behavior: The client pauses execution while waiting for the server’s response.
### 2️⃣ Asynchronous (Async)
Definition
In asynchronous execution, tasks can start without waiting for previous tasks to complete.
The caller does not wait; instead, it can perform other tasks while waiting for the response.
Example in Networking
#### Asynchronous TCP communication (non-blocking sockets):
- Non-Blocking Behavior: The program doesn’t pause execution and can handle other tasks while waiting for data.

### 3️⃣ Key Differences

| Feature      | Synchronous (Sync)       | Asynchronous (Async)       |
|-------------|--------------------------|----------------------------|
| **Execution** | Sequential (one after another) | Concurrent (tasks can overlap) |
| **Blocking?** | Yes, waits for task completion | No, can continue doing other work |
| **Performance** | Slower if waiting for I/O | Faster for I/O-bound tasks |
| **Complexity** | Simpler to implement | More complex (requires event loops, callbacks) |
| **Use Case** | CPU-bound tasks (math calculations) | I/O-bound tasks (networking, file reading) |


## 32-bit vs 64-bit Architecture

## **1. Definition**
- **32-bit Architecture**: Uses 32-bit wide registers, memory addresses, and data buses.
- **64-bit Architecture**: Uses 64-bit wide registers, memory addresses, and data buses.

## **2. Key Differences**

| Feature            | 32-bit Architecture | 64-bit Architecture |
|-------------------|------------------|------------------|
| **Register Size** | 32-bit           | 64-bit           |
| **Addressable Memory** | Up to **4GB** (2³²) | Up to **16 exabytes** (2⁶⁴) theoretically, practical limit ~256TB |
| **Performance** | Slower for large computations | Faster due to more registers & wider data paths |
| **OS Compatibility** | Supports only 32-bit OS | Can run both 32-bit and 64-bit OS |
| **Application Support** | Only 32-bit apps | Supports both 32-bit & 64-bit apps |
| **Data Bus Width** | 32-bit          | 64-bit          |

## **3. Why Is There a Difference?**
- **Memory Addressing**: 64-bit can access **more memory** efficiently.
- **Processing Power**: 64-bit CPUs handle **larger data chunks** at once, improving performance.
- **Security**: 64-bit systems offer better security features (like ASLR, DEP).
- **Backward Compatibility**: 64-bit CPUs can run 32-bit programs, but 32-bit CPUs cannot run 64-bit software.

### **Conclusion**
64-bit architecture is superior for modern computing, enabling more memory, better security, and improved performance.


## design a memory allocator
A memory allocator is responsible for managing dynamic memory allocation and deallocation in an application. 

## 🧠 Goal

Design a memory allocator that:

* Allocates memory of requested size (`malloc`)
* Frees memory (`free`)
* Minimizes fragmentation
* Is efficient in time and space

---

## 🔧 Components of the Design

### 1. **Heap Space Management**

* The allocator manages a large, contiguous block of memory (simulated or actual).
* This "heap" is divided and tracked by the allocator — not the OS.

---

### 2. **Metadata Structure**

Each block in memory has metadata:

```
[Header | Payload | Footer (optional)]
```

**Header** stores:

* Block size
* Status (free/used)
* Possibly links to other blocks (for free list)

---

## 🧱 Memory Block Strategies

### ➕ Allocation

* Align memory to 8/16-byte boundaries.
* Use **best-fit**, **first-fit**, or **next-fit** strategy to find suitable free block.
* Split larger blocks if necessary:

  ```
  [Free block of size 128] → [Allocated 64][Free 64]
  ```

### ➖ Deallocation

* When `free(ptr)` is called:

  * Mark block as free
  * Optionally coalesce with adjacent free blocks to reduce fragmentation

---

## 🔄 Free Space Management

### ✅ Options:

| Strategy        | Description                           |
| --------------- | ------------------------------------- |
| Free list       | Linked list of free blocks            |
| Segregated list | Lists for different block sizes       |
| Bitmap          | Bitmask to indicate allocation state  |
| Buddy system    | Power-of-2 splits for fast coalescing |

---

## 🧮 Fragmentation Control

### 🔹 Internal fragmentation:

* Due to alignment or over-allocation
* Minimized via smart size rounding and block splitting

### 🔸 External fragmentation:

* Scattered free blocks too small to use
* Controlled via coalescing or compaction (if movable)

---

## 🕸️ Thread Safety (Optional)

* Use **mutexes** or **lock-free structures** for concurrent allocators
* Or use **per-thread arena allocators** to reduce contention

---


## 🧠 Interview Summary

> “I’d manage a large heap with metadata per block. I'd maintain a free list (or segregated bins) for fast lookup, split large blocks on allocation, coalesce on free, and align sizes to avoid fragmentation. For concurrency, I’d use thread-local arenas or locks.”

## Suppose that the memory allocator works, how do you avoid race condition if there are multiple threads in caller application?

“To avoid race conditions, I’d start with a global lock for simplicity. For scalability, I’d partition memory using thread-local arenas. In high-performance systems, I’d consider lock-free structures for managing free lists or fixed-size slab pools.”
# 1. Virtual Machine

## 1.1 Definition
- A **Virtual Machine (VM)** is an emulation of a physical computer system.
- Provides virtualized hardware (CPU, memory, storage, network interfaces) on which an operating system and applications can run.
- Enables **isolation** between the host system and the guest OS, as well as between different VMs running on the same host.

## 1.2 Common Use Cases
- **Server Consolidation**: Running multiple server instances on one physical machine.
- **Testing & Development**: Isolated environments for safe software experimentation.
- **Cloud Computing**: Providers like AWS, Azure, and GCP offer VM instances as on-demand resources.

---

# 2. Virtual Memory

## 2.1 Definition
- **Virtual Memory** is an abstraction that gives processes the **illusion** of having contiguous, large address spaces.
- The OS manages the mapping of **virtual addresses** to **physical memory** (RAM), often in fixed-size blocks called **pages**.
- Allows features like **paging**, **swapping**, and **protection** (each process has its own virtual address space).

## 2.2 Benefits
- **Isolation**: One process cannot directly access another process’s memory.
- **Efficient Memory Usage**: Allows over-commitment, where the sum of all virtual memory can exceed physical memory.
- **Protection**: Ensures that rogue processes cannot corrupt another process’s memory or the kernel’s memory.

---

# 3. Hypervisor

## 3.1 Definition
- A **Hypervisor** (or Virtual Machine Monitor, VMM) is software or firmware that creates and manages virtual machines by abstracting the underlying hardware.
  
## 3.2 Types of Hypervisors
1. **Type 1 (Bare Metal)**: Runs directly on the host hardware (e.g., VMware ESXi, Microsoft Hyper-V, Xen). Often used in data centers.
2. **Type 2 (Hosted)**: Runs on top of an existing OS (e.g., VMware Workstation, VirtualBox). Common on desktops for development and testing.

## 3.3 Responsibilities
- Allocating host resources (CPU, memory, storage) to each VM.
- Isolating VMs from one another to maintain security and stability.
- Handling VM lifecycle tasks like creation, deletion, and snapshotting.

---

# 4. Middleware

## 4.1 Definition
- **Middleware** is software that sits between an application and the underlying platform (OS, database, network).
- Provides **common services** and **capabilities** like messaging, authentication, API gateways, or data integration.

## 4.2 Examples
- **Application Servers**: (e.g., JBoss, WebLogic) that provide Java EE services.
- **Message Brokers**: (e.g., RabbitMQ, Kafka) that handle asynchronous communication among services.
- **API Gateways**: (e.g., Kong, NGINX-based solutions) that handle routing, load balancing, and security at the edge.

## 4.3 Use Cases
- **Service-Oriented Architectures (SOA)** or **Microservices** that need reliable messaging and integration.
- **Enterprise Integration**: Connecting multiple enterprise systems with common data formats and protocols.
- **Cloud Applications**: Providing cross-cutting concerns like logging, monitoring, and security.

---

# 5. Other Related Terms

### 5.1 Containers
- **Lightweight virtualization** at the OS level (e.g., Docker, LXC).
- Each container shares the host OS kernel but has isolated user space.

### 5.2 Container Orchestration
- Tools like **Kubernetes**, **Docker Swarm**, **Apache Mesos** that automate the deployment, scaling, and management of containerized applications.

### 5.3 Emulator vs. Hypervisor
- **Emulator**: Imitates the entire hardware architecture (e.g., CPU instruction set) in software, allowing one platform’s software to run on different hardware.
- **Hypervisor**: Typically uses hardware-assisted virtualization to share the same architecture as the host, resulting in better performance than full emulation.

### 5.4 Paravirtualization
- A technique where the guest OS is modified to be aware it is running in a virtualized environment, leading to improved performance (e.g., Xen paravirtualized guests).

### 5.5 OS-level Virtualization
- Another term for containerization (e.g., Docker, LXC), where the kernel itself handles multiple isolated “containers” within the same host.

### 5.6 Virtual Appliance
- A pre-configured virtual machine image containing an OS plus an application, ready for deployment on a hypervisor.

### 5.7 Snapshots
- A point-in-time image of a VM’s disk and memory state, allowing quick rollback or cloning.



## What is Garbage Collector (GC) and how does it work? Where does Garbage Collector detect variables that have no reference? Under normal conditions, how to set the timing to run Garbage Collector?

## 🧹 What is Garbage Collector?

A **Garbage Collector (GC)** is a **memory management system** that **automatically frees memory** occupied by **objects no longer in use**, preventing **memory leaks** and improving performance.

> 🧠 Think of it as an automatic janitor that deletes unreachable objects to reclaim heap space.

---

## ⚙️ How Garbage Collector Works (Conceptually)

### 🔍 GC detects objects that:

* Are **no longer reachable** from the program’s "root set" (e.g. stack, static variables, global references).
* Have **zero references** (reference count = 0) or are **not accessible** via any path.

---

### ✅ Common Strategies:

| Method                       | Mechanism                                                                    |
| ---------------------------- | ---------------------------------------------------------------------------- |
| **Reference Counting**       | Deletes objects with no incoming references. Simple but fails on cycles.     |
| **Tracing (Mark-and-Sweep)** | Marks reachable objects and sweeps the rest as garbage. Handles cycles.      |
| **Generational GC**          | Classifies objects by age (young, old) and collects young ones more often.   |
| **Stop-the-world**           | Pauses execution to run GC cycle (used by many runtimes like Java, CPython). |
| **Incremental/Concurrent**   | Runs GC in small parts or parallel to app (used in modern JVM, Go).          |

---

## 📍 Where Does GC Look for Unreachable Variables?

> GC **starts from root references** and **traces all reachable objects**.

### Root references include:

* Stack variables (local to functions)
* Global/static variables
* CPU registers
* Objects referenced by active threads

> Any object **not transitively reachable from a root** is **garbage**.

---

## ⏱️ When Does GC Run?

### 🔁 Default Behavior (Language-Specific)

| Language             | GC Trigger                                                           | Notes                                 |
| -------------------- | -------------------------------------------------------------------- | ------------------------------------- |
| **Java**             | Heap thresholds, allocation pressure, explicit `System.gc()`         | JVM uses generational + concurrent GC |
| **Python (CPython)** | Object count & thresholds in **reference counting + cycle detector** | Triggers when thresholds are crossed  |
| **Go**               | Heap growth, idle CPU time, automatic tuning                         | Uses concurrent GC                    |

---

### 🛠️ Can You Control GC Timing?

Yes, **partially**, depending on the language:

#### ✅ Java

```java
System.gc();  // Suggests JVM to run GC (not guaranteed)
```

#### ✅ Python

```python
import gc
gc.collect()  # Manually trigger GC
gc.set_threshold(700, 10, 10)  # Tune automatic triggers
```

#### ✅ Go

```go
runtime.GC()  // Manual GC trigger
```

> ⚠️ **For performance-critical systems**, GC tuning is often essential (heap size, pause time, frequency).

---

## 🧠 Interview Tip

> “Garbage collectors automatically reclaim unreachable memory. They typically trace from root references to detect unused objects. Though we can't always control exactly *when* GC runs, most languages let us **tune** or **suggest** GC behavior.”

## With peak hour, too many connections, too high memory, how to run GC?

## 🚨 Problem: Peak Hour Pressure

* High number of active threads/connections = more live objects.
* More allocations = faster heap fill-up.
* Frequent or long GC pauses can cause:

  * Increased **latency**
  * **Throughput drops**
  * **OutOfMemoryError** (in Java) or memory bloat (in Python)

“During peak hours, proactively manage GC by tuning thresholds, increasing heap size, using concurrent collectors (like G1GC or Go’s GC), and triggering manual GC before peak. Monitoring and preemptive cleanup are key to minimizing GC latency spikes under heavy load.”

## ❓ **"With peak hours, too many connections, too many long-lived connections — how to run Garbage Collection (GC) effectively?"**


### 🧠 Problem Context

Under peak load:

* 📈 Thousands of **long-lived connections** (e.g. WebSocket, HTTP keep-alive).
* 🧠 Lots of **in-memory state** retained longer (e.g. session objects, buffers).
* 🐢 GC may **not reclaim memory aggressively**, or **pause too long**.
* ⚠️ Risk: **Memory leaks**, **GC thrashing**, **OutOfMemoryError**, or **latency spikes**.

## 🧠 Interview Soundbite

> “Under heavy load with long-lived connections, I run a preemptive GC before peak, tune GC for low latency and long-lived objects, and monitor heap usage to trigger reactive GC. I also reduce GC pressure using pooling and clean reference management.”


## what is deadlock? what is minimum number of threads required for deadlock to occur? can deadlock occur with only 1 thread

Here's a **precise and interview-friendly breakdown** of **deadlock**, its conditions, and thread requirements:

---

## 🔒 What is Deadlock?

A **deadlock** is a situation where **two or more threads are waiting indefinitely for resources held by each other**, causing all of them to be **blocked forever**.

> 💡 Think of it as two people holding one half of a pen each, and both waiting for the other to let go.

---

## 🧱 4 Coffman Conditions for Deadlock

Deadlock can only occur if **all** of these are true:

| Condition            | Meaning                                                        |
| -------------------- | -------------------------------------------------------------- |
| **Mutual Exclusion** | A resource can be held by only one thread at a time            |
| **Hold and Wait**    | A thread holds a resource while waiting for another            |
| **No Preemption**    | Resources cannot be forcibly taken — only released voluntarily |
| **Circular Wait**    | Circular chain of threads waiting for each other's resources   |

---

## 🔢 Minimum Number of Threads Required for Deadlock?

### ✅ **Minimum = 2 threads**

* **Thread A** holds **Resource X**, waits for **Resource Y**
* **Thread B** holds **Resource Y**, waits for **Resource X**

This forms a **circular wait**, the simplest deadlock.

---

## ❓ Can Deadlock Occur with Only **1 Thread**?

### 🔴 **No, not traditional deadlock** (based on mutual blocking between entities).

However:

### 🟡 But! A **self-deadlock** (a.k.a. reentrant deadlock) *can* occur if:

* A thread **waits on itself** due to incorrect synchronization logic.

#### Example (Python pseudocode):

```python
lock.acquire()
lock.acquire()  # Deadlock: waiting for itself (non-reentrant lock)
```

This is **not a multi-thread deadlock**, but it's a **programming bug** leading to the thread being blocked forever.

---

## 🧠 Interview Summary

> “Deadlock happens when threads hold resources and wait on each other in a cycle. You need **at least 2 threads** to have a classic deadlock, but a single thread can cause a **self-deadlock** if it tries to re-acquire a non-reentrant lock it already holds.”

## docker engine vs VM about manage resource

| Feature                        | **Docker Engine (Containers)**                            | **Virtual Machine (VM)**                                         |
| ------------------------------ | --------------------------------------------------------- | ---------------------------------------------------------------- |
| **Abstraction Level**          | OS-level (shares host kernel)                             | Hardware-level (emulates entire OS + kernel)                     |
| **Resource Isolation**         | Partial (via cgroups, namespaces)                         | Strong (each VM fully isolated via hypervisor)                   |
| **Startup Time**               | Very fast (\~milliseconds)                                | Slow (\~seconds to minutes)                                      |
| **Memory Overhead**            | Low (no guest OS)                                         | High (each VM runs its own OS)                                   |
| **CPU Allocation**             | Shared, but limited via `cpu_shares`, `cpus`, `cpu_quota` | Dedicated virtual CPUs (vCPUs); strict core reservation possible |
| **Memory Allocation**          | Shared, limited via `memory` or `mem_limit`               | Fixed or dynamic allocation (swap, ballooning, etc.)             |
| **Disk I/O Control**           | With device-mapper / overlay2 + blkio controls            | More granular via hypervisor-level control                       |
| **Resource Efficiency**        | High — containers share host kernel                       | Lower — full OS overhead per VM                                  |
| **Security Boundary**          | Weaker (kernel shared)                                    | Stronger (separate kernels per VM)                               |
| **Live Migration / Snapshots** | Limited or experimental                                   | Mature snapshotting and migration support                        |

---

## 🔧 Example: How Docker Manages Resources

```bash
# Limit CPU to 2 cores and memory to 512MB
docker run --cpus="2.0" --memory="512m" my_app
```

Behind the scenes:

* **CPU**: controlled by **cgroups** and **CPU shares/quota**
* **Memory**: hard and soft limits via **memory cgroups**
* **Isolation**: **Linux namespaces** isolate file system, PID, network, etc.

---

## 🧠 Interview Summary


> “Docker containers manage resources using kernel features like cgroups and namespaces. They’re lightweight and fast but offer weaker isolation compared to VMs, which use hypervisors to fully virtualize hardware and OS, at the cost of more overhead.”
