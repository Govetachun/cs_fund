# Topic: NETWORKING

## How TCP open a connection? What does it need to open a connection?

TCP needs to open a connection to determine if the server is available to respond and know there is a way from it to the server before transferring data. To open a connection, firstly server must be bound and listen to a port, it is passive open. Then a process called 3 way handshake happens:

- Client sent SYN packet to the server with sequence num A
- Server sends SYN-ACK packet to the client with rand sequence num B and ack num A' = A+1
- Client sends ACK packet to the server, with ack num B' = B+1 and the sequence number is A' ( the ack num it received in step 2)

## Why there are 3 way handshakes but not 2 way?

2-way will establish a half-duplex connection only. It means this is a one-direction connection the client is the only sender and the server is the only receiver. 3-way will establish a full-duplex connection, both client and server are sender and receiver.

## What is syn, ack mean?

Syn means synchronizaton. SYN packet is sent when the sender tries to connect to the receiver.

Ack means acknowledgment. ACK packet is sent by the receiver to the sender to help the sender know that the SYN packet that it sent before reached the destination successfully.

## Why they have to send 2 "random" sequence numbers? The purpose of this sequence number?

Sequence numbers are used to keep track of the order of data. The initial sequence number needs to be chosen randomly so the data in one flow will not be conflicted with other data bytes in other flows which are transmitted in the same TCP connection. Because sequence number is 32-bit integer so the maximum value of a sequence number is 2^32 - 1. So if the amount of data is greater than 4GB, at least one sequence number value will be reused.

## What if the 3rd handshake fail? How the server can detect it and what does it do in this case?

A two-way handshake is not sufficient for reliable connection establishment because TCP needs to ensure both sides are synchronized and ready to transmit data safely.

### 1️⃣ What Would Happen in a Two-Way Handshake?
A two-way handshake (like in some simpler protocols) could work as follows:

Client sends SYN (Sequence Number X)
Server responds with ACK (ACK = X + 1)\
💡 Issue: The server does not get a confirmation that the client received the ACK, so the connection is not fully synchronized.

### 2️⃣ Problems with a Two-Way Handshake
A two-way handshake does not account for:

Lost Packets 🛑

If the server's response (ACK) is lost in transit, the client won't know whether the connection was accepted or not.
The server might think a connection is established while the client is still waiting for a response.
Old/Duplicated SYN Packets 🛑

If a delayed or retransmitted SYN from an old connection arrives, the server might incorrectly assume a new connection was initiated.
Full-Duplex Synchronization (Both Sides Must Be Ready) 📶

In TCP, both parties need to agree on sequence numbers for reliable, ordered data transfer.
The client needs to verify that the server’s sequence number has been received before data transmission.
### 3️⃣ Why the Three-Way Handshake Works
The three-way handshake solves these issues by ensuring mutual confirmation.

- ✅ Step 1: Client sends SYN (Seq = X).
- ✅ Step 2: Server sends SYN-ACK (Seq = Y, ACK = X + 1).
- ✅ Step 3: Client sends ACK (ACK = Y + 1).

Now, both sides know they are ready to communicate with synchronized sequence numbers.
If a packet is lost, the retransmission mechanism ensures reliability.
### 4️⃣ Analogy: Phone Call Connection 📞
Imagine calling someone:

- Step 1: You say, "Hello, can you hear me?" (SYN)
- Step 2: They reply, "Yes, I can hear you! Can you hear me?" (SYN-ACK)
- Step 3: You respond, "Yes, I can hear you too!" (ACK)
- Now, both parties are sure the connection is stable and can start talking.
Without the third step, one side might think the connection is active, but the other might not.

### What Causes the Third Handshake to Fail?
The third handshake (ACK from the client) may fail due to:

- Network failure (packet loss, unstable connection)
- Client crash (client machine shuts down unexpectedly)
- Malicious attack (SYN flood attack, preventing ACK responses)
- Firewall issues (ACK packet blocked by network security rules)
### How the Server Detects a Failed Third Handshake
The server, upon sending the SYN-ACK, transitions into the SYN-RECEIVED state and waits for the final ACK. If this does not arrive, the server uses timeouts and retransmissions to detect failure.
## How TCP handles the connection?
|Scenario |	Server Response|
|-------------|--------------------------|
|ACK is received |	Connection moves to ESTABLISHED state.|
|ACK is lost (temporary network issue) |	Server retries SYN-ACK a few times.|
|Client crashes or disconnects |	Server times out and removes the connection.|
|SYN Flood Attack |	Server may use SYN cookies to protect resources.|

