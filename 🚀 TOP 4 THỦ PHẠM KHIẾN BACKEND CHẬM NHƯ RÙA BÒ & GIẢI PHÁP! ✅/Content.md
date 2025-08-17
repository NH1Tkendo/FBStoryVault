![🚀](https://static.xx.fbcdn.net/images/emoji.php/v9/tc6/1/16/1f680.png) TOP 4 "THỦ PHẠM" KHIẾN BACKEND CHẬM NHƯ RÙA BÒ & GIẢI PHÁP! ![✅](https://static.xx.fbcdn.net/images/emoji.php/v9/t33/1/16/2705.png)

# 1. Query "SELECT *" - Quả bom nổ chậm

![❌](https://static.xx.fbcdn.net/images/emoji.php/v9/tdd/1/16/274c.png) Không nên: SELECT * FROM users WHERE status = 'active';

![✅](https://static.xx.fbcdn.net/images/emoji.php/v9/t33/1/16/2705.png) Nên: SELECT id, name, email FROM users WHERE status = 'active';

Nguyên nhân gây chậm:

- Tăng băng thông mạng: Database phải truyền tải toàn bộ dữ liệu của tất cả cột, kể cả những cột không cần thiết. Với 1000 user, thay vì truyền 50KB cần thiết, server có thể phải truyền đến 100MB dữ liệu dư thừa.

- Tiêu hao RAM: Server phải load toàn bộ dữ liệu vào memory trước khi gửi cho client. Ví dụ bảng có 20 cột nhưng chỉ cần lấy 3 cột -> lãng phí 85% RAM.

- Chậm ổ cứng: Database phải đọc từ nhiều pages khác nhau trên ổ cứng thay vì chỉ đọc từ vài pages chứa các cột cần thiết.

- Cache kém hiệu quả: Database cache trở nên kém hiệu quả vì phải lưu toàn bộ dữ liệu dư thừa.

# 2. Query không có INDEX

![❌](https://static.xx.fbcdn.net/images/emoji.php/v9/tdd/1/16/274c.png) Query quét toàn bộ bảng: SELECT id, customer_id, total FROM orders WHERE customer_id = 12345;

![✅](https://static.xx.fbcdn.net/images/emoji.php/v9/t33/1/16/2705.png) Giải pháp: CREATE INDEX idx_customer_id ON orders(customer_id);

Nguyên nhân gây chậm: Database phải đọc từng dòng một trong toàn bộ bảng để tìm customer_id = 12345. Giống như bạn lật từng trang sách từ đầu đến cuối cho đến khi tìm thấy kết quả.

Tuy nhiên, không phải lúc nào bạn cũng cần phải tạo index, sau đây là một số trường hợp phổ biến không nên tạo index:

- Bảng nhỏ (dưới 5000 rows): Tìm kiếm vét cạn thậm chí nhanh hơn tìm kiếm bằng index.

- Cột ít giá trị (như gender: chỉ có Nam/Nữ): Index không hiệu quả.

- Bảng có nhiều INSERT/UPDATE: Sẽ làm chậm quá trình INSERT/UPDATE.

- Cột không bao giờ dùng trong WHERE/JOIN: Index chỉ tốn không gian ổ cứng.

# 3. Query với LIKE '%...%'

![❌](https://static.xx.fbcdn.net/images/emoji.php/v9/tdd/1/16/274c.png) Không nên: SELECT * FROM products WHERE name LIKE '%iphone%';

![✅](https://static.xx.fbcdn.net/images/emoji.php/v9/t33/1/16/2705.png) Tối ưu hơn:

- SELECT * FROM products WHERE name LIKE 'iphone%';

- Sử dụng Full-Text Search.

Nguyên nhân gây chậm:

- Index vô dụng: Khi có dấu % ở đầu (%iphone%), database không thể sử dụng index trên cột name. Index chỉ hoạt động khi biết chính xác ký tự bắt đầu.

- So sánh String chậm: Database phải thực hiện pattern matching cho từng dòng. Với 100,000 sản phẩm, server phải kiểm tra substring "iphone" có nằm trong mỗi tên sản phẩm không.

- Không cache được: Kết quả LIKE '%...%' rất khó cache hiệu quả vì có vô số biến thể.

Các trường hợp có thể dùng LIKE '%...%':

- Bảng nhỏ (dưới 5000 rows): Performance bị ảnh hưởng không đáng kể.

- One-time data cleanup: Scripts chạy offline, không ảnh hưởng trải nghiệm user.

- Có thể kết hợp LIMIT: LIKE '%keyword%' LIMIT 10.

# 4. Nested Query (N+1 Problem)

Thường gặp khi làm việc với ORM trong các Framework:

![❌](https://static.xx.fbcdn.net/images/emoji.php/v9/tdd/1/16/274c.png) Không nên:

```
const users = await User.findAll();

for (const user of users) {

user.postCount = await Post.count({ where: { userId: user['id'] }});

} // 1 + N queries!
```

![✅](https://static.xx.fbcdn.net/images/emoji.php/v9/t33/1/16/2705.png) Giải pháp:

const users = await User.findAll({ include: Post }); // 1 query

Nguyên nhân gây chậm:

- Quá nhiều truy vấn: Với 100 users, thay vì 1 lần truy vấn database, bạn tạo ra 101 lần truy vấn (1 + 100). Mỗi lần truy vấn (connection) sẽ có độ trễ về mạng nhất định (thường 1-5ms).

- Connection pool cạn kiệt: Database có giới hạn số lượng connection đồng thời. N+1 queries có thể làm cạn kiệt pool, khiến requests khác phải chờ.

- Database lock conflicts: Nhiều query nhỏ tạo ra nhiều nguy cơ xung đột lock, đặc biệt khi có write operations khác.

Khi nào N+1 có thể chấp nhận được:

- Lazy loading với cache: Nếu relationships đã được cache ở Redis/Memcached.

- Vòng lặp nhỏ (dưới 10 items).

- Development/prototype: Tạm thời để code nhanh, optimize sau.
---
![🚀](https://static.xx.fbcdn.net/images/emoji.php/v9/tc6/1/16/1f680.png) 5 BƯỚC KIỂM TRA VÀ TỐI ƯU QUERY CƠ BẢN BẠN PHẢI BIẾT

Bước 1: Sử dụng EXPLAIN

EXPLAIN SELECT * FROM users WHERE email = 'test@example.com';

Bước 2: Kiểm tra thời gian thực thi

Bất kỳ query nào thực thi trên 100ms đều cần được xem xét lại.

Bước 3: Tạo index cho các cột thường xuyên tìm kiếm

CREATE INDEX idx_email ON users(email);

CREATE INDEX idx_status_created ON users(status, created_at);

Bước 4: Nếu dữ liệu nhiều hãy phân trang thay vì lấy toàn bộ dữ liệu

SELECT id, name FROM users LIMIT 20 OFFSET 0;

Bước 5: Sử dụng cache cho các query phức tạp (Redis)

---
![🤔](https://static.xx.fbcdn.net/images/emoji.php/v9/t34/1/16/1f914.png) KHI NÀO BẠN BIẾT MÌNH ĐÃ TỐI ƯU OKAY?

- Thời gian phản hồi dưới 200ms cho các query cơ bản.

- CPU usage của database server ổn định dưới 70%.

- Không có query nào trong slow query log.

- Người dùng cảm thấy ứng dụng "mượt mà" và "nhanh".