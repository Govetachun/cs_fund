# Topic: SECURITY

## Hash vs Encrypt vs Encode

Hashing is the act of transforming data from arbitrary size to a fixed size value. For example the module operator.

Encode is a way to transform data to another form to preserve usability. For example, 2 bit 0 and 1 can represent almost every kind of data we know.

Encryption is the act of transforming data to another form to preserve confidentiality. For example, AES encryption uses a secret key to transform data into an encrypted string. Only the person who has the same key can decrypt the encrypted string and read the message.

## Are there any way we can crack Hash

We can always try to guess the original string by calculating the hash of every possible input then comparing the result with the hash string. However, due to hashing collision, there might be more than one input that has the same output.

## Symmetric vs asymmetric encryption? AES vs RSA?

Symmetric encryption uses the same key for both encrypting and decrypting data.

Asymmetric encryption uses a public key to encrypt and a private key to decrypt data.

## Fast Hash vs Slow Hash?

Fast Fash is the type of hash function with fast computation time and Slow Hash is the type hash function with slow computation time. Fast Hash is used for checking the integrity of data, while slow hash is used for hashing password.

## When we use Encode??

We use Encode so data can be represented in a better use form. For example, we transform text, audio, image into 1 and 0 so computers can understand, store, process, etc.

## What is the perfect hash function?

A perfect hash function of a set is a hash function that maps distinct elements of this set to elements of another set without any collisions.

## What is the load factor of hashing?

When a map initiates, two things need to be considered are the initial capacity and the load factor. The initial capacity is the capacity of the hash table underlying the map. When the load is bigger than the load factor, it means the underlying table is quite full, a process called rehashing happens, a twice bigger size hash table is allocated to store the data of the map.

## SSL/TLS

SSL - Secure Socket Layer, is the original encryption protocol developed for HTTP. It now has been replaced by the TLS, Transport Layer Security, but the name SSL is still widely in use. Whenever a client makes a connection to a server over HTTPS, the client and server will initiate a TLS handshake, which happens after the TCP handshake has been finished.

The steps of a TLS handshake will vary depending on the key exchange algorithm in use. The RSA key exchange algorithm is used most often. The steps are as followed:

1.  The client kicks off the handshake by sending a "hello" message to the server. The message will include which TLS version, cipher suites the client supports, and a string of random bytes known as the "client random."
2.  The server sends back a message containing the server's SSL certificate, the server's chosen cipher suite, and the "server random" - another random string of bytes.
3.  Authentication: The client verifies the server's SSL certificate with the certificate authority that issued it.
4.  The client sends the "premaster secret", another random string of bytes. The premaster secret is encrypted with the public key from the SSL certificate and can only be decrypted with the private key by the server.
5.  The server decrypts the premaster secret.
6.  Session keys created: Both client and server generate session keys from the client random, the server random, and the premaster secret. They should arrive at the same results.
7.  Client is ready: The client sends a "finished" message that is encrypted with a session key.
8.  Server is ready: The server sends a "finished" message encrypted with a session key.
9.  Secure symmetric encryption achieved: The handshake is completed, and communication continues using the session keys.

TLS handshake can also use Diffie-Hellman (DH) as the key exchange algorithm. The steps then will be:

1.  Client's hello is the same as above.
2.  Server's hello also sends the server's random, chosen cipher suite, SSL certificate. The server also includes a digital signature, created by encrypting the client random, the server random, and its DH parameter using the private key.
3.  The client decrypts the server's digital signature using the SSL certificate's public key, verifying that the server controls the private key and is who it says it is. The client then sends its DH parameter to the server.
4.  The client and server use the exchanged DH parameters to calculate a matching premaster secret independently.
5.  Session keys created: Now both the client and server calculate the session key using the client random, the server random, and the premaster secret like in an RSA handshake.

The rest is the same as the RSA handshake.

## How to verify a certificate? How many kinds of certificates are there?

To be defined.

## What is CA? how to verify certificate of a CA?

CA stands for certificate authority. It is an entity that verifies the certificate of servers.