TCP (Transmission Control Protocol) opens a connection using the three-way handshake process. This handshake ensures that both the client and server are ready to communicate and agree on initial parameters like sequence numbers.

### What TCP Needs to Open a Connection
For a successful TCP connection, the following elements are required:

- **Client & Server IP Addresses:** The two endpoints must know each other's IP addresses.
- **Port Numbers:** Each side must specify a port number (e.g., HTTP uses port 80, HTTPS uses port 443).
- **Initial Sequence Numbers (ISN):** Both client and server generate random ISNs to track the order of packets.
- **Three-Way Handshake Completion:** Without completing all three steps, a TCP connection is not fully established.
- **No Firewall or Network Restrictions:** If firewalls block SYN packets or certain ports, the connection will fail.

## What happens if some bits are wrong due to connection errors? How to detect them and fix them?

When data is transmitted over a network, bit errors can occur due to interference, noise, or packet corruption. TCP has built-in mechanisms to detect and recover from these errors to ensure reliable data delivery.

If some bits are wrong, the checksum of this packet will not match. In this case, the receiver will discard this packet.

## How the timeout is handled? what if the timeout is expired?
### 1️⃣ Concept of Timeout in TCP
A timeout occurs when the sender does not receive an expected ACK within a calculated time frame. The timeout duration is called the **Retransmission Timeout (RTO)**.

2️⃣ What Happens When Timeout Expires?
If a timeout expires before an ACK is received:

Retransmit the Lost Packet
Increase the Timeout (Exponential Backoff)
Reduce Transmission Rate (Congestion Control)

|Scenario	|TCP Response|
|-------------|--------------------------|
|Packet sent, ACK received on time|	Normal transmission continues|
|Packet sent, ACK delayed but within timeout	|No retransmission, TCP updates RTT estimation|
|Packet sent, ACK not received before timeout expires|	Retransmission triggered, RTO increased (exponential backoff)|
|Multiple timeouts occur|	Sending rate reduced (congestion control)|

## What will happen if some "packet" is missing on the way?

When a packet (TCP segment) is lost in transit due to network congestion, interference, or hardware failure, network congestion is assumed to happen. TCP has its own way to deal with network congestion. It is called TCP Congestion control. 

TCP has built-in recovery mechanisms to detect the loss and retransmit the missing packet.It starts when slow-start phrase, in which the transmit rate (congestion window) grows exponentially, doubling each round trip time. When a packet is lost, congestion avoidance begins. The transmit rate is cut into half, then increase linearly, usually by 1 each RTT.

|Mechanism	|When It Happens	|Action Taken	|Speed of Recovery|
|-------------|-----------------|-------------|-----------------|
|Retransmission Timeout (RTO)	|No ACK received before timeout expires	|Retransmit packet, double RTO, slow down sending|	Slow (Waits for timeout)
|Fast Retransmit (3 Duplicate ACKs)	|Receiver detects missing packet but keeps receiving later packets	|Immediately retransmit missing packet	|Fast (No timeout wait)|
|Fast Recovery (Congestion Control)	|After fast retransmit, avoids slowing down too much|	Adjusts congestion window to prevent overreaction|	Optimized Speed|
## How to detect the appropriate number of packets to send (speed of sending packet)?

Like above.

## How TCP close the connection?

It does a 4-way handshake. Firstly peer A send a FIN packet to peer B. Then peer B send an ACK packet for the FIN it receives. After that peer B send it own FIN packet to peer A. Then peer A send ACK packet to the peer B. Connection is over.
Step 2 and 3 can be merged by one, so it will be 3-way handshake instead.

## What if the internet is dropped in the middle of the connection? Or in case one peer is crash?

When a TCP connection is active and one side loses connectivity (due to network failure, system crash, or power loss), TCP must detect this failure and handle it gracefully. TCP does not immediately detect disconnections but relies on **timeouts and keep-alive mechanisms** to recognize a lost peer.


