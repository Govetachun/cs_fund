Khi được hỏi **"Design 1 API xyz"** trong phỏng vấn (backend hoặc system design), bạn cần trình bày bài bản, có cấu trúc và chủ động hỏi lại yêu cầu để làm rõ phạm vi.

---

## 🎯 Mục tiêu trả lời:

1. **Hiểu yêu cầu (requirement clarifications)**
2. **Thiết kế endpoint (API Design)**
3. **Thiết kế data model**
4. **Bảo mật, phân quyền**
5. **Performance, scaling**
6. **Bonus: Logging, rate limiting, monitoring, testing**

---

## ✅ Ví dụ: Design API để "quản lý sản phẩm trong hệ thống e-commerce"

---

### 🧩 Bước 1: Clarify requirement

**Giả định yêu cầu là:**

> "Thiết kế API cho quản lý sản phẩm (product) cho một hệ thống e-commerce đơn giản."

**Hỏi lại interviewer (clarifying):**

* API này phục vụ admin, seller hay user?
* Có cần phân trang (pagination), lọc (filter), sắp xếp (sort) không?
* Có cần bảo mật hoặc xác thực JWT không?
* Có liên kết với danh mục (category), tag không?
* Có cần soft delete hay hard delete?

---

### 📘 Bước 2: Define các API endpoint

```http
GET     /api/products               -> List products (with filters, pagination)
GET     /api/products/{id}         -> Get product details
POST    /api/products              -> Create new product
PUT     /api/products/{id}         -> Update product
DELETE  /api/products/{id}         -> Delete product (soft delete)
```

---

### 🧱 Bước 3: Data model (JSON schema + DB schema)

#### 📦 Request body (POST / PUT):

```json
{
  "name": "iPhone 15 Pro",
  "description": "Latest Apple iPhone",
  "price": 1299.99,
  "category_id": "abc123",
  "tags": ["electronics", "mobile"],
  "inventory": 50
}
```

#### 🧮 SQL Schema:

```sql
CREATE TABLE products (
    id UUID PRIMARY KEY,
    name TEXT NOT NULL,
    description TEXT,
    price DECIMAL(10, 2),
    category_id UUID,
    inventory INT,
    is_deleted BOOLEAN DEFAULT FALSE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);
```

---

### 🔒 Bước 4: Authentication & Authorization

* Dùng **JWT** hoặc **OAuth2**
* Chỉ **admin/seller** có quyền POST, PUT, DELETE
* **User** chỉ có quyền GET

```http
Authorization: Bearer <jwt_token>
```

---

### ⚙️ Bước 5: Pagination, Filtering

```http
GET /api/products?page=1&limit=10&sort=price&order=asc&category=mobile
```

---

### 📈 Bước 6: Performance & Scaling

* **Caching:** Redis layer cho GET /products và GET /products/{id}
* **Rate limiting:** Dùng NGINX hoặc API gateway
* **Database indexing:** index `price`, `category_id`
* **Soft delete:** filter `is_deleted = false`

---

### 🧪 Bước 7: Monitoring & Logging

* Ghi log request/response
* Sử dụng Prometheus + Grafana cho metrics
* Log error theo trace-id (tracing middleware)

---

## 🧠 TL;DR – Checklist khi design 1 API trong interview:

| Step                     | Description                         |
| ------------------------ | ----------------------------------- |
| 1. Requirement           | Ai dùng API? Dữ liệu gì? Ràng buộc? |
| 2. Endpoint design       | RESTful? gRPC? Đặt route rõ ràng    |
| 3. Data Model            | JSON + DB Schema                    |
| 4. AuthN/AuthZ           | Ai được quyền gì? Token?            |
| 5. Pagination, Filtering | Hỗ trợ query param                  |
| 6. Performance           | Caching, indexing, rate limit       |
| 7. Monitoring            | Logging, metrics, alert             |

---

## 🎁 Bonus – Tools để mô phỏng:

* **Postman**: test API
* **Swagger (OpenAPI)**: document endpoint
* **FastAPI / Express.js**: mock server


# # System-Design Walk-through

**Scenario pack:**

| # | Product Domain            | Real-world analogue    |
| - | ------------------------- | ---------------------- |
| 1 | Ride-hailing / Dispatch   | “Grab / Uber”          |
| 2 | Short-video upload & feed | “TikTok / Reels”       |
| 3 | Realtime chat / messaging | “Messenger / WhatsApp” |

Below you’ll find a **full interview-style treatment** for *each* API:

* **1-minute clarification checklist** (the first thing to say in the room)
* **Core REST/gRPC endpoints** (with verbs + sample payloads)
* **Data-model & storage choices**
* **AuthN / AuthZ & multi-tenant concerns**
* **Performance / scale levers**
* **Observability, testing & rollout notes**

