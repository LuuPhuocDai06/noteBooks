# Bài tập: Đọc Stack Trace

Làm từ cơ bản đến nâng cao. **Tự làm trước, rồi mới xem đáp án ở cuối file.**

---

## Cơ bản

### Bài 1
Cho stack trace:

```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 5 out of bounds for length 5
	at Calc.sum(Calc.java:9)
	at App.run(App.java:14)
	at App.main(App.java:5)
```

Trả lời:
1. Loại lỗi gì? Index nào, mảng dài bao nhiêu?
2. Lỗi xảy ra ở file nào, dòng nào?
3. Hàm nào gọi hàm gây lỗi?

### Bài 2
Cho stack trace:

```
Exception in thread "main" java.lang.NullPointerException: Cannot invoke "String.substring(int, int)" because "this.nickname" is null
	at Student.initial(Ex2.java:16)
	at Ex2.main(Ex2.java:6)
```

Trả lời:
1. Biến nào đang `null`?
2. Dòng nào của code mình cần mở ra xem đầu tiên?
3. Đề xuất hai cách sửa.

### Bài 3
Viết đoạn code sau rồi **đoán trước** stack trace sẽ có loại exception gì và message gì, sau đó chạy để kiểm tra:

```java
Object o = "abc";
Integer i = (Integer) o;
```

---

## Trung bình

### Bài 4
Cho stack trace:

```
Exception in thread "main" java.lang.IllegalArgumentException: Không tính được tổng
	at PriceService.total(Ex4.java:22)
	at OrderApp.run(Ex4.java:13)
	at Ex4.main(Ex4.java:6)
Caused by: java.lang.NumberFormatException: For input string: "x"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:662)
	at java.base/java.lang.Integer.parseInt(Integer.java:778)
	at PriceService.parse(Ex4.java:35)
	at PriceService.sum(Ex4.java:29)
	at PriceService.total(Ex4.java:20)
	... 2 more
```

Trả lời:
1. Root cause là gì? Giá trị đầu vào nào gây lỗi?
2. Dòng đầu tiên thuộc code của mình trong phần root cause là dòng nào?
3. Vẽ chuỗi gọi hàm từ `main` đến nơi lỗi xảy ra.
4. `... 2 more` nghĩa là gì?
5. Vì sao `PriceService.total` xuất hiện hai lần với số dòng khác nhau (20 và 22)?

### Bài 5
Viết chương trình có 3 tầng hàm `a() -> b() -> c()`. Hàm `c()` ném `NumberFormatException`. Hàm `b()` bắt rồi ném lại bọc trong `IllegalStateException`. Hàm `a()` bắt rồi ném lại bọc trong `RuntimeException`.

Trước khi chạy, hãy **viết ra giấy** stack trace sẽ có mấy khối `Caused by` và thứ tự của chúng. Sau đó chạy và so sánh.

---

## Nâng cao

### Bài 6
Cho code:

```java
static int factorial(int n) {
    return n * factorial(n - 1);   // dòng 7
}
```

và stack trace (rút gọn):

```
Exception in thread "main" java.lang.StackOverflowError
	at Ex6.factorial(Ex6.java:7)
	at Ex6.factorial(Ex6.java:7)
	at Ex6.factorial(Ex6.java:7)
	... (hàng nghìn dòng giống nhau)
	at Ex6.main(Ex6.java:3)
```

Trả lời:
1. Vì sao trace có hàng nghìn dòng giống nhau?
2. Nhìn trace, làm sao biết ngay hàm nào bị đệ quy vô hạn?
3. Sửa hàm cho đúng.

### Bài 7
Đây là stack trace mô phỏng từ một ứng dụng web Java (đã rút gọn). Có rất nhiều dòng của framework:

```
org.springframework.web.util.NestedServletException: Request processing failed; nested exception is java.lang.NullPointerException: Cannot invoke "com.techstore.model.Category.getName()" because the return value of "com.techstore.model.Product.getCategory()" is null
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:898)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:655)
	... 38 more
Caused by: java.lang.NullPointerException: Cannot invoke "com.techstore.model.Category.getName()" because the return value of "com.techstore.model.Product.getCategory()" is null
	at com.techstore.service.ProductService.toDto(ProductService.java:42)
	at com.techstore.service.ProductService.findById(ProductService.java:31)
	at com.techstore.controller.ProductController.get(ProductController.java:25)
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103)
	at org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:205)
	... 40 more
```