## How long you can keep a TCP connection alive?

To be defined

## What are the differences between TCP and UDP? And in which case we use which?

The key difference between TCP and UDP is TCP has a mechanism to resend loss packets, but UDP doesn't. So TCP suits in the case where packet loss can not be acceptable, like transfer file, request... UDP suits in the case packet loss can be tolerated, like in real-time voice/video calls.

## How Ping command works? What is TTL? How does TTL will be changed?

The ping command is used to check the reachability of a network device and measure **round-trip time (RTT)**. It works by sending **ICMP (Internet Control Message Protocol)** Echo Request packets to a target host and waiting for ICMP Echo Reply packets.

TTL (Time To Live) is a field in the IP header that prevents infinite looping of packets in a network.

Time-to-live is a mechanism that prevents a packet to be forward around the internet forever. Like the name, it is the number of times or hops, that packet can be forward before it is discarded by a router. Each time a router receives a packet, it reduces TTL of that packet by 1. If TTL reaches zero, the router will discard this packet then send an ICMP message back to the sender. The recommended default value of TTL is 64.

## How HTTP works?

HTTP is an application protocol that transfers hypertext messages between client and server. It uses specific request methods to perform specific tasks. They include:

- POST: create a new entity
- PUT: update an entity entirely
- PATCH: update an entity partially
- GET: get value
- HEAD: get value without data
- OPTION: get all the methods that the server supports for this resource
- ...

## Why did people say that HTTP is stateless? The reason they make it stateless?

**HTTP (HyperText Transfer Protocol)** is considered stateless because each request from a client to a server is independent, and the server does not retain information about previous requests.. Stateless provides high scalability.
### Why Was HTTP Designed to Be Stateless?

#### 1️⃣ Simplicity & Scalability
- Statelessness makes HTTP simple and easy to implement.
- Servers can handle millions of requests without tracking individual sessions.
#### 2️⃣ Performance & Resource Efficiency
- No need to store user session data → Lower memory and CPU usage.
- Each request is independent → Load balancing is easier.
#### 3️⃣ Fault Tolerance
- No session dependency → If a server crashes, another server can handle the request.
- Horizontal scaling is easier because no session synchronization is required.
#### 4️⃣ Caching Benefits
- Since each request is self-contained, caching HTTP responses becomes efficient.
- CDNs (Content Delivery Networks) can serve cached content without needing a session.

### How to Handle State in HTTP?

Even though HTTP is stateless, developers use workarounds to store user state:

| Method                        | How It Works |
|--------------------------------|-------------|
| **Cookies**                    | Small pieces of data stored in the browser, sent with every request. |
| **Sessions (Server-side storage)** | Store user data in a database, referenced via a session ID. |
| **JWT (JSON Web Token)**       | Encodes session info in a self-contained token, avoiding server storage. |
| **Local Storage / Session Storage** | Stores data in the browser but is not automatically sent with requests. Useful for client-side state management. |

## Can we make a persistent HTTP connection? pros and cons of this way?

Yes.
HTTP persistent connection keeps one connection to send many objects. It can help reduce CPU resources, travel time, network traffic... But resources will be occupied and not be available to others.

## Why HTTP require cookie each time we send the request?

No it doesn't required cookie.

## Can someone use your cookie and log in your Facebook account? How to migrate this?

Yes! If an attacker steals your session cookie, they can impersonate you and access your Facebook (or any other web account) without needing your password. This type of attack is called Session Hijacking.

1️⃣ How Does Session Hijacking Work?
When you log into Facebook, a session cookie **(e.g., sessionid=abcd1234)** is stored in your browser.
This cookie is automatically sent with each request, allowing Facebook to recognize you.
If an attacker steals this cookie, they can use it on their own browser to access your account.

# How to Prevent Cookie Hijacking?

