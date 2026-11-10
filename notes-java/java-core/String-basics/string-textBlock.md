# Text Block trong Java - Khái niệm

## 1. Text block là gì?

Text block (Java 15+) là chuỗi nhiều dòng viết trong cặp `"""`. Nó giúp bạn không phải nối chuỗi bằng `+` hay chèn `\n` và `\"` thủ công. Thường dùng khi viết JSON, SQL, HTML, XML ngay trong code.

Kết quả vẫn là một `String` bình thường, không có kiểu dữ liệu mới.

## 2. Quy tắc cơ bản

- Dấu mở `"""` phải xuống dòng ngay sau nó, không được viết nội dung cùng dòng.
- Có thể dùng dấu `"` bên trong mà không cần escape (trừ khi gặp 3 dấu liên tiếp).
- Khoảng trắng thụt đầu dòng do căn code (incidental whitespace) được trình biên dịch tự loại bỏ. Mức thụt được tính theo dòng ít thụt nhất, kể cả dòng chứa `"""` đóng.
- Khoảng trắng ở cuối mỗi dòng bị xóa tự động.
- Vị trí dấu đóng quyết định kết quả: nếu `"""` nằm ở dòng riêng thì chuỗi kết thúc bằng `\n`; nếu nằm cuối dòng nội dung thì không có `\n` cuối.

## 3. Escape đặc biệt

| Escape | Ý nghĩa |
|---|---|
| `\<xuống dòng>` | Nối dòng, không chèn `\n` |
| `\s` | Giữ lại một dấu cách (chống bị xóa ở cuối dòng) |
| `\n`, `\t`, `\"` | Vẫn dùng được như bình thường |

## 4. Phương thức liên quan

- `formatted(...)`: giống `String.format`, dùng để chèn giá trị vào text block.
- `stripIndent()`: loại bỏ khoảng trắng thụt đầu dòng theo quy tắc của text block.
- `translateEscapes()`: chuyển các chuỗi escape như `\n`, `\t` thành ký tự thật.
