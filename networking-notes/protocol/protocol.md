# Ghi chú: Protocol (giao thức)

## 1. Khái niệm

- **Protocol** là thỏa thuận chuẩn hóa giữa các bên độc lập, quy định cách tương tác để hợp tác được mà không cần biết nội bộ của nhau.
- Hiểu ngắn gọn: **kịch bản hội thoại chuẩn hóa**, gồm lời thoại và thứ tự lượt nói.
- Các bên có thể là máy với máy, chương trình với chương trình, hoặc người với người (ví dụ phi công và kiểm soát viên).
- Ví dụ: gọi điện thoại, bạn nói "A lô", bên kia đáp "A lô", rồi mới vào chuyện.

## 2. Bốn thành phần của protocol

| Thành phần | Ý nghĩa |
|---|---|
| Cú pháp | Thông điệp có định dạng thế nào |
| Ngữ nghĩa | Thông điệp có nghĩa gì, bên nhận phải làm gì |
| Thời gian và thứ tự | Ai nói trước, chờ bao lâu, mất gói thì sao |
| Trạng thái | Đang ở giai đoạn nào thì thông điệp nào mới hợp lệ |

Về bản chất, protocol là một **máy trạng thái phân tán**: mỗi bên giữ một bản, và protocol giữ cho các bản không lệch nhau.

## 3. Cái gì không phải protocol

- Protocol cần **nhiều bên** và **chuỗi tương tác có trạng thái**.
- Hàm `sort()` không phải protocol: chỉ một chương trình, gọi một lần, xong.
- Thuật toán chạy trong một thực thể không phải protocol. Thuật toán chạy giữa nhiều nút (ví dụ Raft) thì là protocol.
- Định dạng file (PNG) có cú pháp nhưng không có tương tác nên không phải protocol.
- Ví dụ: `sort()` chạy trên server khác, gọi qua mạng. Khi đó cách hai bên nói chuyện để dùng nó mới là protocol, còn thuật toán sắp xếp thì không.

## 4. Phân tầng

- Mỗi tầng giải quyết **một vấn đề** và tin tưởng tầng dưới.
- Ví dụ khi mở trang web HTTPS: **DNS** (tên miền thành IP), **TCP** (kết nối tin cậy), **TLS** (mã hóa, xác thực danh tính), **HTTP** (yêu cầu và nội dung).

## 5. TCP handshake (bắt tay ba bước)

Các bước:
1. Client gửi **SYN**: "Bạn có sẵn sàng không?"
2. Server gửi **SYN-ACK**: "Sẵn sàng. Bạn còn muốn kết nối không?"
3. Client gửi **ACK**: "Có." Sau đó mới truyền dữ liệu.

Mục đích:
- Xác nhận đối phương đang sống.
- Thống nhất số thứ tự khởi đầu của dữ liệu.
- Không phải để xác định danh tính (việc đó thuộc TLS).

Vì sao ba bước, không phải hai:
- Ba bước là tối thiểu để cả hai bên biết chiều gửi và chiều nhận đều thông.
- Với hai bước, server không phân biệt được SYN mới với SYN cũ bị kẹt trên mạng, nên cấp tài nguyên cho kết nối không ai còn muốn.
- Ví dụ shipper: không gọi xác nhận mà chở hàng đến thì không ai nhận. Nhận đơn cũ đã hủy mà không hỏi lại thì giao oan.
- Nếu client nhận SYN-ACK cho yêu cầu mình không gửi, nó gửi **RST** (hủy) để server giải phóng tài nguyên.

Liên quan: **SYN flood** là tấn công gửi hàng loạt SYN giả để làm cạn tài nguyên server. Cách chống: **SYN cookies**.

## 6. Mất gói và gửi lại

- Khi im lặng, client **không phân biệt được** "yêu cầu bị mất" với "phản hồi bị mất".
- Ví dụ: client gửi `PAY 100`, server trừ tiền và trả `OK`, nhưng `OK` bị mất. Client gửi lại thì bị trừ tiền lần hai.
- Tăng thời gian chờ không giải quyết được, vì phản hồi đã mất thì không bao giờ đến.

## 7. Idempotency (lặp lại an toàn)

- **Idempotency**: thực hiện nhiều lần cho cùng hiệu quả như một lần.
- Cách làm: mỗi yêu cầu có một **mã định danh duy nhất**, ví dụ `PAY 100, id=abc123`.
- Server lưu các `id` đã xử lý. Nhận lại `id` cũ thì không xử lý lại, chỉ trả kết quả cũ.
- Client khi gửi lại phải dùng **đúng `id` cũ**, và nhớ `id` của yêu cầu đang chờ.
- Ví dụ: shipper nhìn sổ thấy đơn 123 đã giao thì không giao thêm. Thực tế dùng ở Stripe, MoMo (idempotency key, mã giao dịch).

## 8. Lưu trạng thái ở nơi bền vững

- Việc kiểm tra trùng phải nằm ở **server**, vì chỉ server biết đã xử lý hay chưa. Chỉ lưu ở client thì không đủ.
- Danh sách `id` phải ở **database hoặc đĩa**, không chỉ trong RAM, vì server khởi động lại sẽ mất RAM.
- Ví dụ: sổ "đơn 123 đã giao" phải viết ra giấy, không chỉ nhớ trong đầu.

## 9. Giao dịch nguyên tử (atomic transaction)

Server phải làm hai việc: **(a)** trừ tiền, **(b)** ghi `id` đã xử lý. Nếu sập giữa chừng:
- Làm (a) trước: tiền đã trừ nhưng chưa ghi `id`, gửi lại sẽ bị trừ lần hai.
- Làm (b) trước: đã ghi "xử lý rồi" nhưng chưa trừ tiền, khách không bị trừ và yêu cầu coi như xong.
- Giải pháp: **giao dịch nguyên tử**, hai việc hoặc cùng xảy ra, hoặc cùng không xảy ra (database hỗ trợ sẵn).

## 10. Thuật ngữ nhanh

| Thuật ngữ | Ý nghĩa |
|---|---|
| Protocol | Kịch bản tương tác chuẩn hóa giữa các bên độc lập |
| ACK | Thông điệp xác nhận đã nhận |
| SYN, SYN-ACK | Các bước mở đầu của TCP handshake |
| RST | Hủy kết nối |
| SYN flood | Tấn công làm cạn tài nguyên bằng SYN giả |
| SYN cookies | Cách chống SYN flood |
| Idempotency | Lặp lại nhiều lần = hiệu quả như một lần |
| Idempotency key | Mã định danh để server nhận ra yêu cầu trùng |
| Atomic transaction | Nhiều việc cùng xảy ra hoặc cùng không xảy ra |
| Máy trạng thái | Mô hình các trạng thái và chuyển trạng thái hợp lệ |
