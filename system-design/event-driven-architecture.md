# Event-Driven Architecture

## 1. Khái niệm
Event-Driven Architecture (EDA) là kiểu thiết kế hệ thống trong đó các service giao tiếp với nhau thông qua **event** thay vì phụ thuộc hoàn toàn vào các lời gọi đồng bộ trực tiếp.

Thay vì:
- Order Service gọi trực tiếp Payment Service
- rồi gọi Inventory Service
- rồi gọi Notification Service

thì Order Service chỉ cần phát ra một event như:
- `OrderCreated`

Các service khác nếu quan tâm sẽ tự subscribe và xử lý.

---

## 2. Thành phần chính

### Producer
Service tạo ra event.

Ví dụ:
- Order Service phát `OrderCreated`
- User Service phát `UserRegistered`

### Event
Một sự kiện đã xảy ra trong hệ thống.

Ví dụ:
- `OrderCreated`
- `PaymentSucceeded`
- `UserLoggedIn`
- `EmailSent`

### Broker / Message Bus
Lớp trung chuyển event giữa producer và consumer.

Ví dụ công nghệ thường dùng:
- Kafka
- RabbitMQ
- NATS
- AWS SNS/SQS
- Google Pub/Sub

### Consumer
Service nhận event và xử lý logic.

Ví dụ:
- Payment Service
- Inventory Service
- Notification Service
- Analytics Service

---

## 3. Luồng ví dụ cơ bản

### Bài toán: đặt hàng trong e-commerce
1. User tạo đơn hàng
2. Order Service lưu order vào DB
3. Order Service phát event `OrderCreated`
4. Payment Service nhận event và xử lý thanh toán
5. Inventory Service nhận event và trừ tồn kho
6. Notification Service gửi email xác nhận
7. Analytics Service cập nhật thống kê

Điểm quan trọng: Order Service không cần biết chi tiết từng service còn lại đang xử lý như thế nào.

---

## 4. Điểm mạnh

### Giảm coupling
Các service ít phụ thuộc trực tiếp vào nhau hơn. Một service chỉ cần biết cách phát event, không phải gọi cứng vào nhiều endpoint khác.

### Scale độc lập
Nếu Notification Service quá tải, chỉ cần scale riêng nó thay vì scale cả hệ thống.

### Thêm tính năng dễ
Muốn thêm Fraud Detection hoặc Audit Service chỉ cần subscribe event hiện có, không cần sửa logic gốc quá nhiều.

### Hợp với xử lý bất đồng bộ
Rất phù hợp với:
- email
- analytics
- background jobs
- stream processing
- audit log
- notification pipeline

---

## 5. Trade-off lớn nhất: eventual consistency
Trong hệ thống event-driven, dữ liệu giữa các service có thể **không đồng bộ ngay lập tức**.

Ví dụ:
- Order đã được tạo
- nhưng Payment chưa xử lý xong
- Inventory chưa trừ kho
- Email chưa gửi

Tại một thời điểm, mỗi service có thể đang ở một trạng thái khác nhau.

### Hệ quả
Không thể assume:
- có order là chắc đã thanh toán
- thanh toán xong là chắc đã gửi mail

Vì vậy, phải thiết kế state rõ ràng, ví dụ:
- `PENDING_PAYMENT`
- `PAID`
- `PAYMENT_FAILED`
- `CANCELLED`

Nếu không model trạng thái trung gian rõ ràng, hệ thống sẽ rất dễ rối.

---

## 6. Idempotency — sống còn trong hệ thống event-driven
Trong thực tế, broker thường đảm bảo kiểu **at-least-once delivery**.
Điều đó có nghĩa là cùng một event có thể bị xử lý nhiều lần.

Ví dụ:
- email bị gửi 2 lần
- inventory bị trừ 2 lần
- payment bị charge lặp

### Giải pháp
Thiết kế consumer theo hướng **idempotent**:
- cùng một event xử lý nhiều lần nhưng kết quả cuối vẫn như nhau