When a server and a client want to establish an SSL connection, the server will send its public key to the client. So how the client knows this public key is the right one, and no man in the middle tries to interfere in this conversation. To do that, the server also sent a certificate with the public key. The client will ask the CA to verify if the public key is the correct key of the one in the certificate.

## What is digital signature? What is HMAC?

Digital signature is a mathematical scheme to verify the authenticity of data.

HMAC stands for hash based message authentication code, is used to verify both data integrity and data authenticity.

## How to store credential information efficiency? (user password, config key, database credential, user information, secret key,.... )

First, we need to know if we need to access the original value or not.

For data like passwords, we don't need to access the raw password of users, so we can use some hashing function to hash the password and store it in the database.

For data like user information, the raw data will need to be retrieved in some cases, so we need to use encryption, instead of hashing. To reduce the risk of data compromise, we need to store data and the encryption key in 2 different places.

## Describe a way to defense DDOS? (actually, there are many kinds of DDOS not just network or memory, so this question is pretty complicated)

Block IP address, apply rate limit for IP, for users.


Here’s a **clear, interview-friendly explanation of JWT (JSON Web Token)** — including core concepts, how it works, and related keywords — all in a format that’s easy to explain or whiteboard.

---

## 🔐 What is JWT?

**JWT (JSON Web Token)** is a compact, URL-safe way to represent claims between **two parties** — typically used for **authentication and authorization** in modern APIs.

> Think of JWT as a **signed passport**: the client holds it, and the server trusts it because it’s tamper-proof.

---

## 🧱 JWT Structure
> “JWT is a stateless way to transmit identity claims between parties. It has three parts — header, payload, and signature. The signature ensures integrity, and the payload holds user info like roles and expiry. JWTs are commonly used in REST APIs to verify authenticated users without storing session state on the server. However, they must be used carefully — with expiration, HTTPS, and sometimes refresh tokens for security.”
A JWT consists of **three parts**, separated by dots:

```text
HEADER.PAYLOAD.SIGNATURE
```

### 1. **Header** (metadata)

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

* Algorithm used for signing (e.g., HS256 or RS256)
* Type (always `"JWT"`)

---

### 2. **Payload** (claims/data)

```json
{
  "sub": "1234567890",
  "name": "Alice",
  "role": "admin",
  "exp": 1712345678
}
```

Contains **claims**, like:

* `sub`: subject (user ID)
* `iat`: issued at
* `exp`: expiration timestamp
* `role`, `email`, etc.

> ⚠️ This is **base64-encoded**, not encrypted → **don't store sensitive data** here.

---

### 3. **Signature** (protection)

```text
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

Ensures token is not tampered with — signed using:

* A **secret key** (for HMAC) or
* A **private key** (for RSA/ECDSA)

---

## 🔁 How JWT Works (Flow)

```text
1. Login → user authenticates with username/password
2. Server generates a JWT (with user info + expiry)
3. Client stores JWT (e.g., in localStorage or cookie)
4. On future requests → client sends JWT in header:
   Authorization: Bearer <token>
