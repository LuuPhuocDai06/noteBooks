	String-immutable
- khái niệm: String immutable(bất biến) nghĩa là một đối tượng mà trạng thái của nó không thể thay đổi sau khi được tạo ra.

- Các tính chất:
1. Khai báo class phải là final không cho lớp con kế thừa rồi override để phá vỡ.
2. Mọi field đều phải là private final: không truy cập trực tiếp từ bên ngoài chỉ gán một lần trong constructor.
3. Không có setter: không có method nào được sửa trạng thái.
4. Method thay đổi phải trả về đối tượng mới.
5. Defensive copy với field mutable: nếu có field kiểu mutable thì phải copy khi nhận constructor và copy khi trả getter.