| Defense Mechanism | How It works  | Effectiveness  |
|--------------------|------------------------------------|---------------|
| **Use HTTPS (Secure Encryption)**         | Encrypts cookie data in transit to prevent sniffing.         | ✅ Strong    |
| **Set HttpOnly Flag**                      | Prevents JavaScript from accessing cookies (blocks XSS attacks). | ✅ Strong    |
| **Multi-Factor Authentication (MFA)**     | Even if an attacker gets your cookie, they still need another authentication factor. | ✅ Very Strong |

 What Can You Do If Your Cookie Is Stolen?
- A. Log Out All Sessions
- B. Change Your Password
If your session is hijacked, changing your password invalidates the stolen cookie.
- C. Enable Two-Factor Authentication (2FA)
## What is HTTP session? How does authentication work in HTTP? What is JWT?

Cause HTTP is stateless, HTTP session and cookie are two ways to connect some requests together.

Session is created and stored at server side, usually just temporary. Session ID will be sent to client, client may submit it to server as a cookie, or url parameter, so server will know which session this request belongs to. Session ends when user logout, close web browser, or timeout. Session is more secure, compared to cookie.

Cookie is stored at client browser. Cookie will expire at the set time.

JWT is standard for creating data with opt-in signature and encryption, used for authentication.

## Which type of "data" HTTP can help us to get or push? (binary file? image? text file? video file? music file?)

HTTP can help us get or push all kinds of files like text, image, binary, video, music...

## REST/RESTful?

REST stands for representational state transfer. It's a set of constraints that describes how an API should work. It includes four main principles:

- Client-Sever: There is always a client and a server. Client sends request to server and server reply with response.
- Stateless: Server handles client request independently. Client's request must contain enough information that server requires.
- Uniform interface: For example, a resource is identified in URI, HTTP response always comes with status and body.
- Cacheability: Client can cache response.

An API that follows REST constraints is called RESTful.

## AJAX technique?

AJAX uses XMLHttpRequest to communicate with server. The first "A" stands for asynchronous, which means using AJAX, we can communicate with server without reloading the page.

## How HTTPS work?

HTTPS uses SSL - Secure Socket Layer to protect the communication by encryption. Before exchanging data, an SSL connection must be established first, then data will be encrypted then transferred safely.

## Learn about some useful headers

To be defined.

## When you type "google.com" into your browser, that will happen when you type enter till everything is displayed on your screen?

