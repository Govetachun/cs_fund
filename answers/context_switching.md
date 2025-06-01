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
