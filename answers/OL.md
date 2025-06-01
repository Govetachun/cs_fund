# 🧠 Data Structures & System Design Notes

---

## 🔗 Hash Table with Chaining

### ✅ Implement Hash with Chaining (Python)

```python
class HashTable:
    def __init__(self, size):
        self.size = size
        self.buckets = [[] for _ in range(size)]

    def _hash(self, key):
        return hash(key) % self.size

    def insert(self, key, value):
        index = self._hash(key)
        for pair in self.buckets[index]:
            if pair[0] == key:
                pair[1] = value
                return
        self.buckets[index].append([key, value])

    def get(self, key):
        index = self._hash(key)
        for pair in self.buckets[index]:
            if pair[0] == key:
                return pair[1]
        return None
```

### 📏 How to Choose Bucket Size
- Chọn **số nguyên tố gần nhất** với số lượng item dự đoán.
- Duy trì **load factor** trong khoảng `0.5 - 0.75`:
  ```
  load_factor = number_of_elements / number_of_buckets
  ```

### 🔄 Rehashing Strategy

```python
def rehash(self):
    old_buckets = self.buckets
    self.size *= 2
    self.buckets = [[] for _ in range(self.size)]
    
    for bucket in old_buckets:
        for key, value in bucket:
            self.insert(key, value)
```

---

## 🔍 API Resilience & Monitoring

### ✅ Validate API Response Structure
- Sử dụng JSON Schema / Pydantic để validate structure.
- Kiểm tra các field bắt buộc và type.

### 🔄 Hỗ trợ 3rd-party implement versioned API
- Khuyến khích dùng version (`/api/v1/...`)
- Backward compatibility (field optional, default)

### ❗ Yêu cầu làm rõ từ API provider
- Tài liệu rõ ràng: field nào required, optional
- Khi nào field có thể thay đổi

### 🛡️ Monitoring & Alerting
- Viết hệ thống monitor decode JSON:
    - Log lỗi nếu JSON decode thất bại
    - Alert nếu tần suất lỗi tăng
- Có thể tích hợp với Prometheus + Grafana / Sentry / DataDog

### 🧯 Tắt feature khi API lỗi
- Dùng feature flag để fallback hoặc disable logic
- Circuit breaker để tránh call liên tục gây overload

---

## 📦 Handle Message trong Message Queue

### Các bước chính:

1. **Lấy message từ queue**: Consumer subscribe topic/queue.
2. **Parse & Deserialize message**: JSON, Avro, Protobuf, v.v.
3. **Xử lý nghiệp vụ**: Gọi API, update DB, thực thi logic.
4. **Lưu log / lưu DB**: Ghi lại trạng thái xử lý.
5. **Gửi ACK / NACK về queue**: Acknowledge nếu OK, reject nếu fail.

### ⚖️ Delivery Semantics

| Loại         | Đặc điểm                            | Ứng dụng                              |
|--------------|-------------------------------------|----------------------------------------|
| At-least-once | Có thể **duplicate** | Cần tracking ID (VD: user_id, message_id) |
| At-most-once  | Không retry, có thể mất message     | Dữ liệu không quan trọng               |
| Exactly-once  | Chạy **chính xác 1 lần** (khó)      | Giao dịch, thanh toán                  |

---

### 🔁 Retry / DLQ / Rate Limit

- **Retry** nếu fail (exponential backoff).
- **Dead Letter Queue (DLQ)** để lưu message lỗi nhiều lần.
- **Rate Limiting / Circuit Breaker** để tránh overload downstream.

---

### 🧼 Deduplication Strategy

- Dựa trên message ID / UUID / user ID
- Lưu trạng thái message xử lý để bỏ qua duplicate

---

### 🐢 Low message throughput?

- **Raise consumers**: scale horizontal
- **Batching**: gom message xử lý 1 lần
- **Async processing**: dùng `asyncio`, `aiohttp`, `aiokafka`

---

## 📚 Index trong Database

### 🧾 Mục đích Index
- Giống như mục lục sách: tìm nhanh vị trí dòng phù hợp.
- Hữu ích với `SELECT`, `WHERE`, `ORDER BY`, `GROUP BY`.
- JOIN hai bảng qua cột có index sẽ nhanh hơn nhiều.

### ✅ Ưu điểm

- 🔍 Truy vấn nhanh hơn (giảm full table scan).
- 📈 Hiệu suất JOIN / ORDER / GROUP tốt hơn.
- 📉 Giảm I/O cost cho truy vấn lớn.

### ⚠️ Nhược điểm

- 🔁 Ghi chậm hơn (INSERT/UPDATE/DELETE cần cập nhật index).
- 📦 Tốn thêm dung lượng lưu trữ.
- ❌ Với bảng nhỏ, full scan đôi khi nhanh hơn dùng index.

---

## 🌳 Lưu Tree/Folder vào DB

### 📌 Phương pháp phổ biến

| Phương pháp       | Mô tả                                |
|-------------------|--------------------------------------|
| **Adjacency List** | Lưu `parent_id` cho mỗi node         |
| **Path Enumeration** | Lưu toàn bộ đường dẫn dạng text     |
| **Nested Set**     | Gắn chỉ số `left/right`              |

### 🧱 Adjacency List (Phổ biến nhất)

```sql
CREATE TABLE folder (
    id SERIAL PRIMARY KEY,
    name TEXT,
    parent_id INTEGER REFERENCES folder(id)
);
```

Truy vấn recursive:

```sql
WITH RECURSIVE folder_tree AS (
    SELECT * FROM folder WHERE parent_id IS NULL
    UNION ALL
    SELECT f.* FROM folder f
    JOIN folder_tree ft ON f.parent_id = ft.id
)
SELECT * FROM folder_tree;
```

