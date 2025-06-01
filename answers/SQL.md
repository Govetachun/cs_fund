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

## JOIN vs INNER JOIN vs LEFT JOIN

`INNER JOIN`: lấy các dòng trùng giữa hai bảng. Hiệu năng tốt nhất, thường dùng.

`LEFT JOIN`: lấy tất cả từ bảng trái, nếu không khớp thì điền NULL.

`RIGHT JOIN`: tương tự nhưng từ bảng phải.

`FULL OUTER JOIN`: lấy tất cả từ cả hai bên, chỗ không khớp thì NULL.

- Về performance:

Nếu có index, DB dùng hash join hoặc merge join: O(n + m).

Nếu không, nested loop: O(n × m).

`INNER JOIN` thường nhanh nhất, `FULL OUTER JOIN` tốn RAM và CPU nhất.

Tối ưu `JOIN` bằng cách: index đúng cột, tránh join nhiều bảng không cần thiết, và tránh full table scan.

## How to know your query is using index?
Use EXPLAIN (or EXPLAIN ANALYZE) to check if your query is using an index.
Look for terms like Index Scan (Postgres) or Using index (MySQL) in the output.
If you see Seq Scan or type = ALL, it means the index is not being used and may need optimization.

# ✅ Top 20 Câu SQL Phổ Biến và Trả Lời Ngắn Gọn

| #  | Câu hỏi | Trả lời súc tích |
|----|---------|------------------|
| 1 | JOIN các loại | `INNER`: chỉ dòng khớp; `LEFT`: giữ hết trái; `RIGHT`: giữ hết phải; `FULL`: giữ hết cả hai |
| 2 | GROUP BY vs HAVING | `GROUP BY` nhóm dữ liệu; `HAVING` lọc sau khi nhóm, còn `WHERE` lọc trước |
| 3 | NULL là gì? | Là giá trị chưa biết; so sánh cần dùng `IS NULL`, không dùng `= NULL` |
| 4 | UNION vs UNION ALL | `UNION`: loại trùng, `UNION ALL`: giữ trùng, nhanh hơn |
| 5 | EXISTS vs IN | `EXISTS`: dừng khi thấy match, dùng tốt với subquery lớn; `IN`: load toàn bộ list |
| 6 | Subquery | Subquery trong `SELECT`, `FROM`, `WHERE`; dùng khi cần tính toán hoặc lọc theo điều kiện phụ |
| 7 | ROW_NUMBER(), RANK() | `ROW_NUMBER`: không lặp số thứ tự; `RANK`: có thể nhảy số khi có tie |
| 8 | Lấy dòng thứ N | Dùng `LIMIT N OFFSET N-1` hoặc `ROW_NUMBER()` trong CTE |
| 9 | LIMIT vs OFFSET | `LIMIT`: số dòng trả về; `OFFSET`: bỏ qua N dòng đầu tiên |
| 10 | Tìm dòng duplicate | `GROUP BY col HAVING COUNT(*) > 1` |
| 11 | Xoá duplicate giữ 1 | Dùng CTE với `ROW_NUMBER()` rồi xoá `WHERE rn > 1` |
| 12 | CASE WHEN dùng khi nào | Thay cho `IF` trong SQL để xử lý logic có điều kiện trong `SELECT` |
| 13 | EXPLAIN để làm gì | Xem execution plan, biết có dùng index không |
| 14 | Index hoạt động thế nào | Giúp tìm nhanh theo `WHERE`, `ORDER BY`, `JOIN`; nếu không match prefix thì bỏ qua |
| 15 | DELETE vs TRUNCATE vs DROP | `DELETE`: xoá có điều kiện; `TRUNCATE`: xoá toàn bảng, không log; `DROP`: xoá bảng luôn |
| 16 | UPDATE có điều kiện | `UPDATE table SET col = val WHERE condition` |
| 17 | PRIMARY vs UNIQUE vs FOREIGN | `PRIMARY`: khóa chính, không NULL; `UNIQUE`: không trùng; `FOREIGN`: liên kết bảng khác |
| 18 | Tối ưu query chậm | Thêm index đúng cột, tránh `SELECT *`, dùng `EXPLAIN` để debug |
| 19 | Normalization | Chia bảng để giảm trùng lặp; tốt cho insert/update; join nhiều bảng thì chậm hơn |
| 20 | View vs Materialized View | `View`: truy vấn động; `Materialized`: lưu dữ liệu thật, nhanh nhưng cần refresh |

---

# 🔥 10 Câu SQL Tricky – Trả lời ngắn gọn

| # | Câu hỏi tricky | Trả lời súc tích |
|---|----------------|------------------|
| 1 | JOIN 3 bảng, lọc theo bảng 3 | Dùng `INNER JOIN` hoặc `EXISTS` để đảm bảo chỉ lấy khi bảng 3 có match |
| 2 | Lấy dòng mới nhất theo nhóm | Dùng `ROW_NUMBER() OVER (PARTITION BY group ORDER BY date DESC)` và lọc `rn = 1` |
| 3 | Sắp xếp nhiều điều kiện | `ORDER BY total DESC, signup_date ASC` |
| 4 | Top-N per group | `ROW_NUMBER() OVER (PARTITION BY group ORDER BY metric DESC)` và lọc `rn <= N` |
| 5 | So sánh 2 dòng liền kề | Dùng `LAG()` hoặc `LEAD()` để truy cập dòng trước/sau |
| 6 | Mua hàng liên tiếp 7 ngày | Dùng `DATEDIFF` kết hợp `LAG()` hoặc sessionization logic |
| 7 | UPDATE theo bảng khác | `UPDATE A SET A.col = B.val FROM B WHERE A.id = B.id` (Postgres style) |
| 8 | EXISTS vs LEFT JOIN IS NULL | `EXISTS` nhanh hơn khi subquery trả nhiều dòng; `LEFT JOIN ... IS NULL` dễ hiểu hơn |
| 9 | Phần trăm trong nhóm | Dùng `SUM(val) OVER (PARTITION BY group)` và chia từng dòng cho tổng |
| 10 | Đơn hàng của tháng gần nhất | Lấy `MAX(DATE_TRUNC('month', created_at))` rồi lọc theo tháng đó |

