# Java OOP 03 – Interface vs Abstract Class, Default Method
---

## ĐẶT VẤN ĐỀ: vì sao cần interface và abstract class?

bạn đã biết kế thừa (`extends`). Nhưng Java chỉ cho một class `extends` đúng **một** class cha, nên nếu chỉ dùng kế thừa thì ta gặp hai khó khăn:

- Không thể nói "class này **làm được** việc X" cho những class không cùng một cây kế thừa.
- Không thể **ép** các class con phải có một method nào đó mà vẫn để mỗi class tự viết cách làm riêng.

**Ví dụ dễ hiểu.** `Student` (sinh viên) và `Invoice` (hóa đơn) chẳng liên quan gì nhau, nhưng cả hai đều "in ra được". Không thể cho hai class này `extends` chung một class cha chỉ vì chuyện "in ra được". Cần một cách khác để gắn khả năng "in ra được" cho cả hai.

Hai cách giải quyết là **interface** và **abstract class**, mỗi cái hợp với một kiểu tình huống. Phần 1 bên dưới sẽ lần lượt đi vào từng khái niệm.

---

## PHẦN 1 – KHÁI NIỆM

### 1.1. Abstract class (lớp trừu tượng)

**Khái niệm.** Abstract class là một class **không được tạo đối tượng trực tiếp** (không `new` được), dùng làm khuôn chung cho các class con cùng loại. Nó có thể chứa dữ liệu (field), constructor, method đã viết sẵn, và method `abstract` (chỉ khai báo, không có thân) mà class con **bắt buộc** phải viết. Quan hệ của nó là **is-a** ("là một loại gì").

**Ví dụ dễ hiểu.** Ngoài đời không có con vật nào chỉ là "một con động vật" chung chung, nó luôn là chó, mèo, hay chim. Nhưng mọi con vật đều có tên và đều phải "phát ra tiếng kêu" (mỗi loài một kiểu). Ta mô tả như sau:

```java
public abstract class Person {
    private final String name;                 // dữ liệu chung: có field

    protected Person(String name) {            // constructor chung: class con gọi qua super(...)
        this.name = name;
    }

    public String getName() { return name; }   // method viết sẵn, class con dùng luôn

    public abstract String getRole();          // method trừu tượng: class con BẮT BUỘC viết
}

public class Student extends Person {
    public Student(String name) { super(name); }

    @Override
    public String getRole() { return "Sinh viên"; }
}
```

`new Person("An")` sẽ báo lỗi biên dịch, còn `new Student("An")` thì chạy bình thường.

**Đặc điểm cần nhớ:**

- Có **field instance**, **constructor**, method thường, method `abstract`; được dùng `protected`/`private`.
- Class con dùng `extends` → **chỉ kế thừa được 1 abstract class**.

---

### 1.2. Interface (giao diện / hợp đồng)

**Khái niệm.** Interface là một **hợp đồng** liệt kê các việc mà một class phải làm được. Class nào ghi `implements` interface đó thì **bắt buộc** viết đủ các method trong hợp đồng. Một class có thể `implements` **nhiều** interface. Quan hệ của nó là **can-do** ("làm được gì"). Interface **không có constructor** và **không có field instance** (field trong interface luôn là hằng `public static final`).

**Ví dụ dễ hiểu.** Giống ổ cắm điện: ổ cắm chỉ quy định "thiết bị nào muốn dùng điện phải có phích cắm đúng chuẩn". Máy sấy tóc, sạc điện thoại, tivi chẳng liên quan gì nhau, nhưng đều "cắm điện được" vì cùng đáp ứng chuẩn đó. Trong code:

```java
public interface Describable {
    String describe();      // mặc định là public abstract
}

public class Student implements Describable {
    @Override
    public String describe() {      // phải ghi public khi implement
        return "Sinh viên";
    }
}

public class Invoice implements Describable {   // Invoice không liên quan Student, vẫn implements được
    @Override
    public String describe() {
        return "Hóa đơn";
    }
}

// Dùng chung được cho cả hai:
Describable d = new Student();
System.out.println(d.describe());
```

Một class implements nhiều interface cũng được: `class Teacher implements Describable, Payable`.