The first thing that happens is DNS lookup. The browser will get the corresponding IP address with the domain name from its cache, from the computer cache, from the local host file. If there is no data in these places, then the browser will send a request to DNS server to obtain the IP address. ([More](https://serverfault.com/questions/208172/how-does-my-browser-know-where-to-get-data-from-a-server))

The second thing is browser will detect if HTTPS is required or not in case we don't type the full url. Browser can get this information from its history data, from a preload list. Otherwise, it will send an HTTP request. (When server receives the request, server can do appropriate action to force user uses HTTPS if it wants.)

The third thing is TCP connection establishment. The TCP connection can be established by a process called 3-way handshake. If HTTPS is used, after TCP connection is established, the SSL connection establishment process will start.

Fourthly, after having the connection, client will send data to the server and server will send a response back to client through this connection.

The next thing is browser will render the data from response based on the content type. If there is no content type, the broswer will read the body of response to know which kind of data it is. If the content type is html, broswer will parse it then get resources like images, js files, css files if necessary.

## DNS lookup (in case you already access google.com before and also in case you do not know the IP of google.com)

DNS information is cached in browser and operating system. If there is no corresponding data in both of these places, DNS request will be sent to the DNS resolver. The resolver will make necessary request to Top Level Domain server, to Domain Name server to get the IP address of the requested host, then return to user.

## Which protocol DNS use and why?

DNS primarily uses UDP as transport protocol because it's fast and DNS data usually is small and fits an UDP packet.

TCP is used when the data is greater than 512 bytes, which can not be fit on a single UDP packet.

## The other of place to look up DNS.

To be defined.

## TCP or UDP will be used in this case? why?

To be defined.

## How to know "google.com" require HTTP or HTTPS? how browser can know and redirect from HTTP to HTTPS?

There are at least two ways that make the web we are accessing change from using HTTP to HTTPS.

The first way is the server will redirect to HTTPS when the HTTP request comes.

The second way is using HSTS, HTTP strict transport security. When a browser sends the first HTTP request to server, server will add a header into the response indicates that the next request will require using HTTPS. Browser will automatically use HTTPS next time based on that information. In addition, browsers like Chrome have a HSTS preload list, they will automatically use HTTPS for all of the sites in that list.

## After you get the HTML content for "google.com" how to get the \*.js and image files?

Chain requests.

## When getting \*.js or image files do why use another TCP connection or use the same one as in the get HTML content? How DNS lookup work in this case?

To be defined.

## After your browser display "google.com" fully, is there any connection open?

It depends on if the connection is keep alive or not.

## Caching can apply to which steps? How caching applied?

To be defined.

## What is the connection pool? It's advantages and disadvantages? How to implement connection pool in your programing language?

Connection pool is a cache of database connections so connection can be reused when future request comes. Opening a connection is costly, so after a connection is opened, it is placed into connection pool so it can be reused later.

## What is socket?

Socket is an endpoint for a program to send and receive data across the network. Socket allows communication between processes on the same machine or different machines.

## Why do we need socket? Why socket is a "file" in linux?

Not only socket, everything is treated as files in Linux operating system. It provides a common and universal interface for things to communicate in this OS.

## What is src port when you create a connection to a "server"?

- When a client establishes a TCP connection to a server, it needs both a source port (SRC Port) and a destination port (DST Port) to properly communicate.

- Destination Port (DST Port): The well-known port of the server (e.g., HTTP → 80, HTTPS → 443, SSH → 22).
- Source Port (SRC Port): A randomly assigned port by the client’s operating system.

### Why Use a Random Source Port?
- Prevents conflicts between multiple connections.
- Improves security (harder for attackers to predict).
- Supports multiple connections from the same client to the same server.

## How one server can handle multiple connections to the same port?

When a packet travels from a client to a server, its source (SRC) and destination (DST) IP and port may be modified due to Network Address Translation (NAT), routing, and proxying. Here’s how these values change along the way:

## What is the maximum number of connections a server can handle? (if it has unlimited resource) (in case of the same client and in case of multiple clients)

TCP has 4 fields to identify the uniqueness of a socket: source IP, source port, destination IP, destination port.
Theoretically, if server listens on a port, a client can open a maximum of 2^16 connection. that's is the maximum number of ports a client can open. For multiple clients, the number can be multiplied by 2^32, it is the possible number of source IP addresses. So the maximum number of connections is 2^48.

But in practice, the maximum number of connections depends on the kernel configuration. Because each socket needs a file descriptor, the number of sockets is limited by the maximum number of file descriptors that the machine allows. In addition, a file descriptor needs a certain amount of memory so it can also be limited by the number of available memory.

## When you open multiple tabs on your chrome, how OS knows which packet (both sending and receiving) correspond to which tab? (how about in se you open many tabs to the same page "for eg: google.com")

Because each connection has its own socket that is used to exchange data.

## What are the maximum numbers of connection your machine can connect to "google.com" (if you have unlimited resource)

It is the maximum number of ports my machine can open, which is 2^16.

## Can two processes listen to the same port on your machine? Why? How?

Yes, and it is useful for multicast purpose. Socket needs to be specified as a reused socket before binding to do so.

## What is buffer? why we always need buffer when working with "file"?

Buffer is a segment of memory that is reserved to store necessary data being processed. Buffer helps reduce the processing time.

## What is unix socket? When to use it?

Unix socket or IPC socket is a data communication endpoint that allows processes in a same machine to communicate with others.

## What is TCP proxy? reverse proxy? and VPN?

A TCP proxy server is a server that acts as an intermediary between and client and server. Its goal is to hide clients on the internet. The server only knows that request came from the proxy server and doesn't know about the actual clients.

A reverse proxy, on the other hand, protect server from client. It hides server from client. from the client point of view, client sends data to the reverse proxy and receives data from it too.

VPN allows devices on a public network to be connected as they are in a private network.

## How your router at your home works?

Router connects devices within a network by forwarding packets between them. Packets might be sent between devices, or from a device to the internet and vice versa. It maintains an ARP (address resolution protocol) table to know where to forward a packet to.

## Inside LAN network, it uses IP or MAC address? Why?

Inside a LAN network, it uses MAC address cause we don't need a layer 3 protocol to communicate between devices that are in the same local network.

## How does it know which packet comes from (or arrive at) which machine?

Router will use the ARP table to know the MAC address of the device which this packet is sent to.

## What is the difference between Hub and Switch inside LAN?

Hub is operated on Physical layer and supports only broadcast transmission.

Switch is operated on Data link layer and supports unicast, multicast, and broadcast transmission.s

## How src IP/PORT and dst IP/PORT change on the way to the server?

To be defined.

## How load balancer works?

Load-balancer distributes load to servers. It can do it randomly, uses an algorithm like round-robin, or distribute requests based on the current load of each server.

## When we send a packet to a load balancer how does it forward to the desired server? (Does it keep any data on its memory?)

A load balancer (LB) distributes incoming network traffic across multiple backend servers to optimize performance, ensure availability, and prevent overloading.

When you send a packet (or request) to a load balancer, it decides which server to forward that request to, based on its load-balancing algorithm and session handling mechanisms. Load balancers can operate in different network layers:

- Layer 4 (Transport Layer): TCP/UDP load balancers
- Layer 7 (Application Layer): HTTP/HTTPS load balancers

|Question	|Short Answer|
|--------------------|----------------------------|
|How LB forwards packets?|	Chooses server via algorithm (round-robin, least connections, session affinity).|
|Does it keep data in memory?	|Layer 4 LB: Minimal connection tracking. Layer 7 LB: Yes, keeps session state (cookies, affinity tables).|
|Typical Data Stored?	|Connection/session table, affinity tables, cookie mappings.|
|Persistence after failure?	|Usually not (stateless), unless configured for state persistence (Redis, cluster replication).|

## When the server wants to send data back to the client, does the connection need to go through the load balancer?

It depends on whether the server and the load balancer are in the same private network and share the same IP address.

## What is different between reverse proxy and load balancer?

Reserve proxy acts as an intermediary without any load distributing operation.

## Can load balancer be a bottleneck? (Because it is the end point of too many requests) (bottleneck about RAM or CPU or Network?)

Yes. And it also can be a single point of failure.


## **Layer 4 vs Layer 7 Load Balancing**

| Feature  | Layer 4 Load Balancing | Layer 7 Load Balancing |
|----------|------------------------|------------------------|
| **Layer** | Transport Layer (4) | Application Layer (7) |
| **Traffic Routing** | IP & Port-based | HTTP headers, URL, Content |
| **Speed** | Fast, low overhead | Slower, content processing needed |
| **Flexibility** | Limited | High (content-aware routing) |
| **Common Use** | Simple load balancing, video streaming | Web apps, API Gateway, microservices |
| **Examples** | TCP Load Balancer, AWS NLB | HTTP Load Balancer, AWS ALB |

## QUIC

- QUIC (Quick UDP Internet Connections) is a modern transport protocol developed by Google and later standardized by the IETF (RFC 9000). It was designed to improve upon TCP by reducing connection establishment times and eliminating head-of-line blocking at the transport layer. QUIC runs atop UDP rather than TCP, allowing for more flexible packet handling and better resilience against packet loss. With QUIC, new connections can be established with less latency, often requiring fewer round trips than traditional TCP+TLS handshakes.

- A key advantage of QUIC is its built-in encryption equivalent to TLS 1.3. This ensures that all QUIC traffic is secure by default, removing the need for a separate TLS handshake phase that TCP-based protocols typically rely on. Additionally, QUIC divides data streams in a way that packet loss on one stream does not affect others—a significant departure from TCP, which can hold up all streams if a single packet is dropped. As a result, QUIC-based HTTP/3 offers a more efficient web experience with potentially lower latency, especially in scenarios with variable network conditions.

- Despite its benefits, QUIC adoption requires updates on multiple fronts: server-side implementation (e.g., nginx, Envoy, or a specialized QUIC library), client/browser support, and network compatibility. Many firewalls and middleboxes were originally not designed for extensive UDP-based traffic, although this is improving as QUIC gains mainstream acceptance. Today, major web browsers, CDNs, and large-scale websites are increasingly adopting QUIC and HTTP/3, positioning them as the future of web transport protocols for high-performance and secure connections.