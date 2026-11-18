# Khái niệm OOP cơ bản trong Java

## 1. Class (lớp)

- Là **bản thiết kế (khuôn mẫu)** mô tả một loại đối tượng.
- Gồm hai phần chính:
  - **Thuộc tính (field):** dữ liệu của đối tượng (ví dụ: tên, tuổi, số dư).
  - **Phương thức (method):** hành vi của đối tượng (ví dụ: gửi tiền, rút tiền).
- Bản thân class chỉ là mô tả, chưa chiếm dữ liệu thật cho đến khi tạo object.
- Ví dụ đời thường: bản vẽ thiết kế một chiếc xe.

## 2. Object (đối tượng)

- Là **thực thể cụ thể** được tạo ra từ class bằng từ khóa `new`.
- Từ một class có thể tạo **nhiều object**, mỗi object có **dữ liệu riêng**, độc lập với nhau.
- Việc tạo object còn gọi là **khởi tạo (instantiate)**.
- Ví dụ đời thường: chiếc xe Honda màu đỏ và chiếc xe Toyota màu trắng, cùng được làm từ một bản vẽ.

## 3. Constructor (hàm khởi tạo)

- Là phương thức đặc biệt, **tự động chạy khi dùng `new`**, dùng để gán giá trị ban đầu cho object.
- Đặc điểm:
  - **Tên trùng với tên class.**
  - **Không có kiểu trả về** (kể cả `void`).
  - Có thể có **nhiều constructor** với danh sách tham số khác nhau (overloading).
- Nếu không tự viết, Java tạo sẵn một **constructor mặc định** (không tham số). Nhưng nếu đã tự viết bất kỳ constructor nào thì Java không tạo constructor mặc định nữa.
- Từ khóa `this` chỉ chính object hiện tại, giúp phân biệt thuộc tính với tham số trùng tên.
- Ví dụ đời thường: bước lắp ráp và cài đặt thông số ban đầu khi xe vừa xuất xưởng.

## 4. Encapsulation (đóng gói)

- Là việc **giấu dữ liệu bên trong class** và chỉ cho truy cập qua các phương thức công khai.
- Cách thực hiện:
  - Khai báo thuộc tính là `private`.
  - Cung cấp **getter** (đọc) và **setter** (ghi) là `public` khi cần.
- Lợi ích:
  - **Bảo vệ dữ liệu**, tránh bị sửa sai từ bên ngoài.
  - **Kiểm tra tính hợp lệ** trước khi thay đổi (ví dụ: số dư không được âm, tuổi từ 1 đến 100).
  - **Dễ bảo trì:** có thể sửa cách xử lý bên trong mà không ảnh hưởng code bên ngoài.
- Ví dụ đời thường: máy ATM. Bạn không thể thò tay vào két sắt, chỉ thao tác qua các nút bấm, và máy kiểm tra số dư trước khi cho rút tiền.

## Tóm tắt nhanh

| Khái niệm | Là gì | Ví dụ đời thường |
|---|---|---|
| Class | Bản thiết kế, mô tả thuộc tính và hành vi | Bản vẽ chiếc xe |
| Object | Thực thể cụ thể tạo từ class bằng `new` | Chiếc xe Honda đỏ |
| Constructor | Hàm tự chạy khi `new`, gán giá trị ban đầu | Lắp ráp, cài đặt ban đầu |
| Encapsulation | Giấu dữ liệu (`private`), truy cập qua getter/setter | Máy ATM |