Trả lời:
1. Root cause là gì, nằm ở `Caused by` nào?
2. Lọc ra **chỉ các dòng thuộc code của dự án** (`com.techstore...`). Có mấy dòng, theo thứ tự nào?
3. Mở dòng nào đầu tiên? Giả thuyết của bạn về nguyên nhân dữ liệu sai là gì?
4. Nên sửa ở `toDto` (thêm kiểm tra null) hay ở chỗ tạo `Product`? Giải thích.

### Bài 8
Lấy một chương trình Java nhỏ bất kỳ của bạn. Cố ý làm hỏng nó theo **3 cách khác nhau** (ví dụ: null, index sai, parse sai). Với mỗi cách:
1. Viết ra dự đoán stack trace (loại lỗi, hàm, dòng) trước khi chạy.
2. Chạy và so sánh.
3. Ghi lại chỗ dự đoán sai và lý do.

---

## Đáp án

<details>
<summary>Bài 1</summary>

1. `ArrayIndexOutOfBoundsException`: truy cập index 5 trong mảng dài 5 (index hợp lệ là 0 đến 4).
2. `Calc.java`, dòng 9, trong hàm `sum`.
3. `App.run` (dòng 14) gọi `Calc.sum`; `App.main` (dòng 5) gọi `run`.
</details>

<details>
<summary>Bài 2</summary>

1. `nickname` của đối tượng `Student` (`this.nickname`).
2. `Ex2.java` dòng 16, trong hàm `Student.initial`.
3. Cách 1: khởi tạo `nickname` trong constructor (ví dụ gán bằng `name`). Cách 2: kiểm tra `nickname == null` trước khi gọi `substring` và trả về giá trị mặc định.
</details>

<details>
<summary>Bài 3</summary>

`ClassCastException`, message dạng `class java.lang.String cannot be cast to class java.lang.Integer ...`. Trace chỉ ra dòng ép kiểu trong code của bạn.
</details>

<details>
<summary>Bài 4</summary>

1. Root cause: `NumberFormatException`, chuỗi `"x"` không phải số.
2. `PriceService.parse(Ex4.java:35)`, dòng `Integer.parseInt(p)`.
3. `main` (6) -> `OrderApp.run` (13) -> `PriceService.total` (20) -> `PriceService.sum` (29) -> `PriceService.parse` (35) -> `Integer.parseInt` (JDK) -> lỗi.
4. Hai dòng cuối của khối `Caused by` (`OrderApp.run`, `Ex4.main`) trùng với exception ngoài đã in ở trên nên bị rút gọn.
5. Dòng 20 là nơi `total` gọi `sum` (lỗi gốc đi qua đây). Dòng 22 là nơi `total` ném `IllegalArgumentException` trong khối `catch`. Cùng một hàm nhưng hai vị trí khác nhau.
</details>

<details>
<summary>Bài 5</summary>

Có **2 khối `Caused by`**. Exception ngoài cùng là `RuntimeException` (từ `a`), bên trong là `IllegalStateException` (từ `b`), trong cùng là `NumberFormatException` (từ `c`). Root cause luôn là `Caused by` cuối cùng, tức `NumberFormatException`.
</details>

<details>
<summary>Bài 6</summary>

1. Mỗi lần gọi đệ quy thêm một frame vào stack. Không có điều kiện dừng nên stack đầy đến khi JVM ném `StackOverflowError`.
2. Dòng lặp đi lặp lại giống hệt nhau chính là hàm đệ quy: `Ex6.factorial(Ex6.java:7)`.
3. Thêm điều kiện dừng:
   ```java
   static int factorial(int n) {
       if (n <= 1) return 1;
       return n * factorial(n - 1);
   }
   ```
</details>

<details>
<summary>Bài 7</summary>

1. `NullPointerException` ở khối `Caused by`: `product.getCategory()` trả về `null` rồi gọi `.getName()`.
2. Ba dòng, từ trên xuống: `ProductService.toDto(ProductService.java:42)`, `ProductService.findById(ProductService.java:31)`, `ProductController.get(ProductController.java:25)`.
3. Mở `ProductService.java:42` đầu tiên. Giả thuyết: có sản phẩm trong dữ liệu chưa được gán danh mục (thiếu `category`).
4. Thêm kiểm tra null ở `toDto` chỉ che lỗi. Nếu nghiệp vụ quy định mọi sản phẩm phải có danh mục thì nên sửa ở nơi tạo/lưu `Product` (hoặc ràng buộc dữ liệu). Nếu danh mục là tùy chọn thì xử lý null ở `toDto` là đúng.
</details>

<details>
<summary>Bài 8</summary>

Không có đáp án cố định. Điểm cần kiểm tra: bạn dự đoán đúng loại exception, đúng hàm, đúng dòng chưa, và có xác định đúng dòng đầu tiên thuộc code của mình không.
</details>