---

### 1.3. Default method (Java 8+)

**Khái niệm.** Default method là method **có thân** nằm ngay trong interface, đánh dấu bằng từ khóa `default`. Class implements **không bắt buộc** viết lại (muốn thì vẫn override được). Nó ra đời để **thêm method mới vào interface cũ mà không làm vỡ** các class đã implement trước đó.

**Ví dụ dễ hiểu.** Hãy tưởng tượng interface `List` của Java đã có hàng triệu class implements. Khi Java 8 muốn thêm `sort()` vào `List`, nếu `sort()` là method abstract thì cả triệu class đó đồng loạt báo lỗi. Nhờ `default`, `sort()` có sẵn thân, các class cũ không phải sửa gì mà vẫn dùng được `sort()`. Cách này áp dụng cho `List.sort()`, `Collection.stream()`, `Iterable.forEach()`. Trong code của mình:

```java
public interface Describable {
    String describe();

    default String shortInfo() {                 // class implements KHÔNG bắt buộc viết lại
        String s = describe();
        return s.length() <= 20 ? s : s.substring(0, 20) + "...";
    }
}
```

`Student` implements `Describable` chỉ cần viết `describe()` là đã có luôn `shortInfo()`.

**Hai loại method có thân khác trong interface:**

**Static method** là method gắn với chính interface, gọi bằng tên interface (không gọi qua đối tượng, và không "thừa kế" xuống class con):

```java
public interface Describable {
    String describe();
    static void printAll(List<? extends Describable> list) {
        for (Describable d : list) System.out.println(d.describe());
    }
}
// Gọi: Describable.printAll(list);
```

**Private method** (Java 9+) là method chỉ để các default method trong cùng interface dùng chung code, không lộ ra ngoài.

#### Xung đột default (diamond problem)

**Khái niệm.** Khi một class implements **hai interface có cùng default method**, Java không biết chọn bản nào nên **bắt buộc** bạn override và tự quyết định. Tình huống này gọi là diamond problem (vấn đề hình thoi). Muốn gọi lại bản của từng interface thì dùng cú pháp `Tên_interface.super.method()`.

**Ví dụ dễ hiểu.** Giống hai người cùng đưa bạn hai lời dặn trái nhau cho cùng một việc: bạn phải tự quyết định nghe ai (hoặc nghe cả hai).

```java
interface A { default String info() { return "A"; } }
interface B { default String info() { return "B"; } }

class C implements A, B {
    @Override
    public String info() {
        return A.super.info() + "+" + B.super.info();   // tự giải quyết: "A+B"
    }
}
```

**Quy tắc ưu tiên:** nếu trùng tên method thì **class (kể cả class cha) thắng interface**; nếu hai interface ngang hàng thì bạn phải tự giải quyết như trên.

---

### 1.4. Template method (khung thuật toán)

**Khái niệm.** Template method là cách viết trong abstract class một method **`final`** quy định **thứ tự các bước** của một quy trình, trong đó có những bước để `abstract` cho class con tự điền. Nhờ vậy khung quy trình luôn giữ nguyên, còn chi tiết từng bước thì mỗi class con tự quyết.

**Ví dụ dễ hiểu.** Quy trình pha đồ uống luôn là: đun nước → pha chế → rót ra ly → thêm đường/đá. Mọi đồ uống đều theo thứ tự đó, nhưng bước "pha chế" thì trà pha khác cà phê. Trong code, `makeDrink()` là khung cố định (`final`), còn `brew()` để class con viết:

```java
abstract class Drink {
    public final void makeDrink() {      // khung cố định, class con không được sửa thứ tự
        boilWater();
        brew();                          // bước này mỗi loại một kiểu
        pour();
    }
    private void boilWater() { System.out.println("Đun nước"); }
    protected abstract void brew();
    private void pour() { System.out.println("Rót ra ly"); }
}

class Tea extends Drink {
    @Override protected void brew() { System.out.println("Ngâm trà"); }
}
```

---

### 1.5. Dependency Injection (tiêm phụ thuộc) – cái nền của Spring