5. Server verifies signature → grants/denies access
```

> ✅ Stateless — no need to store session on the server.

---

## ✅ Advantages

* **Stateless authentication**
* Easy to share across services (microservices)
* Works with **mobile, web, and APIs**
* Can include metadata (role, expiry, etc.)

---

## ⚠️ Limitations / Pitfalls

* **Token can't be revoked** unless you keep a blacklist
* Large payload = large token
* If secret key is leaked → **security breach**
* Should always use **HTTPS** (to prevent token theft)

---

## 🔑 Related Keywords

| Term                | Meaning                                                |
| ------------------- | ------------------------------------------------------ |
| `Bearer Token`      | JWT sent in the HTTP header for authentication         |
| `HS256`             | HMAC using SHA-256 (shared secret signing)             |
| `RS256`             | RSA signature (public/private key signing)             |
| `Access Token`      | Short-lived token for accessing resources              |
| `Refresh Token`     | Long-lived token to get new access token               |
| `exp`, `iat`, `nbf` | Standard JWT claims (expiry, issued-at, not-before)    |
| `Base64Url`         | Encoding format used in JWT (like base64 but URL-safe) |

---




## 🎥 What is WebRTC?

> **WebRTC (Web Real-Time Communication)** is a **browser-based API and protocol stack** that enables **real-time audio, video, and data streaming** between peers **without installing plugins or using intermediate servers**.

---

### ✅ WebRTC Use Cases

* Video conferencing (Zoom, Google Meet)
* Live video/audio chats
* P2P file sharing
* Multiplayer games (real-time sync)

---

### 🧱 Key Components of WebRTC

| Component                                        | Role                                             |
| ------------------------------------------------ | ------------------------------------------------ |
| **MediaStream API**                              | Captures audio/video from camera/mic             |
| **RTCPeerConnection**                            | Manages the connection between peers             |
| **RTCDataChannel**                               | Sends arbitrary data (chat, files) between peers |
| **ICE** (Interactive Connectivity Establishment) | Finds best path (NAT traversal)                  |
| **STUN/TURN** servers                            | Helps peers connect through firewalls and NAT    |
| **SDP** (Session Description Protocol)           | Describes the media capabilities                 |

---

### 🔐 WebRTC Security

* End-to-end encrypted by default
* Uses **DTLS-SRTP** for secure media transport

---

## 📦 What is SRT?

> **SRT (Secure Reliable Transport)** is a **low-latency video transport protocol** optimized for **contributing high-quality video streams over the public internet**, especially in broadcast environments.

---

### ✅ SRT Use Cases

* Live video contribution (camera to cloud)
* Remote production (sports, events)
* Broadcast workflows (OBS → studio servers)
* IP camera streams over WAN

---

### 🧱 Key Features of SRT

| Feature                            | Explanation                                                   |
| ---------------------------------- | ------------------------------------------------------------- |
| **UDP-based**                      | SRT is built on top of UDP (unlike TCP), but adds reliability |
| **ARQ** (Automatic Repeat reQuest) | Retransmits lost packets                                      |
| **FEC** (Forward Error Correction) | Helps recover from packet loss                                |
| **Encryption**                     | Uses AES-128/256                                              |
| **Latency control**                | Configurable latency buffer for jitter compensation           |
| **Firewall traversal**             | Built-in NAT traversal (like ICE in WebRTC)                   |

---

### 🎯 SRT vs WebRTC — Comparison Table

| Feature             | WebRTC                                        | SRT                                        |
| ------------------- | --------------------------------------------- | ------------------------------------------ |
| Target use case     | Real-time **interactive** apps                | High-quality **broadcast-grade** streaming |
| Transport base      | RTP over UDP with ICE                         | UDP with reliability layer                 |
| Built-in signaling? | ❌ Needs custom signaling (e.g. via WebSocket) | ❌ You define the signaling manually        |
| NAT traversal       | ✅ via STUN/TURN                               | ✅ via handshake, NAT traversal             |
| Latency             | Ultra low (<200ms)                            | Low, but tunable (500ms–2s+)               |
| Video quality       | Prioritizes low latency                       | Prioritizes quality, deals with loss       |
| Codec negotiation   | Via SDP                                       | You control codec via app or tool          |
| Use with browsers   | ✅ (built-in)                                  | ❌ Needs app (e.g., OBS, VLC, NGINX)        |

---

## 🔑 Keywords to Remember

| Term               | Related To | Meaning                                   |
| ------------------ | ---------- | ----------------------------------------- |
| **SDP**            | WebRTC     | Session description: codec, bitrate, etc. |
| **ICE**            | WebRTC     | Path-finding strategy through networks    |
| **STUN**           | WebRTC     | Discovers public IP/port                  |
| **TURN**           | WebRTC     | Acts as relay when P2P fails              |
| **DTLS-SRTP**      | WebRTC     | Secure transport for media                |
| **ARQ**            | SRT        | Packet recovery via retransmission        |
| **Latency buffer** | SRT        | Adds delay to handle jitter/loss          |
| **AES Encryption** | SRT        | Optional data encryption (AES-128/256)    |

---

## 🧠 Interview-Friendly Summary

> “WebRTC is a browser-native protocol for real-time peer-to-peer communication — ideal for interactive apps like video chat or collaborative tools. It includes media capture, encryption, NAT traversal (via STUN/TURN), and supports both audio/video and data channels. On the other hand, SRT is a high-performance UDP-based protocol used for sending high-quality video streams over unreliable networks — great for broadcast-grade streaming or contribution feeds. SRT handles packet loss and jitter better via ARQ and FEC, but it’s not designed for browsers or real-time interaction.”

## 🎞️ 1. **Codec**

> A **codec** is a program or algorithm that **compresses and decompresses digital media** (audio/video).

### 🔧 How It Works

* **Encoder**: compresses raw video/audio to reduce file size
* **Decoder**: decompresses for playback

### 🔥 Common Video Codecs

| Codec        | Description                          | Use Case                           |
| ------------ | ------------------------------------ | ---------------------------------- |
| H.264 (AVC)  | Most widely used, good compression   | YouTube, Zoom, MP4                 |
| H.265 (HEVC) | Better quality at lower bitrate      | 4K, Netflix                        |
| VP8/VP9      | Open-source alternatives from Google | WebRTC, YouTube                    |
| AV1          | Royalty-free, high-efficiency        | Future streaming, YouTube, Netflix |

> ⚠️ **Codec ≠ container**: MP4, MKV are containers that hold encoded video/audio streams.

---

## 📶 2. **Bitrate**

> **Bitrate** is the **amount of data** processed per second in a video/audio stream.

### 📏 Unit

* Measured in **kbps** (kilobits per second) or **Mbps**

### ⚖️ Trade-off

| Bitrate      | Effect                      |
| ------------ | --------------------------- |
| High bitrate | Better quality, larger size |
| Low bitrate  | Lower quality, smaller size |

> 🎯 Goal: Balance **bitrate** with **resolution and codec** to optimize streaming.

---

## 🛠️ 3. **FFmpeg**

> **FFmpeg** is a **command-line tool** for **processing video/audio**: convert, compress, stream, analyze, and more.

### ✅ What You Can Do with FFmpeg

* Convert formats (e.g., `.mp4` → `.webm`)
* Transcode (change codec/bitrate)
* Extract audio/video frames
* Stream to RTMP/SRT servers
* Segment video for HLS/DASH

### 🧪 Example Commands

```bash
# Convert video to H.264
ffmpeg -i input.mov -c:v libx264 -crf 23 -preset medium output.mp4

