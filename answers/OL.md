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
