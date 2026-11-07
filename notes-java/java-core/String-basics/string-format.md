# String format trong Java: Lý thuyết

## 1. Khái niệm

`String.format` dựng một chuỗi từ **mẫu (template)** và **các giá trị**. Chỗ nào cần chèn giá trị thì đặt một *format specifier* bắt đầu bằng `%`.

```
%[argument_index$][flags][width][.precision]conversion
```

Ba cách gọi cho cùng một kết quả:

```java
String.format("Xin chào %s", name);        // trả về String
System.out.printf("Xin chào %s%n", name);  // in thẳng ra console
"Xin chào %s".formatted(name);             // Java 15+, gọi như method của String
```

## 2. Conversion (kiểu dữ liệu)

| Specifier | Ý nghĩa | Ví dụ |
|---|---|---|
| `%s` | String (object thì gọi `toString()`) | `An` |
| `%d` | Số nguyên (int, long, BigInteger) | `42` |
| `%f` | Số thực (float, double, BigDecimal) | `3.140000` |
| `%c` | Một ký tự | `A` |
| `%b` | boolean | `true` |
| `%x` / `%X` | Hex | `ff` / `FF` |
| `%e` | Ký hiệu khoa học | `1.5e+03` |
| `%n` | Xuống dòng theo hệ điều hành (nên dùng thay `\n`) | |
| `%%` | In ký tự `%` | `%` |

## 3. Width, flags, precision

| Thành phần | Ý nghĩa | Ví dụ → Kết quả |
|---|---|---|
| width | Độ rộng tối thiểu, mặc định canh phải | `%5d` → `"   42"` |
| `-` | Canh trái | `%-5d` → `"42   "` |
| `0` | Đệm số 0 | `%05d` → `"00042"` |
| `+` | Luôn hiện dấu | `%+d` → `+42` |
| `,` | Phân cách hàng nghìn | `%,d` → `1,234,567` |
| `(` | Số âm trong ngoặc | `%(d` với -5 → `(5)` |
| `.n` với `%f` | Số chữ số thập phân (làm tròn HALF_UP) | `%.2f` với 3.456 → `3.46` |
| `.n` với `%s` | Cắt chuỗi còn n ký tự | `%.3s` với "Java" → `Jav` |
| `n$` | Chọn tham số theo vị trí, dùng lại được | `%2$s %1$s` |

## 4. Demo

```java
import java.util.Locale;

public class FormatDemo {
    public static void main(String[] args) {
        // Cơ bản
        String name = "An";
        int age = 20;
        double gpa = 3.456;
        System.out.println(String.format("Tên: %s, tuổi: %d, GPA: %.2f", name, age, gpa));
        // Tên: An, tuổi: 20, GPA: 3.46

        // Width + canh lề: in bảng
        System.out.printf("%-10s|%6s|%8s%n", "Tên", "Tuổi", "Lương");
        System.out.printf("%-10s|%6d|%8.1f%n", "Bình", 25, 1500.5);
        System.out.printf("%-10s|%6d|%8.1f%n", "Chi", 31, 12000.0);

        // Đệm số 0 và phân cách hàng nghìn
        System.out.println(String.format("ORD-%06d", 42));         // ORD-000042
        System.out.println(String.format("%,d VND", 1_250_000));   // 1,250,000 VND

        // Dùng lại tham số theo vị trí
        System.out.println(String.format("%1$s gặp %2$s. %2$s chào %1$s.", "An", "Bình"));

        // Hex, boolean, phần trăm
        System.out.println(String.format("%x | %b | %d%%", 255, true, 80)); // ff | true | 80%

        // Locale ảnh hưởng dấu thập phân / phân cách nghìn
        System.out.println(String.format(Locale.US, "%,.2f", 1234.5));      // 1,234.50
        System.out.println(String.format(Locale.GERMANY, "%,.2f", 1234.5)); // 1.234,50

        // formatted() + text block
        String json = """
                {"name": "%s", "age": %d}
                """.formatted("An", 20);
        System.out.print(json);
    }
}
```

## 5. Các lỗi hay gặp

```java
String.format("%d", 3.5);     // IllegalFormatConversionException: %d không nhận double
String.format("%.2f", 5);     // IllegalFormatConversionException: %f không nhận int
String.format("%s %s", "a");  // MissingFormatArgumentException: thiếu tham số
String.format("100%");        // UnknownFormatConversionException: phải viết 100%%
```

Quy tắc nhớ: **kiểu của specifier phải khớp kiểu của giá trị** (`%d` cho số nguyên, `%f` cho số thực), và **số specifier phải khớp số tham số**.

## 6. Lưu ý cho backend

- **Locale:** `String.format` mặc định dùng `Locale` của máy. Trên server, nếu cần kết quả cố định (JSON, log, CSV) thì truyền `Locale.US` hoặc `Locale.ROOT`, nếu không `%.2f` có thể ra `3,14`.
- **Tiền tệ:** dùng `BigDecimal` với `%.2f`, đừng dùng `double`.
- **Hiệu năng:** `String.format` chậm hơn `+` hoặc `StringBuilder`, không nên gọi trong vòng lặp nóng.
- **Logging SLF4J** dùng `{}` chứ không phải `%s`: `log.info("User {} login", id);`
- **Không ghép SQL bằng `String.format`:** dễ bị SQL injection, hãy dùng `PreparedStatement`.
- `MessageFormat` (`{0}`, `{1}`) là cách format khác, hay gặp ở i18n. Biết tên là đủ.

## 7. Tóm tắt nhanh

- `%s` chuỗi, `%d` nguyên, `%.2f` thực 2 số lẻ, `%n` xuống dòng, `%%` ký tự `%`.
- Canh lề: width + `-` (trái). Đệm số 0: `0`. Hàng nghìn: `,`.
- `printf` in ra, `String.format` / `formatted` trả về chuỗi.
- Nhớ Locale khi dữ liệu đi ra ngoài (file, API).
