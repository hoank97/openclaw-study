# CAP Theorem

## 1. Khái niệm
CAP là viết tắt của:
- **Consistency**
- **Availability**
- **Partition Tolerance**

CAP Theorem nói rằng trong một **distributed system**, khi xảy ra **network partition**, hệ thống không thể đồng thời đảm bảo cả **Consistency** và **Availability** một cách tuyệt đối.

Nói đơn giản:
- khi mạng giữa các node bị chia cắt
- hệ thống phải nghiêng về một trong hai phía:
  - giữ dữ liệu nhất quán hơn (**CP**)
  - hoặc vẫn tiếp tục phục vụ request (**AP**)

---

## 2. Định nghĩa từng thành phần

### Consistency
Trong CAP, consistency nghĩa là:
> Sau khi ghi dữ liệu thành công, mọi lần đọc tiếp theo từ bất kỳ node nào cũng phải trả về bản mới nhất.

Ví dụ:
- ghi `balance = 80`
- đọc ở node A, B, C đều phải thấy `80`

Đây không phải là "consistency" theo nghĩa ACID constraints, mà là **single, up-to-date view of data**.

### Availability
Availability nghĩa là:
> Mọi request hợp lệ đều nhận được phản hồi, kể cả khi phản hồi đó có thể không chứa dữ liệu mới nhất.

Nói cách khác:
- hệ thống không được im
- không được timeout vô hạn
- không được từ chối hàng loạt chỉ vì đang mất đồng bộ

### Partition Tolerance
Partition tolerance nghĩa là:
> Hệ thống vẫn tiếp tục hoạt động khi có lỗi mạng giữa các node, khiến chúng không thể giao tiếp bình thường với nhau.

Ví dụ:
- node ở Hà Nội không kết nối được với node ở Sài Gòn
- cluster bị split thành 2 phần do network issue

Trong distributed system thực tế, partition là điều khó tránh khỏi.

---

## 3. Ý nghĩa thực tế của CAP
Cách diễn đạt "chọn 2 trong 3" nghe dễ nhớ nhưng hơi gây hiểu lầm.

Thực tế hơn là:
- trong distributed systems, **Partition Tolerance gần như bắt buộc phải có**
- khi **partition xảy ra**, hệ thống phải chọn giữa:
  - **Consistency**
  - **Availability**

Vì vậy, câu hỏi thực chiến thường là:
- hệ thống này khi mạng lỗi sẽ thiên về **CP** hay **AP**?

---

## 4. Ví dụ trực quan
Giả sử có 2 node:
- Node A
- Node B

Ban đầu cả hai đều có:
- `balance = 100`

Sau đó mạng giữa A và B bị đứt.

Một request ghi vào A cập nhật:
- `balance = 80`

Nhưng B không nhận được update.

Bây giờ có request đọc vào B.

### Nếu chọn CP
B sẽ từ chối trả lời hoặc báo lỗi vì nó không chắc dữ liệu của mình còn mới nhất.

**Kết quả:**
- dữ liệu đúng hơn
- nhưng hệ thống có thể không available

### Nếu chọn AP
B vẫn trả lời:
- `balance = 100`

**Kết quả:**
- hệ thống vẫn available
- nhưng dữ liệu có thể cũ

---

## 5. CP là gì?
**CP = Consistency + Partition Tolerance**

Khi có partition:
- ưu tiên dữ liệu nhất quán
- chấp nhận một phần hệ thống không phản hồi tạm thời

### Phù hợp với các bài toán
- ngân hàng
- payment
- inventory rất nhạy cảm
- booking, reservation
- metadata quan trọng

### Trade-off
- request có thể fail
- timeout có thể tăng
- trải nghiệm user có thể bị ảnh hưởng trong thời gian hệ thống chờ quorum hoặc leader

---

## 6. AP là gì?
**AP = Availability + Partition Tolerance**

Khi có partition:
- hệ thống vẫn tiếp tục phục vụ request
- chấp nhận dữ liệu tạm thời chưa đồng bộ

### Phù hợp với các bài toán
- social feed
- like/view count
- analytics dashboard
- recommendation
- notification status không quá critical

### Trade-off
- có thể đọc dữ liệu cũ
- các node có thể lệch nhau tạm thời
- phải chấp nhận eventual consistency

---

## 7. CA có ý nghĩa gì?
**CA = Consistency + Availability** nhưng không chấp nhận partition.

Trong distributed systems thực chiến:
- network partition là điều có thể xảy ra bất cứ lúc nào
- nên CA thuần gần như không phải lựa chọn nghiêm túc khi nói về hệ thống phân tán thực sự

CA chỉ có ý nghĩa mạnh khi:
- hệ thống chạy một node
- hoặc bỏ qua bài toán partition

---

## 8. Hiểu lầm phổ biến

### Hiểu lầm 1: CAP nghĩa là lúc nào cũng chọn đúng 2 trong 3
Sai. Ý đúng là:
- khi partition xảy ra, hệ thống phải chọn giữa consistency và availability

### Hiểu lầm 2: AP là hệ thống tệ
Sai. AP phù hợp với nhiều business case mà stale data tạm thời không phải vấn đề quá lớn.