**Khái niệm.** Dependency Injection (DI) là kỹ thuật **truyền đối tượng mà một class cần dùng từ bên ngoài vào** (thường qua constructor), thay vì để class đó tự `new` bên trong. Khi class chỉ biết đối tượng đó qua **interface**, ta thay đổi hoặc thay bằng bản giả (để test) mà không phải sửa class.

**Ví dụ dễ hiểu.** Một chiếc đèn bàn chỉ cần "có điện". Nếu đèn tự gắn cứng một cục pin bên trong (tự `new`), muốn đổi sang nguồn khác phải mổ đèn ra. Nếu đèn có ổ cắm chuẩn (interface), bạn cắm pin, cắm điện lưới, hay cắm nguồn thử nghiệm đều được:

```java
interface PowerSource { int volt(); }

class Lamp {
    private final PowerSource source;               // chỉ biết interface
    Lamp(PowerSource source) { this.source = source; }   // nguồn được "tiêm" từ bên ngoài
    boolean turnOn() { return source.volt() >= 5; }
}
```

Trong Spring: `StudentService` nhận `StudentRepository` (interface) qua constructor. Đây chính là lý do gần như mọi tầng trong Spring đều viết dạng **interface + implementation**.

---

### 1.6. Bảng so sánh

| Tiêu chí | Interface | Abstract class |
|---|---|---|
| Quan hệ | can-do (làm được gì) | is-a (là loại gì) |
| Số lượng kế thừa | `implements` **nhiều** | `extends` **1** |
| Field instance (state) | Không (chỉ hằng `static final`) | Có |
| Constructor | Không | Có |
| Method có thân | `default`, `static`, `private` | Mọi loại method thường |
| Modifier method | mặc định `public` | `public/protected/private/package` |
| Dùng khi | Định nghĩa hợp đồng, nhiều class không liên quan cùng làm được 1 việc | Nhiều class con **cùng loại**, chia sẻ state + code chung |

---

### 1.7. Khi nào dùng cái nào (quy tắc ra quyết định)

1. **Mặc định chọn interface.** Nó linh hoạt: class implements được nhiều interface, dễ thay thế, dễ test.
2. Chọn **abstract class** khi cần ít nhất một trong:
   - **State chung** (field mà mọi con đều có, như `name`, `id`) kèm constructor để ép validate.
   - **Code dùng chung nhiều** + helper `protected` cho class con.
   - **Template method** (xem mục 1.4).
3. Cần **cả hai** → kết hợp: interface định nghĩa hợp đồng, abstract class cài sẵn phần chung (gọi là *skeletal implementation*). Ví dụ trong JDK: `List` (interface) + `AbstractList` (abstract class).
4. Ba câu tự hỏi nhanh:
   - "Các class này **có chung dữ liệu và hành vi nền** không?" → abstract class.
   - "Các class này chỉ **cùng biết làm 1 việc**, còn lại khác nhau hoàn toàn?" → interface.
   - "Sau này có thể cần **thêm nhiều khả năng** cho cùng một class?" → interface (vì implements được nhiều).

### 1.8. Sai lầm hay gặp

- Dùng abstract class chỉ vì "muốn chia sẻ vài dòng code" → sau này class đó không `extends` được class khác nữa. Cân nhắc default method hoặc composition (T5).
- Đặt tên interface chung chung (`IManager`) → mất ý nghĩa. Đặt tên theo khả năng: `Describable`, `Payable`, `Comparable`.
- Quên `public` khi implement method của interface → lỗi "attempting to assign weaker access privileges".
- Nhét state vào interface (field trong interface là hằng số, không phải biến của đối tượng).
- Lạm dụng default method để biến interface thành abstract class trá hình.

---

## PHẦN 2 – DEMO: Hệ thống thanh toán

Demo này dùng đủ các khái niệm ở trên: interface, default/static method, abstract class, template method, `implements` thêm khả năng.

```java
// ===== 1. Interface: hợp đồng =====
public interface PaymentMethod {
    boolean pay(long amount);                       // abstract

    default String describe() {                     // default: dùng chung được
        return "Thanh toán bằng " + getClass().getSimpleName();
    }

    static boolean isValidAmount(long amount) {     // static: hàm tiện ích của interface
        return amount > 0;
    }
}

// Khả năng riêng, không phải phương thức nào cũng có
public interface Refundable {
    boolean refund(long amount);
}
```

