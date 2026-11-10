# Khái niệm: Đọc Stack Trace trong Java

## 1. Stack trace là gì?

Khi chương trình ném exception mà không ai bắt, JVM in ra **chuỗi các lời gọi hàm** dẫn đến lỗi. Chuỗi này gọi là stack trace (dấu vết ngăn xếp lời gọi).

```
Exception in thread "main" java.lang.NullPointerException: Cannot read field "city" because "c.address" is null
	at OrderService.customerCity(Main.java:18)
	at Main.main(Main.java:6)
```

## 2. Cấu trúc một stack trace

| Phần | Ví dụ | Cho biết |
|---|---|---|
| Dòng đầu | `NullPointerException: Cannot read field "city"...` | Loại exception và message: **lỗi gì** |
| Dòng `at ...` trên cùng | `at OrderService.customerCity(Main.java:18)` | **Nơi lỗi xảy ra**: hàm, file, số dòng |
| Các dòng `at ...` bên dưới | `at Main.main(Main.java:6)` | **Ai đã gọi** hàm ở trên, tiếp tục đến `main` |

## 3. Cách đọc từng bước

1. **Đọc dòng đầu**: xác định loại exception và message.
2. **Đọc các dòng `at` từ trên xuống**: dòng trên cùng là nơi lỗi xảy ra, các dòng dưới là chuỗi hàm đã gọi tới nó.
3. **Tìm dòng đầu tiên thuộc code của bạn**: bỏ qua các dòng `java.base/...` hoặc thư viện, đó thường là nơi cần xem trước.
4. **Gặp `Caused by:`**: đây là lỗi gốc bị bọc lại. Với stack trace dài, **`Caused by` cuối cùng mới là root cause**, hãy đọc nó đầu tiên.
5. **Gặp `... 23 more`**: các dòng trùng với phần phía trên đã bị rút gọn, không cần quan tâm.

### Ví dụ có `Caused by`

```
Exception in thread "main" java.lang.IllegalStateException: Config sai
	at ConfigLoader.load(ConfigLoader.java:12)
	at App.main(App.java:5)
Caused by: java.lang.NumberFormatException: For input string: "12a"
	at java.base/java.lang.NumberFormatException.forInputString(NumberFormatException.java:67)
	at java.base/java.lang.Integer.parseInt(Integer.java:661)
	at ConfigLoader.load(ConfigLoader.java:10)
	... 1 more
```

Root cause là `NumberFormatException` (chuỗi `"12a"` không phải số), xảy ra ở `ConfigLoader.java:10`.

## 4. Các exception hay gặp

| Exception | Nguyên nhân thường gặp |
|---|---|
| `NullPointerException` | Dùng biến/field đang `null` |
| `ArrayIndexOutOfBoundsException` | Index < 0 hoặc >= length |
| `ClassCastException` | Ép kiểu sai |
| `NumberFormatException` | Parse chuỗi không phải số |
| `StackOverflowError` | Đệ quy không có điểm dừng |

## 5. Mẹo trong IntelliJ

- Khi chạy bằng Run, **click vào link xanh** trong console để nhảy thẳng tới dòng lỗi.
- Dán stack trace vào **Code → Analyze Stack Trace or Thread Dump**, mỗi dòng `at` sẽ thành link tới code.
