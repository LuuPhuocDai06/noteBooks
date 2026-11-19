# Java OOP 02 — Kế thừa, Override, Đa hình, super

## 1. Kế thừa
Kế thừa cho phép lớp **con** (subclass) dùng lại field và method của lớp **cha** (superclass) bằng từ khóa `extends`. Quan hệ là **"is-a"**: `Dog` is-a `Animal`.

Mục đích: tránh lặp code, và tạo nền cho đa hình (ngày 3).

```java
class Animal {
    protected String name;
    Animal(String name) { this.name = name; }
    void eat() { System.out.println(name + " is eating"); }
}

class Dog extends Animal {
    Dog(String name) { super(name); }   // gọi constructor cha
    void bark() { System.out.println(name + " says woof"); }
}
```

## 2. Các ý cần nắm về kế thừa

**a) `super`**
- `super(...)`: gọi constructor lớp cha, **phải là dòng đầu tiên** trong constructor con. Nếu không viết, Java tự chèn `super()` (cha phải có constructor không tham số, nếu không sẽ lỗi biên dịch).
- `super.method()`: gọi method của cha.

**b) Override (ghi đè)**
Con định nghĩa lại method của cha với **cùng tên, cùng tham số**.
```java
class Dog extends Animal {
    @Override
    void eat() {
        super.eat();                       // giữ hành vi cha
        System.out.println("...and wags tail");
    }
}
```
- Luôn dùng `@Override` để compiler bắt lỗi gõ sai.
- Không được thu hẹp quyền truy cập (cha `public` thì con không được `protected`).

**c) Override vs Overload**
| | Override | Overload |
|---|---|---|
| Ở đâu | Cha ↔ con | Cùng một lớp |
| Tham số | Giống hệt | Khác nhau |
| Quyết định lúc | Runtime | Compile time |

**d) Access modifier trong kế thừa**
`private`: con **không** truy cập trực tiếp (dùng getter). `protected`: con truy cập được. `public`: ai cũng truy cập.

**e) Thứ tự khởi tạo**
`new Dog("Rex")` → chạy constructor `Animal` trước, rồi mới tới `Dog`.

**f) Các quy tắc khác**
- Java chỉ cho **đơn kế thừa** (một cha duy nhất); đa kế thừa dùng `interface` (học sau).
- Mọi lớp đều ngầm kế thừa `Object` (nên có `toString`, `equals`, `hashCode`).
- `final class` không thể bị kế thừa (ví dụ `String`); `final method` không thể bị override.
- Hàm `static` và `private` **không** được override.

**g) Lưu ý**
Chỉ dùng kế thừa khi thật sự là "is-a". Trường hợp không phải thì có cách khác (composition), sẽ học ở T5.

## 3. Đa hình (Polymorphism)

### Khái niệm
**Đa hình** = một biến kiểu **cha** có thể trỏ tới đối tượng của nhiều kiểu **con** khác nhau, và khi gọi method thì Java chạy **phiên bản của đối tượng thật** (quyết định lúc runtime), không phải theo kiểu khai báo của biến.

```java
Animal a = new Dog("Rex");   // upcasting: biến kiểu cha, đối tượng con
a.eat();                     // chạy eat() của Dog (nếu Dog có override)
```

### Các ý cần nắm
**a) Upcasting** (con → cha): tự động, luôn an toàn.
```java
Animal a = new Dog("Rex");
```
Qua biến `a`, chỉ gọi được những method **có trong `Animal`**. `a.bark()` sẽ lỗi biên dịch dù đối tượng thật là Dog.

**b) Dynamic dispatch**: method được chạy phụ thuộc **đối tượng thật**, không phụ thuộc kiểu biến. Đây là lý do đa hình hoạt động, và chỉ áp dụng cho method được **override** (field và `static` thì không).

**c) `instanceof` và downcasting** (cha → con): phải ép kiểu thủ công, sai kiểu sẽ ném `ClassCastException`. Luôn kiểm tra trước:
```java
if (a instanceof Dog) {
    Dog d = (Dog) a;
    d.bark();
}
// Java 16+: gọn hơn
if (a instanceof Dog d) {
    d.bark();
}
```

**d) Lợi ích**: viết code chung cho nhiều kiểu, thêm kiểu mới không phải sửa code cũ.
```java
static void feedAll(Animal[] animals) {
    for (Animal a : animals) a.eat();   // Dog, Cat, ... đều chạy đúng bản của mình
}
```

**e) Override (đa hình lúc runtime) vs Overload (lúc compile)**: chỉ override mới là đa hình thật sự.

(Đa hình qua **interface** và `abstract class` sẽ học ở T4.)

## 4. Demo chạy được

```java
public class Main {
    static class Employee {
        private final String name;
        protected double baseSalary;

        Employee(String name, double baseSalary) {
            this.name = name;
            this.baseSalary = baseSalary;
            System.out.println("Employee constructor");
        }
        String getName() { return name; }
        double salary() { return baseSalary; }
        @Override public String toString() { return name + " - " + salary(); }
    }

    static class Manager extends Employee {
        private final double bonus;

        Manager(String name, double baseSalary, double bonus) {
            super(name, baseSalary);
            this.bonus = bonus;
            System.out.println("Manager constructor");
        }
        @Override double salary() { return super.salary() + bonus; }
    }

    public static void main(String[] args) {
        Employee e = new Employee("An", 1000);
        Manager m = new Manager("Binh", 1000, 500);
        System.out.println(e);   // An - 1000.0
        System.out.println(m);   // Binh - 1500.0 (toString của cha gọi salary() của con)

        // Đa hình: biến kiểu cha, đối tượng thật là con
        Employee[] list = { e, m, new Manager("Chi", 2000, 300) };
        double total = 0;
        for (Employee x : list) {
            total += x.salary();             // mỗi phần tử chạy salary() của chính nó
            if (x instanceof Manager mgr) {  // downcasting an toàn
                System.out.println(mgr.getName() + " là Manager");
            }
        }
        System.out.println("Tổng: " + total);   // 1000 + 1500 + 2300 = 4800.0
    }
}
```
Chạy và quan sát: thứ tự in constructor, vì sao `m.toString()` cho ra 1500, và vì sao vòng `for` tính đúng lương từng người dù biến `x` đều là kiểu `Employee`.

## 4. Lỗi thường gặp
1. Quên gọi `super(...)` khi cha không có constructor mặc định → lỗi biên dịch.
2. Quên `@Override`, gõ sai tên → thành method mới, không phải ghi đè.
3. Lạm dụng kế thừa cho quan hệ không phải "is-a".
4. Cho field `public` ở lớp cha → phá encapsulation của T2.
5. Downcast không kiểm tra `instanceof` → `ClassCastException`.
6. Tưởng `Animal a = new Dog()` thì gọi được `a.bark()` → lỗi biên dịch.
