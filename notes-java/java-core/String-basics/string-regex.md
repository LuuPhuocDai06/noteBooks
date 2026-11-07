# Regex trong Java String: Lý thuyết

## 1. Khái niệm

**Regex (Regular Expression)** là một **mẫu (pattern)** dùng để mô tả hình dạng của chuỗi, rồi kiểm tra, tìm kiếm, thay thế hoặc cắt chuỗi theo mẫu đó.

Bốn cách dùng regex trong Java:

| Cách dùng | Mục đích |
|---|---|
| `str.matches(regex)` | Kiểm tra **cả chuỗi** có khớp mẫu không (trả về `boolean`) |
| `str.replaceAll(regex, thay)` / `replaceFirst` | Thay thế phần khớp mẫu |
| `str.split(regex)` | Cắt chuỗi theo mẫu |
| `Pattern` + `Matcher` | Tìm **từng đoạn khớp** trong chuỗi, lấy nhóm `()` |

### Bảng ký hiệu cần nhớ

| Ký hiệu | Ý nghĩa |
|---|---|
| `.` | Một ký tự bất kỳ |
| `\d` / `\D` | Một chữ số / không phải chữ số |
| `\w` / `\W` | Chữ, số hoặc `_` / ngược lại |
| `\s` / `\S` | Khoảng trắng / không phải khoảng trắng |
| `[abc]` | Một trong các ký tự a, b, c |
| `[a-z]`, `[0-9]` | Một ký tự trong khoảng |
| `[^abc]` | Một ký tự **không** thuộc a, b, c |
| `*` | 0 lần hoặc nhiều lần |
| `+` | 1 lần hoặc nhiều lần |
| `?` | 0 hoặc 1 lần |
| `{n}`, `{n,m}` | Đúng n lần, từ n đến m lần |
| `^` / `$` | Đầu / cuối chuỗi |
| `\|` | Hoặc |
| `( )` | Nhóm |

> **Lưu ý quan trọng:** trong Java, dấu `\` trong chuỗi phải viết thành `\\`.
> Ví dụ regex `\d+` viết là `"\\d+"`.

---

## 2. Ví dụ (demo)

```java
import java.util.regex.*;

public class RegexDemo {
    public static void main(String[] args) {

        // 1. matches: kiểm tra cả chuỗi
        System.out.println("12345".matches("\\d+"));        // true
        System.out.println("12a45".matches("\\d+"));        // false

        // 2. Số điện thoại VN: bắt đầu bằng 0, theo sau 9 chữ số
        System.out.println("0901234567".matches("0\\d{9}")); // true

        // 3. replaceAll: xóa mọi khoảng trắng thừa
        String s = "Java    la   ngon   ngu   hay";
        System.out.println(s.replaceAll("\\s+", " "));       // Java la ngon ngu hay

        // 4. split: cắt theo dấu phẩy hoặc chấm phẩy
        String[] parts = "a,b;c,d".split("[,;]");
        System.out.println(parts.length);                    // 4

        // 5. Pattern + Matcher: tìm tất cả số trong chuỗi
        Matcher m = Pattern.compile("\\d+").matcher("Co 12 tao, 7 cam va 150 nho");
        while (m.find()) {
            System.out.println(m.group());                   // 12, 7, 150
        }

        // 6. Nhóm (): tách ngày/tháng/năm
        Matcher d = Pattern.compile("(\\d{2})/(\\d{2})/(\\d{4})").matcher("31/10/2026");
        if (d.matches()) {
            System.out.println(d.group(1)); // 31
            System.out.println(d.group(2)); // 10
            System.out.println(d.group(3)); // 2026
        }
    }
}
```

---

## 3. Giải thích dễ hiểu nhất

### Hình dung regex như một "khuôn bánh"

Chuỗi là cục bột, regex là cái khuôn. Cục bột nào vừa khuôn thì `matches` trả về `true`.

### Cách đọc regex: chia thành từng mảnh nhỏ, đọc từ trái sang phải

**Ví dụ `0\d{9}` (số điện thoại):**
- `0` → ký tự đầu phải là chữ số `0`
- `\d` → tiếp theo là một chữ số bất kỳ
- `{9}` → chữ số đó lặp đúng 9 lần

**Ví dụ email đơn giản `\w+@\w+\.com`:**
- `\w+` → có ít nhất 1 chữ/số (phần tên)
- `@` → dấu @
- `\w+` → có ít nhất 1 chữ/số (phần domain)
- `\.` → dấu chấm thật (vì `.` một mình nghĩa là "ký tự bất kỳ", nên muốn chấm thật phải thêm `\`)
- `com` → đúng chữ com

### Ba "thợ" cần phân biệt

- `*`, `+`, `?`, `{}` là các **bộ đếm**, luôn đứng sau thứ cần đếm.
  - `a*` có 0 hoặc nhiều chữ a
  - `a+` có ít nhất 1 chữ a
  - `a?` có hoặc không có chữ a
- `[ ]` là **"chọn 1 trong"**: `[aeiou]` nghĩa là một nguyên âm.
- `( )` là **"gói lại"** để đếm cả nhóm hoặc lấy ra sau này: `(ab)+` khớp `ab`, `abab`, ...

### Hai lỗi người mới hay gặp

1. Quên `\\` trong Java (viết `"\d"` sẽ lỗi biên dịch).
2. `matches` yêu cầu khớp **toàn bộ** chuỗi, không phải chỉ một phần.
   `"abc123".matches("\\d+")` là `false`, vì có `abc` ở đầu.
   Muốn tìm một phần thì dùng `Matcher.find()`.

---

## 4. Bổ sung: `Pattern`, `Matcher`, `find`, `matches` và nhóm

### Quy trình 3 bước

```java
Pattern p = Pattern.compile("Java \\d+");   // 1. biên dịch regex (làm 1 lần, dùng lại được)
Matcher m = p.matcher("Java 17 va Java 21"); // 2. gắn regex vào chuỗi cần xét
while (m.find()) {                            // 3. tìm từng đoạn khớp
    System.out.println(m.group());
}
```

### Các hàm của `Matcher`

| Hàm | Ý nghĩa |
|---|---|
| `matches()` | Khớp **toàn bộ** chuỗi |
| `find()` | Tìm đoạn khớp **tiếp theo**, trả `true` nếu có. Gọi lặp trong `while` để lấy hết |
| `lookingAt()` | Khớp **từ đầu chuỗi**, không cần khớp hết đến cuối |
| `group()` hoặc `group(0)` | Toàn bộ đoạn vừa khớp |
| `group(n)` | Nội dung của nhóm `( )` thứ n |
| `group("ten")` | Nội dung của nhóm có tên `(?<ten>...)` |
| `groupCount()` | Số nhóm `( )` **có trong regex** (không phụ thuộc chuỗi) |
| `start()` / `end()` | Vị trí bắt đầu / vị trí **ngay sau** ký tự cuối của đoạn khớp |
| `reset()` | Đưa Matcher về đầu chuỗi để tìm lại |

### Demo (đã chạy thử)

```java
String text = "Java 17 va Java 21";
Pattern p = Pattern.compile("Java \\d+");