### Cách làm phổ biến
- gắn `event_id`
- lưu bảng các event đã xử lý
- nếu event đã được xử lý rồi thì bỏ qua
- hoặc dùng business key như `order_id + event_type`

Không có idempotency thì hệ thống sớm muộn gì cũng có bug rất đau đầu.

---

## 7. Ordering problem
Không phải lúc nào event cũng tới đúng thứ tự mong muốn.

Ví dụ:
- `OrderCancelled` đến trước `OrderCreated`
- `PaymentFailed` đến sau `PaymentSucceeded`

### Cách xử lý
- ưu tiên ordering theo từng entity (`order_id`) thay vì đòi global ordering
- partition theo key phù hợp
- thiết kế state machine chịu được event đến lệch hoặc đến trễ

Không nên ngây thơ tin rằng mọi event trong hệ thống luôn tới đúng thứ tự tuyệt đối.

---

## 8. Dual write problem
Đây là lỗi kinh điển.

Ví dụ Order Service làm 2 việc:
1. ghi order vào DB
2. publish event `OrderCreated`

Nếu ghi DB thành công nhưng publish event thất bại:
- order tồn tại trong DB
- nhưng các service khác không biết gì

Nếu publish event thành công nhưng ghi DB thất bại:
- consumer nghĩ order tồn tại
- nhưng dữ liệu thật lại không có

=> trạng thái hệ thống bị lệch.

---

## 9. Transactional Outbox Pattern
Đây là cách xử lý chuẩn cho dual write problem.

### Ý tưởng
Thay vì ghi DB rồi publish broker trực tiếp, service sẽ:
1. ghi business data vào DB
2. ghi thêm 1 record vào bảng `outbox`
3. commit cùng một transaction

Sau đó một tiến trình riêng sẽ:
1. đọc các bản ghi trong `outbox`
2. publish event ra broker
3. đánh dấu đã publish

### Lợi ích
- dữ liệu business và event intent được lưu atomically
- tránh trường hợp DB thành công nhưng event biến mất không kiểm soát

Transactional Outbox là pattern cực kỳ quan trọng khi build microservices hoặc event-driven systems nghiêm túc.

---

## 10. Saga Pattern
Với các luồng business dài nhiều bước, không thể dùng một transaction ACID duy nhất xuyên toàn hệ thống.

Ví dụ flow:
1. tạo order
2. thanh toán
3. giữ hàng trong kho
4. tạo shipment

Nếu bước 3 fail thì phải làm gì với payment đã thành công?

### Đây là lúc cần Saga
Saga giúp xử lý các business transaction phân tán bằng chuỗi step + compensation.

### Hai kiểu chính

#### Choreography
Mỗi service nghe event và tự phát event tiếp.

Ví dụ:
- `OrderCreated`
- `PaymentSucceeded`
- `InventoryReserved`
- `ShipmentCreated`

**Ưu:** phân tán, ít điểm trung tâm  
**Nhược:** logic business rải rác, khó theo dõi toàn flow

#### Orchestration
Có một orchestrator điều phối toàn flow.

**Ưu:** nhìn flow rõ, dễ control  
**Nhược:** dễ biến orchestrator thành God service nếu thiết kế tệ

### Compensation
Nếu payment thành công nhưng inventory fail thì phải có bước bù trừ như:
- refund
- mark order failed
- gửi thông báo

---

## 11. DLQ (Dead Letter Queue)
Nếu consumer fail liên tục, không thể retry vô hạn.

### Cách xử lý chuẩn
- retry một số lần có kiểm soát
- nếu vẫn fail thì đưa event vào DLQ

### DLQ giúp gì?
- giữ lại event lỗi
- tránh làm nghẽn luồng chính
- cho phép debug và replay sau

Không có DLQ, hệ thống rất dễ rơi vào cảnh fail âm thầm hoặc queue bị nghẽn.

---

## 12. Observability
Event-driven system rất khó debug nếu không có observability tốt.

