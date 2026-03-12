# CAP vs ACID vs BASE

## 1. Vì sao dễ nhầm?
Ba khái niệm này đều xuất hiện rất nhiều khi học database và distributed systems:
- **CAP**
- **ACID**
- **BASE**

Nhìn qua thì chúng đều liên quan đến consistency, reliability, correctness, nhưng thực chất chúng trả lời **những câu hỏi khác nhau**.

Nếu không tách bạch, rất dễ nói sai kiểu:
- "CAP consistency giống ACID consistency"
- "BASE là ngược lại hoàn toàn với ACID"
- "Hệ thống AP thì không thể có transaction"

Các câu kiểu đó thường là hiểu nửa mùa.

---

## 2. ACID là gì?
ACID là tập hợp các tính chất của **transaction** trong database truyền thống.

### A — Atomicity
Transaction hoặc thành công toàn bộ, hoặc rollback toàn bộ.

Ví dụ:
- trừ tiền ở tài khoản A
- cộng tiền ở tài khoản B

Không được phép xảy ra kiểu:
- trừ ở A rồi
- nhưng cộng vào B thất bại

### C — Consistency
Sau transaction, dữ liệu phải vẫn hợp lệ theo các rule và constraint.

Ví dụ:
- không vi phạm foreign key
- tổng số dư không rơi vào trạng thái vô lý theo rule business

### I — Isolation
Các transaction concurrent không được làm lộ trạng thái trung gian của nhau một cách sai lệch.

### D — Durability
Khi transaction đã commit thành công, dữ liệu phải được giữ lại ngay cả khi hệ thống crash sau đó.

### ACID trả lời câu hỏi gì?
> Làm sao để một transaction trong database giữ được tính đúng đắn và an toàn?

---

## 3. CAP là gì?
CAP là một định lý cho **distributed systems**.

Nó nói rằng khi xảy ra **network partition**, hệ thống không thể đồng thời đảm bảo cả:
- **Consistency**
- **Availability**
- **Partition Tolerance**

Thực tế hơn:
- partition là điều gần như không tránh được
- nên khi network lỗi, hệ thống phải nghiêng về **CP** hoặc **AP**

### CAP trả lời câu hỏi gì?
> Khi hệ thống phân tán bị lỗi mạng, ta ưu tiên dữ liệu nhất quán hay ưu tiên tiếp tục phục vụ request?

---

## 4. BASE là gì?
BASE thường được xem là cách tư duy phù hợp hơn cho nhiều distributed systems hiện đại.

BASE là viết tắt của:
- **Basically Available**
- **Soft state**
- **Eventual consistency**

### Basically Available
Hệ thống cố gắng luôn phản hồi request, kể cả khi phản hồi đó chưa phải dữ liệu mới nhất.

### Soft state
Trạng thái hệ thống có thể thay đổi theo thời gian, kể cả không có input mới từ client, do cơ chế replication hoặc background sync.

### Eventual consistency
Nếu không có update mới, dữ liệu giữa các node cuối cùng sẽ hội tụ về cùng một trạng thái.

### BASE trả lời câu hỏi gì?
> Nếu chấp nhận consistency yếu hơn, hệ thống phân tán có thể đạt scale và availability tốt hơn bằng cách nào?

---

## 5. Điểm khác nhau cốt lõi

### ACID tập trung vào transaction correctness
- level database / transaction
- thường gắn với relational DB hoặc transactional guarantees

### CAP tập trung vào trade-off khi có network partition
- level distributed system
- nói về hành vi hệ thống khi mạng lỗi

### BASE tập trung vào tư duy thiết kế chấp nhận eventual consistency
- level distributed architecture
- nói về cách sống chung với inconsistency tạm thời để đổi lấy scale và availability

---

## 6. So sánh nhanh

| Khái niệm | Trả lời câu hỏi gì? | Bối cảnh chính | Từ khóa |
|---|---|---|---|
| ACID | Transaction có an toàn và đúng không? | Database / transaction | Atomicity, Isolation, Durability |
| CAP | Khi network partition xảy ra thì ưu tiên gì? | Distributed systems | CP, AP, Partition |
| BASE | Làm sao hệ thống vẫn available và scale tốt dù consistency yếu hơn? | Distributed systems / large-scale systems | Eventually consistent, soft state |

---

## 7. Chữ "Consistency" bị nhầm nhiều nhất
Đây là chỗ quan trọng nhất.

### ACID Consistency
Nghĩa là:
- dữ liệu phải hợp lệ theo schema, constraint, business rule

Ví dụ:
- không được tạo order tham chiếu đến user không tồn tại
- tổng tiền phải khớp rule hệ thống

### CAP Consistency
Nghĩa là:
- mọi node phải thấy cùng một version dữ liệu mới nhất

Ví dụ:
- ghi `stock = 0`
- đọc ở bất kỳ node nào cũng phải thấy `0`

### BASE / eventual consistency
Nghĩa là:
- tại một thời điểm, các node có thể chưa giống nhau
- nhưng cuối cùng sẽ đồng bộ về cùng trạng thái

Ba cái consistency này liên quan nhau nhưng không đồng nghĩa.

---

## 8. Một hệ thống có thể vừa ACID vừa dính CAP không?
Có.

