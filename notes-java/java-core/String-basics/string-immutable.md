# Immutable trong Java (String và class immutable)

## 1. Khái niệm

Immutable (bất biến) nghĩa là một đối tượng mà **trạng thái không thể thay đổi sau khi được tạo ra**. Muốn "thay đổi" thì phải tạo đối tượng mới.

`String` là ví dụ có sẵn của Java. Class tự viết cũng có thể làm immutable.

## 2. Năm quy tắc viết class immutable

1. **Khai báo class là `final`**: không cho lớp con kế thừa rồi override để phá tính bất biến (ví dụ override getter hoặc thêm field mutable).
2. **Mọi field đều `private final`**: không truy cập trực tiếp từ bên ngoài, chỉ gán một lần trong constructor.
3. **Không có setter**: không có method nào sửa trạng thái.
4. **Method "thay đổi" trả về đối tượng mới**: người gọi phải gán lại kết quả.
5. **Copy phòng thủ (defensive copy) với field mutable**: nếu có field kiểu mutable (`Date`, mảng, `List` thường...) thì copy khi nhận vào constructor và copy khi trả ra ở getter. Nếu đã dùng `List.copyOf(...)` trong constructor thì list đã bất biến, getter trả thẳng được.

## 3. Một vài ví dụ nhỏ liên quan

**Quên gán lại kết quả**

```java
String a = "java";
a.toUpperCase();          // vô tác dụng, kết quả bị bỏ đi
a = a.toUpperCase();      // đúng: gán biến sang object mới
```

**Gán lại tham số không ảnh hưởng bên ngoài (Java là pass-by-value)**

```java
static void change(String s) { s = s + "!"; }   // chỉ đổi s cục bộ
static String change2(String s) { return s + "!"; }

x = change2(x);   // cách đúng: trả về rồi gán lại
```

**String pool, `==` và `equals`**

```java
String s1 = "abc", s2 = "abc";
String s3 = new String("abc");
s1 == s2;          // true  (cùng object trong pool)
s1 == s3;          // false (s3 là object mới)
s1.equals(s3);     // true  (so nội dung)
s3.intern() == s1; // true  (intern trả về object trong pool)
```

**Bất biến nông và sâu**

`List.copyOf` làm list không thêm/xóa được, nhưng nếu phần tử bên trong là object mutable thì vẫn sửa được. Muốn bất biến thật thì phần tử cũng phải immutable. `record` cũng chỉ bất biến nông.

## 4. Ưu điểm và nhược điểm

**Ưu điểm**
- Thread-safe, không cần `synchronized`.
- An toàn làm key `HashMap` (hash không đổi).
- Chia sẻ object tự do, không sợ bị sửa nhầm (nền tảng của String pool).
- Dễ suy luận và debug, không có thay đổi ngầm.
- Dễ hoàn tác (undo) vì giữ được các phiên bản cũ.

**Nhược điểm**
- Mỗi thay đổi tạo object mới, tốn bộ nhớ và thời gian khi làm nhiều lần (ví dụ `+=` trong vòng lặp lớn).
- Cách khắc phục: dùng builder (như `StringBuilder`) để lắp ráp rồi tạo object bất biến cuối cùng.
