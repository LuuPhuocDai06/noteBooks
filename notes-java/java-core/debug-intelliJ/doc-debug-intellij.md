# Khái niệm: Debug bằng IntelliJ IDEA

## 1. Debug là gì?

Debug là chạy chương trình **từng bước có kiểm soát**, dừng tại những điểm bạn chọn để xem giá trị biến và luồng chạy, từ đó hiểu vì sao chương trình sai. Mục tiêu không chỉ là chặn lỗi mà là **hiểu vì sao dữ liệu sai**.

## 2. Breakpoint

**Breakpoint** là điểm chương trình dừng lại.

- Đặt: click vào lề trái của dòng code (hiện chấm đỏ).
- Chạy: bấm **Debug** (`Shift+F9`), không phải Run.
- Khi dừng, dòng hiện tại được tô sáng, chưa được thực thi.

## 3. Các thao tác điều khiển (Windows)

| Thao tác | Phím | Ý nghĩa |
|---|---|---|
| Step Over | `F8` | Chạy dòng hiện tại, không chui vào hàm |
| Step Into | `F7` | Chui vào hàm đang gọi |
| Step Out | `Shift+F8` | Chạy hết hàm hiện tại, quay ra nơi gọi |
| Resume | `F9` | Chạy tiếp đến breakpoint kế |
| Run to Cursor | `Alt+F9` | Chạy đến dòng con trỏ |
| Evaluate Expression | `Alt+F8` | Tính thử một biểu thức ngay lúc đang dừng |

## 4. Các panel quan trọng

- **Frames**: chính là call stack, click từng frame để xem biến ở từng tầng gọi.
- **Variables**: giá trị các biến hiện tại.
- **Watches**: ghim biểu thức để theo dõi qua từng bước.

## 5. Breakpoint nâng cao

Chuột phải vào chấm đỏ để cấu hình:

| Loại | Công dụng | Ví dụ |
|---|---|---|
| **Condition** | Chỉ dừng khi điều kiện đúng | `i == 99` |
| **Logpoint** (Evaluate and log) | In log mà không dừng chương trình, không cần sửa code | `"i = " + i` |
| **Exception Breakpoint** (`Ctrl+Shift+F8`) | Dừng đúng lúc exception được ném | `NullPointerException` |

## 6. Quy trình debug gợi ý

1. Đọc stack trace để biết lỗi gì, xảy ra ở đâu.
2. Đặt breakpoint trước dòng nghi ngờ.
3. Chạy Debug, dùng Step Over/Into và quan sát **Variables**.
4. Dùng **Evaluate Expression** để thử giả thuyết.
5. Dùng **Frames** đi ngược lên để tìm nơi dữ liệu sai bắt đầu xuất hiện.
6. Sửa ở nguồn gây lỗi, không chỉ chỗ lỗi nổ ra.