> **Interview tip** – Keep one whiteboard section reserved for *“Open Questions / Assumptions”*. Updating it as you go shows structured thinking.

---

## 1️⃣ Ride-Hailing Booking API (“Grab”)

### 1. Clarify scope in ≤30 s

| Axis        | Ask                                                    |
| ----------- | ------------------------------------------------------ |
| Actors      | Rider, Driver, Dispatch service                        |
| MVP         | “Request a single ride, see driver ETA, cancel”        |
| Geography   | Single city (lat/long within \~50 km)                  |
| Consistency | Can we tolerate duplicate bookings? (idempotency keys) |
| Surge       | Ignore dynamic pricing v1?                             |

### 2. Endpoints

```http
POST /v1/rides/request
GET  /v1/rides/{ride_id}
POST /v1/rides/{ride_id}/cancel

POST /v1/drivers/location/bulk   # driver GPS heartbeat (gRPC stream)
GET  /v1/riders/{rider_id}/history?limit=20
```

<details><summary>Sample request (ride request)</summary>

```json
{
  "rider_id": "u_789",
  "pickup":  { "lat": 10.772, "lng": 106.698 },
  "dropoff": { "lat": 10.738, "lng": 106.687 },
  "payment_method": "wallet",
  "client_ts": 1720348392,
  "idempotency_key": "2e22-9a8c-..."   // mitigate retries
}
```

</details>

### 3. Data model (polyglot)

| Store                          | Purpose                                          | Schema hint                                                                                                   |
| ------------------------------ | ------------------------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| **PostgreSQL**                 | long-term ride ledger                            | `rides(id PK, rider_id, driver_id, status, pickup_point geography(Point,4326), ...)`  (PostGIS for geo index) |
| **Redis / KeyDB**              | live driver locations (TTL ≤ 30 s)               | `driver:{id} -> {lat,lng, heading, speed}`                                                                    |
| **Kafka / Pulsar**             | Ride lifecycle events for settlement & analytics | topic = `ride_events`                                                                                         |
| **ElasticSearch / ClickHouse** | Geo-spatial heat maps, BI queries                |                                                                                                               |

### 4. Matching / Dispatch logic

```
┌──── rider POST /request ────┐
│  write ride=REQUESTED       │
│  push to Kafka[match_req]   │
└────────────┬───────────────┘
             │
      Dispatcher (stream job)
             │
   query Redis geo radius (1km) 
             │
         assign driver
             │
  PATCH ride=ASSIGNED (SQL + cache)
             │
   send push via FCM / APNS
```

### 5. Non-functional checklist

* **Auth:** OAuth2 access-token; driver tokens use a different audience (`aud=driver`).
* **Idempotency:** `idempotency_key` + unique DB constraint.
* **Rate-limit:** 3 active ride requests / rider.
* **Consistency trade-off:** location data is eventually consistent (≤ 3 s).
* **Scalability knob:** shard Redis by `driver_id % N` to keep geo sets < 10 M members.
* **Observability:** traceID flows across HTTP ↔ gRPC ↔ Kafka; Prometheus counter `match.latency_seconds_bucket`.

---

## 2️⃣ Short-Video Upload & Feed API (“TikTok”)

### 1. Clarify

| Q                    | Example answer                     |
| -------------------- | ---------------------------------- |
| Max upload size?     | 100 MB MP4                         |
| Processing pipeline? | Transcode → thumbnail → moderation |
| Feed algo?           | Leave as black-box “FeedService”   |

### 2. Endpoints

```http
POST    /v1/videos            # multipart/form-data
GET     /v1/videos/{id}       # playback URL + stats
GET     /v1/feed?cursor=...   # paginated personalized feed
POST    /v1/videos/{id}/like
POST    /v1/videos/{id}/comment
```

### 3. Storage layout

```
S3 bucket       : raw-uploads/{video_id}.mp4
S3 transcoded   : hls/{video_id}/...
DynamoDB “videos”: video_id PK, owner, status, duration, like_count (eventually consistent)
ClickHouse “events”: user_action, ts
Redis “feed_cache:{user_id}”: sorted-set (video_id, score)
```

### 4. Media pipeline (event-driven)

```
Client  ──>  /videos (S3 presigned PUT)  ──>
Lambda "ingest" ->  SQS "transcode"
                 ->  Rekognition moderation
                 ->  send SNS to owner on fail/ready
```

### 5. Performance & CDN

* CloudFront / Akamai for HLS segments.
* Signed URL with 1-hour TTL.
* **Hot feed caching**: latest 200 video IDs per user in Redis, fallback to FeedService (ML ranking) on cache miss.