Ví dụ:
- một database trên từng node có thể vẫn dùng transaction ACID nội bộ
- nhưng khi mở rộng ra nhiều node / nhiều region / nhiều replica, hệ thống tổng thể vẫn phải đối mặt với trade-off của CAP

Điểm này rất quan trọng:
- **ACID không loại bỏ CAP**
- **CAP không phủ nhận ACID**

Chúng ở hai lớp vấn đề khác nhau.

---

## 9. BASE có phải là “không cần đúng” không?
Không.

BASE không có nghĩa là:
- thích lưu gì thì lưu
- dữ liệu sai kệ nó
- không cần transaction

BASE chỉ nghĩa là:
- trong distributed systems, đôi khi ta chấp nhận consistency yếu hơn trong ngắn hạn
- để đổi lấy availability và scalability

Hệ thống BASE tốt vẫn cần:
- idempotency
- retry strategy
- conflict resolution
- monitoring
- data reconciliation

Nếu không, nó sẽ thành mớ hỗn độn chứ không phải kiến trúc đúng nghĩa.

---

## 10. Ví dụ thực tế

### Ví dụ ACID điển hình: chuyển tiền trong cùng một DB
Yêu cầu:
- trừ tài khoản A
- cộng tài khoản B
- cả hai phải thành công hoặc rollback toàn bộ

Đây là bài toán transaction correctness điển hình.

### Ví dụ CAP điển hình: hệ thống replicated multi-node
Yêu cầu:
- dữ liệu được replicate giữa nhiều node
- nếu network split xảy ra thì phải chọn:
  - chờ đồng bộ để giữ consistency
  - hay tiếp tục trả lời để giữ availability

Đây là bài toán trade-off của distributed system.

### Ví dụ BASE điển hình: social feed / analytics
Yêu cầu:
- hệ thống phải phản hồi nhanh
- dữ liệu đếm hoặc feed có thể lệch vài giây
- cuối cùng sẽ đồng bộ lại

Đây là tư duy eventual consistency.

---

## 11. ACID vs BASE có phải đối lập tuyệt đối?
Không hẳn.

Nhiều tài liệu nói:
- ACID = kiểu cứng, chặt, DB truyền thống
- BASE = kiểu mềm, eventual consistency, distributed scale

Điều đó đúng một phần, nhưng không nên hiểu là hai phe thù địch tuyệt đối.

Thực tế:
- trong cùng một hệ thống, một số thành phần có thể cần ACID mạnh
- một số thành phần khác có thể chọn BASE

Ví dụ e-commerce:
- payment ledger → cần transaction mạnh, gần ACID hơn
- recommendation / analytics / notifications → có thể thiên BASE

---

## 12. CAP và BASE liên quan nhau thế nào?
BASE thường là cách tư duy của nhiều hệ thống chọn thiên về **Availability** trong bối cảnh CAP.

Nếu hệ thống chấp nhận:
- phản hồi nhanh
- dữ liệu có thể stale tạm thời
- đồng bộ dần sau

thì đó là tinh thần của BASE.

Nói cách khác:
- **CAP** mô tả trade-off
- **BASE** là một cách thiết kế để sống chung với trade-off đó

---

## 13. Khi nào ưu tiên ACID?
Ưu tiên ACID khi:
- dữ liệu tài chính quan trọng
- transaction phải đúng tuyệt đối
- constraint business nghiêm ngặt
- sai một bước là thiệt hại thật

Ví dụ:
- banking ledger
- payment records
- inventory reservation rất nhạy cảm
- order billing

---

## 14. Khi nào nghiêng BASE?
Nghiêng BASE khi:
- scale lớn
- phân tán nhiều node/region
- cần availability cao
- business chấp nhận stale data ngắn hạn

Ví dụ:
- social feed
- analytics
- recommendation
- cache distributed
- metrics aggregation

---

## 15. Nhìn theo tầng kiến trúc
Một cách hiểu rất gọn:

### ACID
- nói về **transaction layer**

### CAP
- nói về **distributed system trade-off layer**

### BASE
- nói về **distributed design philosophy layer**

Nhìn như vậy sẽ đỡ lẫn.

---

## 16. Một hệ thống lớn thường dùng cả 3 góc nhìn
Hệ thống thực tế không chọn một rồi bỏ hai cái còn lại.

Ví dụ một app thương mại điện tử:
- DB cho payment dùng transaction ACID mạnh
- toàn hệ thống nhiều service vẫn chịu trade-off CAP khi có partition
- analytics và notification có thể được thiết kế theo kiểu BASE / eventual consistency

Đó mới là tư duy thực chiến.

---

## 17. Tóm tắt cực ngắn

### ACID
- lo chuyện transaction đúng, an toàn, bền vững

### CAP
- lo chuyện khi mạng lỗi trong distributed system thì ưu tiên consistency hay availability

### BASE
- lo chuyện làm sao hệ thống scale và luôn phản hồi bằng cách chấp nhận eventual consistency

---

## 18. Câu chốt dễ nhớ
- **ACID**: đúng trong transaction
- **CAP**: chọn trade-off khi partition
- **BASE**: sống chung với inconsistency để scale tốt hơn