### Hiểu lầm 3: CP luôn tốt hơn AP
Sai. Nếu bài toán là feed, metrics, recommendation, thì cố ép CP mọi nơi có thể làm hệ thống chậm và phức tạp không cần thiết.

### Hiểu lầm 4: CA là mục tiêu tối thượng
Sai. Trong distributed system, partition là chuyện có thật, nên CA không phải giấc mơ thực tế cho mọi hệ thống.

---

## 9. CAP và ACID Consistency khác nhau
Đây là điểm rất nhiều người nhầm.

### CAP Consistency
Mọi node nhìn thấy cùng một phiên bản dữ liệu mới nhất.

### ACID Consistency
Dữ liệu luôn thỏa mãn các ràng buộc và rule của hệ thống sau transaction.

Ví dụ:
- foreign key không bị vi phạm
- dữ liệu không rơi vào trạng thái invalid

Hai chữ "consistency" này không phải cùng một nghĩa.

---

## 10. CAP và eventual consistency
Nhiều hệ thống chọn availability khi xảy ra partition, nên phải chấp nhận **eventual consistency**.

Nghĩa là:
- dữ liệu có thể lệch tạm thời giữa các node
- nhưng sau một khoảng thời gian, hệ thống sẽ hội tụ về cùng một trạng thái

Ví dụ:
- like count giữa các region có thể chưa giống nhau ngay lập tức
- nhưng sau vài giây hoặc vài phút sẽ đồng bộ

---

## 11. Khi nào nên thiên CP?
Nên nghiêng CP khi dữ liệu sai là rất nguy hiểm hoặc gây thiệt hại rõ ràng:
- số dư tài khoản
- thanh toán
- inventory quan trọng
- đặt vé, đặt chỗ
- quota hoặc limit nghiêm ngặt

Nói ngắn gọn:
- thà báo lỗi tạm thời còn hơn trả dữ liệu sai

---

## 12. Khi nào nên thiên AP?
Nên nghiêng AP khi business chấp nhận stale data trong thời gian ngắn:
- social feed
- analytics
- recommendation
- notifications
- like/share/view counters
- cache distributed

Nói ngắn gọn:
- cứ phục vụ request trước, đồng bộ lại sau

---

## 13. CAP trong thiết kế thực tế
Một hệ thống lớn thường không hoàn toàn CP hoặc AP cho mọi thứ.

Cùng một product có thể chia như sau:
- payment: nghiêng CP
- inventory: nghiêng CP
- analytics: nghiêng AP
- notification: nghiêng AP
- recommendation: nghiêng AP

Vì vậy, khi system design không nên hỏi kiểu ngây ngô:
- "Hệ thống này là CP hay AP?"

Mà nên hỏi:
- use case nào cần correctness tuyệt đối?
- use case nào chấp nhận stale data?
- SLA ưu tiên là data accuracy hay service uptime?

---

## 14. Liên hệ với Event-Driven Architecture
Event-driven systems thường:
- phân tán
- nhiều service độc lập
- có async processing
- chấp nhận eventual consistency ở một số luồng

Do đó, chúng thường mang hơi hướng:
- chấp nhận stale state tạm thời
- ưu tiên scale và availability cho một số workflow

Ví dụ:
- order đã tạo xong
- notification chưa gửi ngay
- analytics chưa cập nhật ngay

Đó là biểu hiện của trade-off giữa availability và consistency.

---

## 15. PACELC — mở rộng thực tế hơn CAP
CAP chỉ nói về lúc **có partition**.

Trong thực tế, ngay cả khi không có partition, hệ thống vẫn còn trade-off giữa:
- **Latency**
- **Consistency**

PACELC nói rằng:
- **If Partition, choose Availability or Consistency**
- **Else, choose Latency or Consistency**

Điều này phản ánh thực tế hơn rất nhiều.

Ví dụ:
- đọc từ replica gần user → latency thấp hơn nhưng có thể stale
- đọc từ leader → consistency cao hơn nhưng latency cao hơn

---

## 16. Cách trả lời phỏng vấn ngắn gọn
Một câu trả lời khá ổn:

> CAP theorem nói rằng trong distributed systems, khi xảy ra network partition, ta không thể đồng thời đảm bảo strong consistency và availability. Vì partition là điều không thể tránh khỏi, nên thực tế hệ thống phải chọn nghiêng về CP hoặc AP tùy use case. Những dữ liệu critical như payment hoặc inventory thường thiên về consistency, còn feed, analytics hay like count thường chấp nhận eventual consistency để giữ availability.

---

## 17. Tóm tắt
- **C**: đọc ở mọi node đều ra bản mới nhất
- **A**: request hợp lệ nào cũng được trả lời
- **P**: hệ thống tiếp tục hoạt động dù mạng bị chia cắt

Khi partition xảy ra:
- muốn dữ liệu đúng hơn → nghiêng **CP**
- muốn hệ thống luôn phản hồi → nghiêng **AP**

Không có lựa chọn nào đúng tuyệt đối cho mọi bài toán. Tất cả phụ thuộc vào business requirement và trade-off chấp nhận được.