### 6. Auth & abuse

* Upload requires **JWT** with `scope=upload_video`.
* **Content-moderation** queue blocks video status until green-light.
* Like/comment endpoints protected by **idempotency token** to avoid double likes.

---

## 3️⃣ Realtime Chat API (“Messenger”)

### 1. Clarify

| Topic            | Question               |
| ---------------- | ---------------------- |
| Protocol         | Long-poll? WebSocket?  |
| Message ordering | Weak vs strong?        |
| Fan-out          | 1-to-1 vs group (≤256) |

Assume *WebSocket* bidirectional, groups ≤ 100.

### 2. Core methods (WebSocket + REST fallback)

| Direction           | Proto                                              | Payload                                           |
| ------------------- | -------------------------------------------------- | ------------------------------------------------- |
| **Client → Server** | `SEND_MESSAGE`                                     | `{cid, channel_id, text, ts_client, idempotency}` |
| **Server → Client** | `MESSAGE_DELIVERED`, `MESSAGE_READ`, new `MESSAGE` |                                                   |

REST backup:

```http
POST /v1/channels/{id}/messages
GET  /v1/channels/{id}/messages?since=...
```

### 3. Storage & sequence numbers

* **ScyllaDB / Cassandra**
  `messages(partition: channel_id, clustering: msg_seq DESC)`
* **Redis Stream** per channel for transient fan-out (`XADD`, `XREADGROUP`).
* **Kafka** “chat\_events” for audit & analytics.

#### Sequence generator

```
Snowflake-style(42-bit) or K-ordering service => unique, roughly time-ordered msg_seq
```

### 4. Fan-out service

```
1. User sends message (WS)  -> Chat-Ingress
2. Persist to DB, assign seq
3. Push to Redis stream
4. FanOut worker pulls, multiplexes to all WebSocket sessions of recipients
5. ACK path: client sends READ receipts -> update state table
```

### 5. Reliability & offline

* Mobile device reconnects with `last_ack_seq`; server sends delta.
* Messages stored safely in Scylla with QUORUM write, but fan-out at MOST-ONCE (acceptable duplicate show).

### 6. Security

* End-to-end encryption: store *ciphertext* payload, key handled by client crypto lib (out-of-scope if interviewer not asking).
* Authentication: short-lived TLS session token (Auth0 or in-house).
* Rate-limit: 30 messages / 5 s to preserve quality.

---

## 🔑 Cross-scenario “interview talk-track”

1. **Open with questions:** *“Before I jump into endpoints let me confirm actors, cardinality, SLAs.”*
2. **Draw high-level flow diagram** – request path, async jobs, storage layers.
3. **Start with the **write** path** (hardest), then show read optimisation (cache/CDN).
4. **Explicit trade-offs:** eventual vs strong consistency, hot keys, TTL strategy, shard keys.
5. **Mention observability** (metrics, traces) **and rollout** (canary, feature flag).
6. **Close with “Future work”** – i18n, data-retention, GDPR delete, chaos testing.

---

### ⌘  Quick ASCII sample diagram (Ride-hailing)

```
┌──Mobile──┐
│  Rider   │
└─┬─────▲──┘
  │REST │
  ▼     │WS
┌───────────────┐   Kafka    ┌──────────────┐
│ API-Gateway   ├───────────►│ Dispatch svc │
└────┬──────────┘            └─────┬────────┘
     │ SQL write                    │
     ▼                              │Redis Geo
┌─────────────┐                     │
│  Postgres   │◄────────────────────┘
└─────────────┘
```

---

## 📚 Further reading / inspirations

* **Ride-Hailing:**

  * “Uber’s Microservice Architecture” — Uber Eng Blog, 2022
  * Huang et al., *“Machine Learning for Dynamic Pricing in Ridesharing”*, KDD 2020

* **Video platforms:**

  * “TikTok’s Video Processing Pipeline” — ByteDance Tech Blog, 2023
  * Xu et al., *“Serving 1 Billion Video Views a Day with S3 + HLS”*, AWS re\:Invent 2022 talk

* **Chat systems:**

  * Rybalkin & Grosskurth, *“Building WhatsApp Behind the Scenes”* (InfoQ 2020)
  * Meta, *“MQTT at Scale for Messenger”*, Engineering Blog 2021

---

### 🛠️  Closing

Use the **same skeleton** for any “Design API XYZ” question:

```
Clarify → Endpoint → Data → Scaling → Security → Ops
```

Practise condensing each bullet to a *15–20 s sound-bite*; the rest of the detail is there when the interviewer wants to dive deeper.