### Nên có tối thiểu
- `event_id`
- `correlation_id`
- `trace_id`
- structured logging
- metrics
- distributed tracing
- monitoring queue lag
- DLQ monitoring

Nếu không có các metadata này, việc trace một luồng nghiệp vụ xuyên nhiều service sẽ cực kỳ đau đầu.

---

## 13. Thiết kế payload event
Có hai cực đoan thường gặp:

### Fat event
Chứa khá nhiều dữ liệu.

**Ưu:** consumer ít phải gọi ngược producer  
**Nhược:** payload to, khó version, dễ lộ data thừa

### Thin event
Chỉ chứa ít data, ví dụ `order_id`.

**Ưu:** gọn, ít phụ thuộc schema  
**Nhược:** consumer phải query thêm, dễ tăng coupling runtime

### Cách chọn thực tế
Nên tìm điểm cân bằng:
- đủ data để consumer xử lý phần lớn use case độc lập
- nhưng không nhồi mọi thứ vào event

---

## 14. Versioning event
Schema event sẽ thay đổi theo thời gian.

Ví dụ thêm field:
- `discount_code`
- `currency`
- `region`

Nếu thay đổi bừa bãi, consumer cũ có thể vỡ.

### Best practice
- dùng `schema_version`
- giữ backward compatibility
- thêm field optional trước
- tránh rename hoặc xóa field nóng vội

---

## 15. Khi nào nên dùng?
Event-driven rất hợp khi:
- hệ thống lớn
- có nhiều bounded contexts
- workflow bất đồng bộ nhiều
- cần scale độc lập
- nhiều consumer cùng quan tâm một business event

Ví dụ:
- e-commerce
- fintech
- notification platform
- analytics pipeline
- IoT ingestion
- audit log system

---

## 16. Khi nào không nên dùng?
Không nên lạm dụng trong các trường hợp:
- hệ thống nhỏ, ít team, logic đơn giản
- cần đồng bộ mạnh và phản hồi ngay
- team chưa đủ năng lực vận hành, monitor, debug distributed system

Nhiều đội áp dụng event-driven quá sớm sẽ tự đẩy hệ thống vào độ phức tạp không cần thiết.

---

## 17. So sánh nhanh với synchronous REST

### REST / gRPC sync
**Ưu điểm:**
- dễ hiểu
- dễ debug hơn
- phù hợp request/response trực tiếp

**Nhược điểm:**
- coupling cao hơn
- một service chậm có thể kéo theo chuỗi chậm

### Event-driven
**Ưu điểm:**
- linh hoạt
- scale tốt
- decoupled hơn
- hợp các async workflows

**Nhược điểm:**
- khó reasoning hơn
- eventual consistency
- duplicate handling
- observability khó hơn

### Kết luận
Hệ thống tốt thường là **hybrid**:
- chỗ cần phản hồi ngay thì dùng sync
- chỗ cần xử lý bất đồng bộ thì dùng events

---

## 18. Sai lầm phổ biến
- Tách microservices quá sớm
- Không có idempotency
- Không có outbox
- Không theo dõi DLQ
- Event naming lộn xộn
- Consumer phụ thuộc ngược producer quá nhiều
- Không có correlation ID

---

## 19. Tóm tắt
Event-Driven Architecture rất mạnh nếu dùng đúng bài:
- decoupling tốt
- scale độc lập
- mở rộng tính năng dễ
- hợp workflow bất đồng bộ

Nhưng cái giá phải trả là:
- eventual consistency
- duplicate events
- ordering issues
- khó debug hơn nhiều
- cần kỷ luật kỹ thuật cao

Đây không phải là kiểu thiết kế “xịn hơn mọi thứ khác”, mà là một công cụ mạnh với trade-off rõ ràng.

---

## 20. Từ khóa nên nhớ
- Event
- Producer / Consumer
- Broker
- Eventually Consistent
- Idempotency
- Ordering
- Dual Write
- Transactional Outbox
- Saga
- DLQ
- Observability
- Event Versioning