# Change bitrate
ffmpeg -i input.mp4 -b:v 1000k -b:a 128k output.mp4

# Create HLS segments
ffmpeg -i input.mp4 -codec: copy -start_number 0 -hls_time 10 -hls_list_size 0 -f hls playlist.m3u8
```

> FFmpeg is like a **Swiss Army knife** for media engineers.

---

## 📡 4. **HLS Server (HTTP Live Streaming)**

> **HLS (HTTP Live Streaming)** is a **streaming protocol** developed by Apple that delivers media **as small chunks (.ts) + playlist (.m3u8)** over HTTP.

### ⛓️ How HLS Works

1. Video is split into `.ts` chunks (\~10 seconds)
2. A `.m3u8` playlist file lists these chunks
3. Browser/player downloads chunks over HTTP and plays them

### 🧱 HLS Output Structure

```
output/
├── playlist.m3u8     ← index file (playlist)
├── segment0.ts       ← 10s chunk
├── segment1.ts
├── segment2.ts
```

### 🌍 Use Cases

* Adaptive bitrate streaming (ABR)
* VOD + Live streaming
* Works on all modern browsers/devices



## 🧠 Summary for Interview

> "A **codec** compresses video to reduce size (like H.264 or AV1). **Bitrate** controls quality vs bandwidth — higher bitrate means better quality but more data. **FFmpeg** is the go-to tool to transcode or segment video, e.g., for adaptive bitrate streaming. An **HLS server** serves `.ts` segments and `.m3u8` playlists over HTTP to enable smooth video playback — commonly used in live and on-demand streaming."

---

## 🔑 Related Keywords

| Term         | Meaning                                         |
| ------------ | ----------------------------------------------- |
| `libx264`    | FFmpeg encoder for H.264                        |
| `CRF`        | Constant Rate Factor (controls quality in x264) |
| `TS segment` | Transport stream chunk in HLS                   |
| `ABR`        | Adaptive Bitrate Streaming (multi-quality)      |
| `m3u8`       | Playlist file used in HLS                       |
| `muxing`     | Combining video + audio into one file           |
| `RTMP`       | Real-time Messaging Protocol (for ingest)       |

Here’s a **clear and interview-ready explanation** of **Adaptive Bitrate Streaming (ABR)** — a critical concept in video streaming systems.

---

## 🎯 What is Adaptive Bitrate Streaming (ABR)?

> **Adaptive Bitrate Streaming (ABR)** is a technique where the **video player automatically switches between different video quality levels** (bitrates) based on the **user's current network speed and device capability**, ensuring smooth playback.

It improves **user experience** by minimizing:

* Buffering
* Lag
* Video playback interruptions

---

## 🧱 How ABR Works – Step by Step

1. 🎞️ **Video is encoded into multiple versions**:

   * Same content
   * Different **resolutions** and **bitrates**
   * Example:

     ```
     360p - 500 kbps
     720p - 1500 kbps
     1080p - 3000 kbps
     ```

2. 🔪 **Segmented into small chunks** (e.g., 2–10 seconds per chunk)

   * Formats: `.ts`, `.mp4`, `.webm`, etc.

3. 📃 **Playlist file (e.g., HLS `.m3u8`, MPEG-DASH `.mpd`)** is created

   * Lists available quality levels and their chunk URLs

4. 📱 **Player starts at a lower quality**

   * Measures buffer health + bandwidth

5. 📈 **Player switches to higher or lower quality dynamically**

   * Based on:

     * Current download speed
     * Buffer status
     * Device CPU load

---

## 💡 Example: HLS Playlist (master `.m3u8`)

```m3u8
#EXTM3U
#EXT-X-STREAM-INF:BANDWIDTH=500000,RESOLUTION=640x360
low/index.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=1500000,RESOLUTION=1280x720
medium/index.m3u8
#EXT-X-STREAM-INF:BANDWIDTH=3000000,RESOLUTION=1920x1080
high/index.m3u8
```

> The player chooses `low`, `medium`, or `high` depending on real-time conditions.

---

## ⚖️ Trade-offs

| Pros                                  | Cons                                |
| ------------------------------------- | ----------------------------------- |
| Seamless playback under poor networks | Increased storage and encoding time |
| Efficient bandwidth usage             | Slight startup delay for measuring  |
| Cross-device compatibility            | Complex encoding/packaging pipeline |

---

## 🔑 Related Terms

| Term                     | Meaning                                          |
| ------------------------ | ------------------------------------------------ |
| **Representation**       | One version of a video (resolution + bitrate)    |
| **Chunk/Segment**        | Small piece of video (e.g., 5 seconds)           |
| **Manifest/Playlist**    | File listing all representations (e.g., `.m3u8`) |
| **Bandwidth estimation** | How the player decides which quality to pick     |
| **Buffer health**        | Amount of preloaded video in memory              |

---

## 🧠 Interview Soundbite

> “Adaptive Bitrate Streaming (ABR) improves user experience by adjusting video quality on the fly based on real-time bandwidth and buffer conditions. It works by encoding the video into multiple bitrate versions, segmenting them, and letting the player switch levels using a manifest file. Protocols like HLS and MPEG-DASH implement this. I’ve used FFmpeg to generate adaptive renditions and tools like hls.js or Shaka Player for testing ABR playback.”