```java
// ===== 2. Abstract class: phần dùng chung + template method =====
public abstract class AbstractPayment implements PaymentMethod {
    private final String owner;        // state chung
    protected long totalPaid;

    protected AbstractPayment(String owner) {
        if (owner == null || owner.isBlank()) {
            throw new IllegalArgumentException("owner không hợp lệ");
        }
        this.owner = owner;
    }

    @Override
    public final boolean pay(long amount) {              // khung cố định, con không được sửa
        if (!PaymentMethod.isValidAmount(amount)) return false;
        long total = amount + calcFee(amount);           // bước 1: con quyết định phí
        boolean ok = doCharge(total);                    // bước 2: con quyết định cách trừ tiền
        if (ok) totalPaid += total;                      // bước 3: chung cho mọi loại
        return ok;
    }

    protected abstract long calcFee(long amount);
    protected abstract boolean doCharge(long total);

    public String getOwner() { return owner; }
    public long getTotalPaid() { return totalPaid; }
}
```

```java
// ===== 3. Các class cụ thể =====
public class Cash extends AbstractPayment {
    public Cash(String owner) { super(owner); }

    @Override protected long calcFee(long amount) { return 0; }
    @Override protected boolean doCharge(long total) { return true; }
}

public class CreditCard extends AbstractPayment implements Refundable {
    private final long limit;

    public CreditCard(String owner, long limit) {
        super(owner);
        this.limit = limit;
    }

    @Override protected long calcFee(long amount) { return amount * 2 / 100; }   // phí 2%
    @Override protected boolean doCharge(long total) { return total <= limit; }

    @Override
    public boolean refund(long amount) {
        if (amount <= 0 || amount > totalPaid) return false;
        totalPaid -= amount;
        return true;
    }

    @Override
    public String describe() {                                  // override default
        return "Thẻ tín dụng của " + getOwner();
    }
}
```

```java
// ===== 4. Dùng: đa hình qua interface (nối T3) =====
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<PaymentMethod> methods = List.of(
                new Cash("An"),
                new CreditCard("Bình", 1_000_000)
        );

        for (PaymentMethod m : methods) {
            boolean ok = m.pay(500_000);
            System.out.println(m.describe() + " → " + (ok ? "OK" : "Thất bại"));

            if (m instanceof Refundable r) {                    // kiểm tra "khả năng"
                System.out.println("  hoàn 100000: " + r.refund(100_000));
            }
        }
    }
}
```

Kết quả mong đợi:

```
Thanh toán bằng Cash → OK
Thẻ tín dụng của Bình → OK
  hoàn 100000: true
```

**Đọc demo để thấy được gì:**

- `PaymentMethod` là **hợp đồng** → `Main` chỉ biết `PaymentMethod`, không quan tâm Cash hay CreditCard (đa hình).
- `AbstractPayment` giữ **state chung** (`owner`, `totalPaid`) và **khung `pay()`** (template method, `final`).
- `Refundable` là **khả năng thêm** chỉ một số class có: `CreditCard implements Refundable` còn `Cash` thì không. Abstract class không làm gọn được chuyện này vì `extends` chỉ 1.
- `describe()` là **default method**: `Cash` dùng luôn, `CreditCard` override lại.
- Một class vừa **extends** 1 abstract class vừa **implements** nhiều interface: `class CreditCard extends AbstractPayment implements Refundable`.

---

## TÓM TẮT 5 DÒNG

1. Interface = hợp đồng "làm được gì", implements nhiều được, không có state.
2. Abstract class = "là loại gì" + state + code chung, extends chỉ 1.
3. Default method cho phép mở rộng interface mà không làm vỡ code cũ; trùng default thì phải tự giải quyết bằng `X.super.method()`.
4. Mặc định chọn interface; chọn abstract class khi cần state/constructor/template method; hay kết hợp cả hai.
5. Backend (Spring) dựa trên interface + Dependency Injection để tách tầng, dễ thay thế và dễ test.