System.out.println(p.matcher(text).matches());    // false: cả chuỗi không chỉ là "Java <số>"
System.out.println(p.matcher(text).lookingAt());  // true: đầu chuỗi khớp "Java 17"

Matcher m = p.matcher(text);
while (m.find()) {
    System.out.println(m.group() + " [" + m.start() + "," + m.end() + ")");
}
// Java 17 [0,7)
// Java 21 [11,18)

Matcher d = Pattern.compile("(\\d{2})/(\\d{2})/(\\d{4})").matcher("31/10/2026");
System.out.println(d.groupCount());               // 3
if (d.matches()) {
    System.out.println(d.group(0));               // 31/10/2026 (toàn bộ)
    System.out.println(d.group(1));               // 31
    System.out.println(d.group(3));               // 2026
}

// Dùng nhóm trong replaceAll bằng $1, $2, $3
System.out.println("31/10/2026".replaceAll("(\\d{2})/(\\d{2})/(\\d{4})", "$3-$2-$1"));
// 2026-10-31
```

### Giải thích dễ hiểu

- **`matches` và `find` khác nhau thế nào?** `matches` hỏi "**cả chuỗi** có đúng hình dạng này không?" (dùng để **kiểm tra**). `find` hỏi "**trong chuỗi** có đoạn nào đúng hình dạng này không?" rồi đứng lại ở đoạn đó, gọi lần nữa thì tìm đoạn kế tiếp (dùng để **tìm và lấy**).
- **`group` dùng được với cả `matches` và `find`**, miễn là lần gọi gần nhất trả về `true`.
- **Đánh số nhóm:** đếm theo dấu `(` mở, từ trái sang phải. Ví dụ `((\d{2})/(\d{2}))`: nhóm 1 là cả `dd/MM`, nhóm 2 là `dd`, nhóm 3 là `MM`.
- **Nhóm không bắt `(?:...)`:** dùng để "gói lại" mà **không** tính vào số thứ tự nhóm. Ví dụ `(?:\d{2})/(\d{2})/(\d{4})` chỉ có 2 nhóm.
- **Nhóm có tên `(?<ten>...)`:** gọi bằng `group("ten")`, không lo số thứ tự bị lệch khi sửa regex.

### Lỗi hay gặp

1. Gọi `group()` khi chưa `find()`/`matches()` hoặc khi chúng đã trả `false` thì ném `IllegalStateException: No match found`.
2. Nhầm `groupCount()` là "số lần khớp". Thật ra nó chỉ đếm số cặp `( )` trong regex.
3. Gọi `str.matches(regex)` trong vòng lặp lớn: mỗi lần Java biên dịch lại regex. Nên `Pattern.compile` một lần rồi dùng lại.