---

Bạn có thể dùng cấu trúc này để ghi chú kỹ thuật hoặc làm tài liệu phỏng vấn/kỹ thuật nội bộ. Nếu bạn cần tách thành các file `.md` riêng hoặc export sang PDF, mình có thể giúp thêm.


# 🔄 Switch Context khi Job Hoàn Thành (Job Dependency)

## 🚩 1. Khái niệm chung

Bạn có các jobs `A`, `B`, `C`,... cần chạy theo thứ tự phụ thuộc:

- `Job B` chỉ được chạy sau khi `Job A` đã hoàn tất.
- `Job C` chỉ chạy khi `Job A` và `Job B` hoàn tất.

```
[A]   ----> Hoàn thành ----> [B]
[B]   ----> Hoàn thành ----> [C]
```

---

## 🎯 2. Các kỹ thuật phổ biến

### 📌 (1) Event-driven (Callback, Observer)

Job sẽ phát ra **event** khi hoàn tất, job tiếp theo **subscribe** và chạy khi nhận được event.

**Ví dụ (Python – `threading.Event`)**

```python
from threading import Thread, Event
import time

def job_a(done_event):
    print("Job A started")
    time.sleep(2)
    print("Job A done")
    done_event.set()  # phát event

def job_b(wait_event):
    print("Job B waiting for job A...")
    wait_event.wait()
    print("Job B started after job A done")

event_a_done = Event()
Thread(target=job_a, args=(event_a_done,)).start()
Thread(target=job_b, args=(event_a_done,)).start()
```

**Output:**
```
Job A started
Job B waiting for job A...
(2 giây sau)
Job A done
Job B started after job A done
```

---

### 📌 (2) Polling (liên tục kiểm tra trạng thái)

Job sau kiểm tra liên tục xem job trước đã xong chưa.

**Ví dụ (Polling):**

```python
import time

job_a_done = False

def job_a():
    global job_a_done
    print("Job A started")
    time.sleep(2)
    job_a_done = True
    print("Job A done")

def job_b():
    print("Job B polling for job A...")
    while not job_a_done:
        time.sleep(0.1)
    print("Job B started after job A done")

from threading import Thread
Thread(target=job_a).start()
Thread(target=job_b).start()
```

**Nhược điểm:** Tốn CPU vì phải kiểm tra liên tục.

---

### 📌 (3) Async/Await (Promises/Futures)

Dùng bất đồng bộ `async/await` để **đợi** job trước rồi tiếp tục job sau.

**Ví dụ (Python asyncio):**

```python
import asyncio

async def job_a():
    print("Job A started")
    await asyncio.sleep(2)
    print("Job A done")

async def job_b():
    print("Job B waiting for job A...")
    await job_a()
    print("Job B started after job A done")

asyncio.run(job_b())
```

**Output:**
```
Job B waiting for job A...
Job A started
(2 giây sau)
Job A done
Job B started after job A done
```

---

## ⚙️ 3. So sánh các phương pháp

| Phương pháp     | Ưu điểm                          | Nhược điểm                                | Tình huống dùng                            |
|------------------|----------------------------------|--------------------------------------------|--------------------------------------------|
| **Event-driven** | Hiệu quả, không tốn tài nguyên  | Cần quản lý event/subscription             | Microservices, distributed systems         |
| **Polling**      | Dễ implement                    | Tốn CPU, không hiệu quả                    | Tạm thời, nguyên mẫu                       |
| **Async/Await**  | Gọn, dễ đọc, dùng được trong I/O | Cần async runtime và thư viện hỗ trợ      | Xử lý I/O-bound như DB, API, file, v.v.   |

---

## 🧱 4. Thực tế Production (Job Dependency Handling)

### ✅ (1) **Job Scheduler** – ví dụ: Airflow, Celery, Luigi

Tự động quản lý dependency và switch context khi job hoàn thành.

**Ví dụ Airflow DAG:**

```python
from airflow import DAG
from airflow.operators.python import PythonOperator
from datetime import datetime

def job_a_func():
    print("Job A")

def job_b_func():
    print("Job B")

with DAG('example', start_date=datetime(2024, 1, 1), schedule_interval=None) as dag:
    task_a = PythonOperator(task_id='job_a', python_callable=job_a_func)
    task_b = PythonOperator(task_id='job_b', python_callable=job_b_func)

    task_a >> task_b  # job_b phụ thuộc vào job_a
```

---

### ✅ (2) **Message Queue** – ví dụ: Kafka, RabbitMQ, Redis

- `Job A` publish event "job_a_done"
- `Job B` subscribe event và chạy khi nhận được.

```
[Job A] ---> Kafka ---> [Job B listener]
```

---

## 🛠️ 5. Lưu ý khi chuyển context

- ✅ **Idempotency**: đảm bảo job không chạy nhiều lần khi nhận event trùng lặp.
- ✅ **Reliability**: dùng message queue có **durability** (Kafka, RabbitMQ).
- ✅ **Monitoring & Logging**: giám sát trạng thái, retry khi lỗi.

---

## 🚩 Kết luận (Recommendation)

- Dùng **event-driven** nếu bạn xây microservices hoặc hệ thống phân tán.
- Dùng **async/await** cho tác vụ bất đồng bộ nội bộ, đặc biệt với I/O.
- **Tránh polling** nếu có giải pháp tốt hơn.
- Nếu dùng production-level job dependency, ưu tiên dùng **Airflow**, **Celery**, hoặc **Kafka-based orchestration**.

---
