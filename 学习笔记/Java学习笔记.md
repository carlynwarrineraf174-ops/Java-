# Java 学习笔记（全量版）

---

## 01 · OOP核心（封装继承多态）

### 1. 封装（Encapsulation）

**概念：** 将对象的属性（数据）和行为（方法）捆绑在一起，对外隐藏内部实现细节，仅暴露受控的访问接口。核心通过 `private` 关键字限制直接访问，通过 `getter/setter` 提供受控读写。

- **本质：** 数据隐藏 + 接口公开
- **目的：** 保护数据完整性，降低耦合，提高可维护性
- **实现手段：** `private` 修饰字段 → `public getXxx() / setXxx()` 访问

```java
class Person {
    private String name;
    private int age;

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public int getAge() { return age; }
    public void setAge(int age) {
        if (age > 0 && age < 150) this.age = age;
    }
}
```

### 2. 继承（Extends）

**概念：** 子类复用父类的属性和方法，并可以扩展或重写。Java 是**单继承**（一个类只能有一个直接父类），但可以通过接口实现"多继承"的效果。

- **本质：** is-a 关系，子类 IS-A 父类
- **关键字：** `extends`
- **构造器链：** 子类构造器默认调用 `super()`（父类无参构造）
- **方法重写（Override）：** 子类重写父类方法，运行时多态的基础
- **`super` 关键字：** 访问父类被隐藏的成员

```java
class Animal {
    public void eat() { System.out.println("吃"); }
}

class Dog extends Animal {
    @Override
    public void eat() { System.out.println("狗吃骨头"); }
}
```

- **继承的内存本质：** 子类对象在堆中有一块连续内存，前半部分是父类成员，后半部分是子类新增成员。

### 3. 多态（Polymorphism）

**概念：** 同一个方法调用，因对象实际类型不同而产生不同行为。多态的三个必要条件：继承、重写、父类引用指向子类对象。

- **编译时类型**（声明类型）vs **运行时类型**（实际对象类型）
- **动态绑定：** 方法调用时 JVM 根据对象的**实际类型**而不是声明类型来决定调用哪个方法
- **向上转型：** `Animal a = new Dog();` — 自动，但只能调用父类有的方法
- **向下转型：** `Dog d = (Dog) a;` — 强制，恢复子类特有方法

```java
Animal a = new Dog();
a.eat();  // 输出 "狗吃骨头"（动态绑定）

Dog d = (Dog) a;  // 向下转型
d.bark();          // 调用 Dog 特有方法
```

### 4. 方法重写（Override）vs 重载（Overload）

| | 重写（Override） | 重载（Overload） |
|---|---|---|
| 发生位置 | 父子类之间 | 同一个类 |
| 方法签名 | 必须相同 | 参数列表不同 |
| 返回类型 | 相同或协变 | 可不同 |
| 关键字 | `@Override` 可省略 | 无 |
| 绑定时机 | 运行时（动态绑定） | 编译时 |

### 5. 访问修饰符

| 修饰符 | 本类 | 同包 | 子类（不同包） | 任意 |
|--------|------|------|---------------|------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| 默认（无） | ✅ | ✅ | ❌ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

---

## 02 · 抽象类 接口 内部类 枚举

### 1. 抽象类（Abstract Class）

**概念：** 用 `abstract` 修饰的类，不能实例化，可以包含抽象方法和具体方法。抽象方法只声明不实现，强制子类去实现。

- **本质：** 模板设计模式 — 父类定义"骨架"，子类填补"细节"
- **谁用谁实现：** 抽象方法用 `abstract` 声明，子类必须 `@Override`
- **构造器：** 抽象类可以有构造器（给子类调用 `super()` 用）

```java
abstract class Animal {
    public abstract void eat();  // 抽象方法

    public void sleep() {        // 具体方法
        System.out.println("睡");
    }
}
```

### 2. 接口（Interface）

**概念：** 完全抽象的类型，只定义行为规范（方法签名），不包含实现。Java 8+ 允许 `default` 和 `static` 方法有方法体。

- **本质：** 契约/规范 — 类可以实现多个接口（弥补单继承局限）
- **对比抽象类：** 抽象类 = "是什么"（is-a），接口 = "能做什么"（can-do）
- **JDK8+ 新特性：** `default` 方法（提供默认实现）、`static` 方法（接口级工具方法）

```java
interface Usb {
    void connect();       // 抽象方法（默认 public abstract）
    void disconnect();
    default void info() { System.out.println("USB设备"); }  // 默认方法
}

class Camera implements Usb {
    @Override
    public void connect() { System.out.println("相机连接"); }
    @Override
    public void disconnect() { System.out.println("相机断开"); }
}
```

### 3. 内部类（Inner Class）

**概念：** 定义在另一个类内部的类。内部类可以访问外部类的所有成员（包括 private），编译后生成 `Outer$Inner.class`。

**四种内部类：**

| 类型 | 定义位置 | 特点 |
|------|---------|------|
| 成员内部类 | 类内部、方法外 | 持有外部类引用 `Outer.this` |
| 静态内部类 | `static` 修饰 | 不持有外部类引用 |
| 局部内部类 | 方法内 | 作用域仅限于方法 |
| 匿名内部类 | 表达式创建 | 没有类名，用于一次性实现接口/抽象类 |

```java
// 匿名内部类（最常用）
Arrays.sort(books, new Comparator() {
    @Override
    public int compare(Object o1, Object o2) {
        Book b1 = (Book) o1;
        Book b2 = (Book) o2;
        return b1.getPrice() - b2.getPrice();
    }
});
```

### 4. 枚举（Enum）

**概念：** 一组固定的常量集合，枚举类型是 `java.lang.Enum` 的子类。枚举可以有自己的字段、方法和构造器。

- **本质：** 枚举本质是类，每个枚举常量是类的实例
- **适用场景：** 星期、性别、状态机等有限固定值
- **特殊方法：** `values()`（返回所有枚举常量）、`valueOf()`（按名称获取）

```java
enum Season {
    SPRING, SUMMER, AUTUMN, WINTER;

    private String description;
    Season() { this.description = "季节"; }
}

Season s = Season.SUMMER;
System.out.println(s.ordinal());  // 1（索引从0开始）
```

### 5. final 关键字

**概念：** `final` 表示"最终不可变"，用途：

| 修饰目标 | 效果 |
|---------|------|
| `final class` | 不能被继承（如 String、Math） |
| `final method` | 不能被重写 |
| `final variable` | 值不可变（常量） |

### 6. 代码块（Code Block）

**概念：** 类中用 `{}` 包裹的代码段，在构造对象时自动执行。

```java
class Test {
    static { /* 静态代码块：类加载时执行一次 */ }
    { /* 实例代码块：每次创建对象执行，在构造器之前 */ }
}
```

---

## 03 · 常用类（String / Arrays / 异常）

### 1. String 类

**概念：** `String` 是不可变字符序列（immutable），底层是 `final byte[]`（JDK9+）。任何对 String 的"修改"都会创建新对象。因为不可变，所以线程安全且可以缓存哈希码。

- **字符串池（String Pool）：** JVM 维护一个字符串常量池，相同字面量复用对象
- **`new String("xxx")`** 创建新对象，不进入池
- **`intern()`：** 手动将字符串放入池

```java
String name = "Jack";
System.out.println(name.charAt(0));       // J
System.out.println(name.substring(5));     // Jack
System.out.println(name.substring(1, 5)); // ello
```

### 2. StringBuilder / StringBuffer

**概念：** 可变字符序列，用于高效字符串拼接。底层也是 `byte[]`，但可以动态扩容。

| 类 | 线程安全 | 性能 |
|---|---------|------|
| `StringBuffer` | ✅ 同步方法 | 略低 |
| `StringBuilder` | ❌ 不同步 | 更高（推荐单线程用） |

- **扩容机制：** 初始容量 16，超过时按 `(oldCapacity << 1) + 2` 扩容
- **适用场景：** 循环拼接大量字符串时，远快于 `String +=`

```java
StringBuffer s2 = new StringBuffer();
StringBuilder s3 = new StringBuilder();

long start = System.currentTimeMillis();
for (int i = 0; i < 8000000; i++) s2.append(i);
long end = System.currentTimeMillis();
System.out.println("Buffer Time:" + (end - start));

start = System.currentTimeMillis();
for (int i = 0; i < 8000000; i++) s3.append(i);
end = System.currentTimeMillis();
System.out.println("Builder Time:" + (end - start));
```

### 3. StringBuffer 常用操作

```java
StringBuffer sb = new StringBuffer("Hello,");
sb.append("World");
sb.delete(1, 2);          // 删除[1,2)范围的字符
sb.replace(1, 2, "Mik"); // 替换
sb.insert(3, ",");        // 插入
```

**价格千分位格式化：**

```java
Scanner scanner = new Scanner(System.in);
String str = scanner.next();
StringBuffer s1 = new StringBuffer(str);
int a = s1.indexOf(".");
while (true) {
    a -= 3;
    if (a <= 0) break;
    s1.insert(a, ",");
}
System.out.println(s1); // 如 1234567.89 → 1,234,567.89
```

### 4. Math 类

**概念：** `java.lang.Math` 提供数学运算的静态方法，所有方法都用 `static` 修饰，直接类名调用。该类构造器是 `private` 的，不能实例化。

```java
Math.abs(-3);      // 绝对值 → 3
Math.pow(3, 4);    // 幂运算 → 81.0
Math.ceil(2.1);    // 向上取整 → 3.0
Math.floor(2.9);   // 向下取整 → 2.0
Math.round(6.7);   // 四舍五入 → 7
Math.random();     // [0.0, 1.0) 随机数

// 生成 [2, 7) 的随机整数
double a = Math.random() * 5 + 2;
System.out.println((int) Math.round(a));
```

### 5. Arrays 类

**概念：** `java.util.Arrays` 是数组操作的工具类，所有方法都是静态的。核心功能：排序、查找、填充、复制、转字符串。

- **`sort()`：** 底层使用 Dual-Pivot Quicksort（基本类型）或 TimSort（对象类型）
- **`binarySearch()`：** 二分查找，要求数组已排序
- **`copyOf()`：** 底层调用 `System.arraycopy()`（native 方法，高效）

```java
Integer arr[] = {1, 64, 4, 465, 31, 5};

Arrays.sort(arr);                                // 排序
System.out.println(Arrays.toString(arr));        // 转字符串

int index = Arrays.binarySearch(arr, 1);          // 二分查找
Integer arr2[] = Arrays.copyOf(arr, arr.length + 1); // 扩容

Arrays.fill(arr3, null);                         // 填充
```

**自定义排序（Comparator）：**

```java
Arrays.sort(books, new Comparator() {
    @Override
    public int compare(Object o1, Object o2) {
        Book b1 = (Book) o1;
        Book b2 = (Book) o2;
        return b1.getName().length() - b2.getName().length();
    }
});
```

### 6. LocalDateTime（新日期API）

**概念：** Java 8 引入的不可变日期时间 API（`java.time` 包），替代旧的 `Date` 和 `Calendar`。核心类：`LocalDate`（日期）、`LocalTime`（时间）、`LocalDateTime`（日期+时间）、`DateTimeFormatter`（格式化）。

- **为什么取代 Date：** 旧 API 线程不安全、设计混乱（月份从0开始）、时区处理复杂
- **不可变：** 所有加减操作返回新对象，原对象不变

```java
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter fmt = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
String format = fmt.format(now);
System.out.println(format);                   // 2026-06-29 10:30:00

LocalDateTime future = now.plusWeeks(400);    // 加400周
System.out.println(fmt.format(future));
```

### 7. 异常处理 try-catch-finally

**概念：** Java 的异常处理机制用于捕获和处理运行时错误。`Throwable` 是顶层父类，分为 `Error`（不可处理，如 OutOfMemoryError）和 `Exception`（可处理）。

**异常分类：**
- **检查型异常（Checked Exception）：** 编译时必须处理（如 IOException、SQLException）
- **非检查型异常（Unchecked Exception / RuntimeException）：** 编译时不强制处理（如 NullPointerException、ArrayIndexOutOfBoundsException）

**执行顺序：** `try` → 有异常则进 `catch` → 最终进 `finally`（即使有 return 也会执行 finally）

```java
try {
    Person person = null;
    System.out.println(person.getName());        // NullPointerException
} catch (NullPointerException e) {
    System.out.println(e.getMessage());
} catch (NumberFormatException e) {
    System.out.println(e.getMessage());
} finally {
    System.out.println("continue..");            // 始终执行
}
```

**异常经典练习（输入校验）：**

```java
Scanner scanner = new Scanner(System.in);
boolean loop = true;
while (loop) {
    loop = false;
    String number = scanner.next();
    try {
        int a = Integer.parseInt(number);
    } catch (Exception e) {
        System.out.println("输入的不是整数，请重新输入：");
        loop = true;
    }
}
```

### 8. Chapter 10 作业精选

**Test11 — Book数组按价格/名字长度排序：**
```java
Book[] books = new Book[4];
books[0] = new Book("红楼梦", 100);
books[1] = new Book("金瓶梅", 90);

Arrays.sort(books, new Comparator() {
    @Override
    public int compare(Object o1, Object o2) {
        Book b1 = (Book) o1;
        Book b2 = (Book) o2;
        return b1.getName().length() - b2.getName().length();
    }
});
```

**Test12 — 字符串指定范围反转：**
```java
public static void reverse(String str, int start, int end) {
    StringBuilder sb = new StringBuilder(str);
    String substring = sb.substring(start, end + 1);
    String reverse = new StringBuilder(substring).reverse().toString();
    sb.replace(start, end + 1, reverse);
    System.out.println(sb);
}
```

**Test15 — 统计字符串中大写/小写/数字个数：**
```java
char[] arr = str.toCharArray();
int UpContent = 0, DownContent = 0, DiContent = 0;
for (char c : arr) {
    if (c >= 'a' && c <= 'z') DownContent++;
    else if (c >= 'A' && c <= 'Z') UpContent++;
    else if (c >= '0' && c <= '9') DiContent++;
}
```

---

## 04 · 集合框架

### 1. Collection 接口

**概念：** `Collection` 是单列集合的顶层接口，定义了集合的通用操作。Java 集合框架分为两大体系：`Collection`（单列）和 `Map`（双列键值对）。

- **核心方法：** `add()`、`remove()`、`size()`、`contains()`、`isEmpty()`、`clear()`
- **特点：** 可以存储不同类型对象（不使用泛型时），存储的是对象引用

```java
List arrayList = new ArrayList();
arrayList.add(1);
arrayList.add("Jack");
arrayList.add(true);
arrayList.remove(new Integer(1));   // 删除元素，非索引
System.out.println(arrayList.size());
System.out.println(arrayList.contains("Jack"));
```

### 2. 遍历方式：Iterator & 增强 for

**概念：** `Iterator`（迭代器）是集合遍历的统一接口，每个集合内部维护一个迭代器。增强 for（for-each）本质是语法糖，编译后转换成 Iterator 或数组索引遍历。

- **Iterator 特点：** 可以在遍历时通过 `iterator.remove()` 安全删除元素
- **增强 for 限制：** 遍历时不能增删集合元素（会抛 ConcurrentModificationException）

```java
// Iterator
Iterator iterator = arrayList.iterator();
while (iterator.hasNext()) {
    Object o = iterator.next();
    System.out.println(o);
}

// 增强 for
for (Object o : arrayList) {
    System.out.println(o);
}
```

### 3. List 接口

**概念：** `List` 是有序、可重复的集合，每个元素有索引。实现类主要有 `ArrayList`（数组结构，查询快增删慢）和 `LinkedList`（链表结构，增删快查询慢）。

```java
List list = new ArrayList();
list.add("aaa");
list.add(1, "OBJ");     // 指定位置插入
list.set(0, "Marry");   // 替换
list.remove("aaa");
list.remove(new Integer(1));
System.out.println(list.subList(0, 2));  // 子列表
```

### 4. ArrayList 底层原理

**概念：** `ArrayList` 底层是 `Object[]` 数组（JDK6 之前是 `Entry[]`），JDK8 开始第一次 `add()` 时初始化为容量 10 的数组。

- **扩容机制：** 超过容量时按 `oldCapacity + (oldCapacity >> 1)`（1.5倍）扩容，使用 `Arrays.copyOf()` 复制到新数组
- **增删效率低：** 中间插入/删除需要移动元素（`System.arraycopy()`）
- **查询效率高：** 通过索引直接访问数组元素（O(1)）

### 5. LinkedList（双向链表）

**概念：** `LinkedList` 底层是双向链表，每个节点（`Node`）持有 `prev`、`data`、`next` 三个属性。

- **增删效率高：** 只需修改节点引用（O(1)）
- **查询效率低：** 需要从头/尾遍历（O(n)）
- **双端操作：** 实现了 `Deque` 接口，可作为栈/队列使用

```java
// 手动模拟双向链表
class Node {
    public int data;
    public Node next;
    public Node prev;
}

// LinkedList 使用
LinkedList linkedList = new LinkedList();
linkedList.add("one");
linkedList.add("two");

// 三种遍历
Iterator iterator = linkedList.iterator();
for (Object o : linkedList) { }
for (int i = 0; i < linkedList.size(); i++) {
    System.out.println(linkedList.get(i));
}
```

### 6. Set（HashSet）

**概念：** `Set` 是无序、不可重复的集合。`HashSet` 底层是 `HashMap`（value 用固定的 `PRESENT` 对象填充），通过 `hashCode()` 和 `equals()` 保证元素唯一性。

- **去重原理：** 先计算元素的 `hashCode()` 定位桶，如果桶为空直接放入；如果桶不为空，用 `equals()` 逐个比较，没有相同则放入
- **顺序问题：** `HashSet` 不保证顺序，`LinkedHashSet` 按插入顺序维护，`TreeSet` 按排序规则维护

```java
HashSet hashSet = new HashSet();
hashSet.add("Jack");
hashSet.add("Jack");   // 重复元素不会添加
hashSet.add(null);
System.out.println(hashSet);
```

### 7. LinkedHashSet（按插入顺序）

**概念：** `LinkedHashSet` 继承自 `HashSet`，底层是 `LinkedHashMap`，在哈希表基础上用双向链表维护元素插入顺序。

```java
LinkedHashSet linkedHashSet = new LinkedHashSet();
linkedHashSet.add("Mike");
linkedHashSet.add("Jack"); // 维护插入顺序
```

### 8. TreeSet（Comparator 排序）

**概念：** `TreeSet` 底层是 `TreeMap`（红黑树），元素按自然顺序（`Comparable`）或自定义顺序（`Comparator`）排序，插入时间复杂度 O(log n)。

```java
TreeSet treeSet = new TreeSet(new Comparator() {
    @Override
    public int compare(Object o1, Object o2) {
        String s1 = (String) o1;
        String s2 = (String) o2;
        return s1.length() - s2.length();
    }
});
treeSet.add("1000");
treeSet.add("20000");
treeSet.add("300");
System.out.println(treeSet); // 按字符串长度排序
```

### 9. HashMap

**概念：** `HashMap` 是哈希表实现的 Map，存储键值对（key-value）。JDK8 底层 = 数组 + 链表 + 红黑树。

- **存储原理：** 计算 key 的 `hashCode()` → 二次哈希（`(h = key.hashCode()) ^ (h >>> 16)`）→ 定位数组索引 − 如果该位置为空直接放入；不为空则遍历链表/红黑树用 `equals()` 比较
- **扩容机制：** 默认容量 16，负载因子 0.75，超过 `容量 × 0.75` 时扩容为 2 倍
- **树化：** 当链表长度 ≥ 8 且数组长度 ≥ 64，链表转红黑树（查找 O(n) → O(log n)）
- **反树化：** resize 时如果红黑树节点 ≤ 6，退化为链表
- **key 不可变原则：** HashMap 的 key 最好用不可变类（如 String、Integer），否则 `hashCode` 变化会导致找不到元素

```java
Map hashMap = new HashMap();
hashMap.put("no1", "Jack");
hashMap.put("no2", "jim");

// 三种遍历方式
// 1) keySet 遍历
Set set = hashMap.keySet();
for (Object key : set) {
    System.out.println(key + " " + hashMap.get(key));
}

// 2) values 遍历
Collection values = hashMap.values();
for (Object value : values) { }

// 3) entrySet 遍历
Set set1 = hashMap.entrySet();
for (Object o : set1) {
    Map.Entry entry = (Map.Entry) o;
    System.out.println(entry.getKey() + "-" + entry.getValue());
}
```

### 10. HashTable

**概念：** `HashTable` 是线程安全的哈希表（方法用 `synchronized` 修饰），key 和 value 都不能为 null。与 `HashMap` 的区别：

- **线程安全：** HashTable 同步，HashMap 不同步
- **null 值：** HashTable 不允许 null key/value，HashMap 允许一个 null key
- **性能：** HashTable 因为同步开销，性能低于 HashMap
- **扩容：** HashTable 扩容为 2 倍 + 1，HashMap 为 2 倍

```java
Hashtable hashtable = new Hashtable();
hashtable.put("100", 1);
hashtable.put("200", 2);
// key 和 value 都不能为 null
```

### 11. TreeMap（Comparator 排序）

**概念：** `TreeMap` 是红黑树实现的 Map，按键排序。未传入 Comparator 时按 key 的自然顺序（要求 key 实现 Comparable）。

```java
TreeMap treeMap = new TreeMap(new Comparator() {
    @Override
    public int compare(Object o1, Object o2) {
        String s1 = (String) o1;
        String s2 = (String) o2;
        return s1.length() - s2.length();
    }
});
treeMap.put("100", "100");
treeMap.put("65465", "200");
```

### 12. Collections 工具类

**概念：** `java.util.Collections` 是集合的工具类，提供排序、查找、同步等静态方法。

```java
ArrayList list = new ArrayList();
Collections.reverse(list);     // 反转
Collections.shuffle(list);     // 打乱
Collections.sort(list);        // 排序
Collections.sort(list, new Comparator(){...}); // 自定义排序
Collections.max(list);                         // 最大值
Collections.frequency(list, "200");            // 出现次数
Collections.copy(list1, list);                 // 复制（需先初始化目标大小）
```

### 13. Properties

**概念：** `Properties` 继承 `HashTable`，key-value 都是 String 类型，常用于读写配置文件（`.properties` 文件）。

```java
Properties properties = new Properties();
properties.put("100", "1");
properties.put("200", 2);
System.out.println(properties.get("100"));
properties.remove("400");
System.out.println(properties.getProperty("100")); // 仅适用于String
```

### 14. Chapter 11 作业精选

**HW3 — LinkedList 存书按价格排序（冒泡）：**
```java
public static void Sort(List arrayList, Book[] books) {
    for (int i = 0; i < arrayList.size() - 1; i++) {
        for (int j = 0; j < arrayList.size() - 1 - i; j++) {
            if (books[j].getPrice() > books[j + 1].getPrice()) {
                Book temp = books[j];
                books[j] = books[j + 1];
                books[j + 1] = temp;
                arrayList.set(j, books[j]);
                arrayList.set(j + 1, temp);
            }
        }
    }
}
```

**HW4 — HashSet 去重（重写 equals + hashCode）：**
```java
public class Employee {
    private String name;
    private int age;

    @Override
    public boolean equals(Object o) {
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return age == employee.age && Objects.equals(name, employee.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
```

**HW7 — HashMap 遍历过滤：**
```java
HashMap hashMap = new HashMap();
hashMap.put("100", new People("Jack", 50000, "100"));

Set set1 = hashMap.entrySet();
for (Object o : set1) {
    Map.Entry entry = (Map.Entry) o;
    People people = (People) entry.getValue();
    if (people.getSalary() >= 18000) {
        System.out.println(people);
    }
}
```

**HW11 — HashSet 修改元素属性后 remove 的坑：**
```java
Person aa = new Person(101, "AA");
hashSet.add(aa);
aa.name = "CC";           // 修改属性后，hashCode 变了
hashSet.remove(aa);        // 无法删除（计算出的桶位置变了）
hashSet.add(new Person(101, "CC")); // 会添加到新桶
hashSet.add(new Person(101, "AA")); // 在旧桶添加
System.out.println(hashSet); // [101 CC, 101 CC, 101 AA] 🤯
```

> **注意：** HashSet 中元素作为 key 的 hashCode 依赖的属性不要修改，否则会导致内存泄漏！

---

## 05 · 泛型

### 1. 泛型引入

**概念：** 泛型（Generic）在 JDK5 引入，允许在定义类/接口/方法时使用类型占位符，编译时进行类型检查，消除强制类型转换。

- **本质：** 类型参数化 — 将类型作为参数传递
- **编译期行为：** 泛型信息仅在编译期存在，编译后通过"擦除"（Erasure）移除，JVM 运行时不知道泛型
- **优点：** 类型安全（编译期检查）、消除强转、代码复用

```java
// 不使用泛型——需要强转
ArrayList list = new ArrayList();
list.add(new Dog("Jack", 5));
Dog dog = (Dog) list.get(0);

// 使用泛型——编译期类型检查
ArrayList<Dog> dogs = new ArrayList<>();
dogs.add(new Dog("Jack", 5));
Dog dog = dogs.get(0);          // 不需要强转
```

### 2. 泛型类

**概念：** 在类声明时使用类型参数，类中的字段/方法可以使用该类型参数。

```java
class Pig<E> {
    E e;
    Pig(E e) { this.e = e; }
    public void f() { System.out.println(e.getClass()); }
}

Pig<A> aPig = new Pig<>(new A());
Pig<B> bPig = new Pig<>(new B());
```

**多泛型参数：**
```java
class Tiger<S, D, I> {
    private S s;
    private D d;
    private I i;
}

Tiger<String, Double, Integer> tiger = new Tiger<>("jack");
tiger.setD(20.0);
tiger.setI(50);
```

### 3. 泛型接口

**概念：** 与泛型类类似，在接口声明时使用类型参数。实现类可以指定具体类型或保留泛型。

```java
interface Tiger<K, T, V> {
    K getKey();
    T getValue();
}

class Mi implements Tiger<Double, Integer, String> {
    @Override
    public Double getKey() { return 0.0; }

    @Override
    public Integer getValue() { return 0; }
}
```

### 4. 泛型通配符

**概念：** `?` 表示未知类型，用于灵活处理泛型类型继承关系。

| 通配符 | 含义 | 能否读取 | 能否写入 |
|--------|------|---------|---------|
| `?` | 任意类型 | ✅（Object） | ❌ |
| `? extends T` | T 或 T 的子类 | ✅（T） | ❌ |
| `? super T` | T 或 T 的父类 | ✅（Object） | ✅（T） |

- **PECS 原则（Producer Extends, Consumer Super）：** 只读用 extends，只写用 super

### 5. Chapter 12 作业精选

**HW3 — Employee 按姓名长度 + 日期排序：**
```java
class MyDate implements Comparable<MyDate> {
    private int year, month, day;

    @Override
    public int compareTo(MyDate o) {
        int n1 = year - o.year;
        if (n1 == 0) {
            int n2 = month - o.month;
            if (n2 != 0) return n2;
            else return day - o.day;
        }
        return n1;
    }
}

// main 中排序
employees.sort(new Comparator<Employee>() {
    @Override
    public int compare(Employee o1, Employee o2) {
        int n1 = o1.getName().length() - o2.getName().length();
        if (n1 == 0) return o1.getDate().compareTo(o2.getDate());
        return n1;
    }
});
```

**HW4 — DAO 泛型类：**
```java
class Dao<T> {
    HashMap<String, T> map = new HashMap<>();

    public void save(String id, T entity) { map.put(id, entity); }
    public T get(String id) { return map.get(id); }
    public void update(String id, T entity) { map.put(id, entity); }
    public void delete(String id) { map.remove(id); }
    public void show() {
        for (String key : map.keySet()) {
            System.out.println(key + ":" + map.get(key));
        }
    }
}
```

---

## 06 · 多线程与线程池

### 1. 线程创建方式

**概念：** 线程是操作系统调度的最小单位，Java 中通过 `java.lang.Thread` 类或 `java.util.concurrent` 包实现多线程。三种创建方式：

| 方式 | 特点 | 返回值 | 适用场景 |
|------|------|--------|---------|
| `extends Thread` | 简单直接 | 无 | 单任务简单场景 |
| `implements Runnable` | 避免单继承限制 | 无 | 多任务共享资源 |
| `implements Callable` | 可返回结果，可抛异常 | 有（FutureTask 获取） | 需要返回值的任务 |

**守护线程（Daemon）：** `setDaemon(true)` — 所有用户线程结束时，守护线程自动结束（如 GC 就是守护线程）

**extends Thread：**
```java
class MyThread extends Thread {
    public MyThread(String name) { super(name); }

    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(getName() + "-" + i);
            Thread.yield();  // 让出CPU
        }
    }
}

// 启动
MyThread t1 = new MyThread("Plane");
t1.start();

// 守护线程
myThread1.setDaemon(true);  // 主线程结束则自动结束
```

**implements Runnable：**
```java
class MyRun implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            Thread t = Thread.currentThread();
            System.out.println(t.getName() + " " + i);
        }
    }
}

// 启动
MyRun mr = new MyRun();
Thread t1 = new Thread(mr, "Jack");
t1.setPriority(10);  // 设置优先级（1~10）
t1.start();
```

**implements Callable（有返回值）：**
```java
class MyCallable implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 0; i < 100; i++) sum += i;
        return sum;
    }
}

// 启动
MyCallable mc = new MyCallable();
FutureTask<Integer> ft = new FutureTask<>(mc);
Thread t1 = new Thread(ft);
t1.start();
Integer result = ft.get();  // 获取返回值（阻塞等待）
System.out.println(result);
```

### 2. 线程状态（6种）

```
NEW（新建）→ RUNNABLE（就绪/运行）→ BLOCKED（阻塞）→ WAITING（等待）→ TIMED_WAITING（超时等待）→ TERMINATED（终止）
```

- **`sleep()`：** 不释放锁，进入 TIMED_WAITING
- **`wait()`：** 释放锁，进入 WAITING（必须 synchronized 块内调用）
- **`yield()`：** 从 RUNNING 回到 RUNNABLE，让出 CPU（不一定成功）
- **`join()`：** 当前线程等待该线程执行完毕

### 3. synchronized 同步

**概念：** `synchronized` 是 JVM 级别的互斥锁，保证同一时刻只有一个线程执行同步代码块/方法。锁是**可重入**的（同一个线程可以多次获取同一把锁）。

- **锁的对象：**
  - 同步代码块：锁住括号内的对象
  - 同步实例方法：锁住 `this`
  - 同步静态方法：锁住类（`ClassName.class`）
- **特性：** 原子性（互斥执行）、可见性（释放锁时强制刷新缓存到内存）

```java
// 同步代码块
synchronized (MyThread.class) {
    if (ticket < 100) {
        ticket++;
        System.out.println(getName() + "卖第" + ticket + "张票");
    }
}
```

### 4. Lock 锁（ReentrantLock）

**概念：** `java.util.concurrent.locks.Lock` 是 JDK5 引入的显式锁，比 `synchronized` 更灵活（可中断、可超时、可尝试获取）。

- **`ReentrantLock`：** 可重入锁，与 `synchronized` 类似但功能更丰富
- **必须手动释放：** `lock()` 后必须在 `finally` 块中 `unlock()`
- **公平/非公平：** 构造器传 `true` 为公平锁（先等待先获取）

```java
class MyRun implements Runnable {
    int ticket = 0;
    Lock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
            lock.lock();
            try {
                if (ticket == 1000) { break; }
                ticket++;
                System.out.println(Thread.currentThread().getName() + "卖了" + ticket + "张票");
            } finally {
                lock.unlock();
            }
        }
    }
}
```

### 5. wait/notify 线程通信

**概念：** `wait()` 让当前线程进入 WAITING 状态并释放锁，`notify()` 随机唤醒一个等待线程，`notifyAll()` 唤醒所有等待线程。

- **必须 synchronized 块内调用：** 因为需要先获得对象的监视器（锁）
- **与 sleep 区别：** sleep 不释放锁，wait 释放锁

```java
// Desk 共享资源
class Desk {
    public static int foodCount = 10;  // 总碗数
    public static int foodFlag = 0;    // 0=没有食物, 1=有食物
    public static Object lock = new Object();
}

// 厨师（生产者）
class Cook extends Thread {
    @Override
    public void run() {
        while (true) {
            synchronized (Desk.lock) {
                if (Desk.foodCount == 0) break;
                if (Desk.foodFlag == 1) {
                    Desk.lock.wait();       // 有食物，等待
                } else {
                    System.out.println("做了一碗面条");
                    Desk.foodFlag = 1;
                    Desk.lock.notifyAll();  // 通知食客
                }
            }
        }
    }
}

// 食客（消费者）
class Foodie extends Thread {
    @Override
    public void run() {
        while (true) {
            synchronized (Desk.lock) {
                if (Desk.foodCount == 0) break;
                if (Desk.foodFlag == 0) {
                    Desk.lock.wait();       // 没食物，等待
                } else {
                    Desk.foodCount--;
                    System.out.println("在吃面条，还能再吃" + Desk.foodCount + "碗");
                    Desk.foodFlag = 0;
                    Desk.lock.notifyAll();  // 通知厨师
                }
            }
        }
    }
}
```

### 6. BlockingQueue 阻塞队列

**概念：** `BlockingQueue` 是线程安全的阻塞队列，当队列满时 `put()` 阻塞生产者，当队列空时 `take()` 阻塞消费者。基于 ReentrantLock + Condition 实现。

- **常用实现：** `ArrayBlockingQueue`（有界）、`LinkedBlockingQueue`（可选有界）
- **避免手动 wait/notify：** BlockingQueue 内部已经封装好线程通信逻辑

```java
ArrayBlockingQueue<String> queue = new ArrayBlockingQueue<>(1);

// 生产者
class Cook extends Thread {
    ArrayBlockingQueue queue;
    public Cook(ArrayBlockingQueue queue) { this.queue = queue; }

    @Override
    public void run() {
        while (true) {
            queue.put("厨师");  // 队列满时阻塞
            System.out.println("厨师做了一碗面条");
        }
    }
}

// 消费者
class Foodie extends Thread {
    ArrayBlockingQueue<String> queue;
    public Foodie(ArrayBlockingQueue<String> queue) { this.queue = queue; }

    @Override
    public void run() {
        while (true) {
            String take = queue.take();  // 队列空时阻塞
            System.out.println(take);
        }
    }
}
```

### 7. 线程池

**概念：** 线程池是创建和管理线程的机制，避免频繁创建/销毁线程的开销。核心思想：复用已创建的线程。

- **核心参数（ThreadPoolExecutor）：**
  - `corePoolSize`：核心线程数（常驻）
  - `maximumPoolSize`：最大线程数
  - `keepAliveTime`：非核心线程空闲存活时间
  - `workQueue`：任务队列（BlockingQueue）
  - `handler`：拒绝策略

**Executors 工厂方法（不推荐生产用，容易 OOM，建议手动 new ThreadPoolExecutor）：**
```java
// 缓存线程池（动态扩容）
ExecutorService pool = Executors.newCachedThreadPool();
pool.submit(new MyRunnable());

// 固定大小线程池
ExecutorService pool1 = Executors.newFixedThreadPool(3);
pool1.submit(new MyRunnable());
```

### 8. Chapter 14 作业精选

**Hw3 — 两个线程交替打印素数：**
```java
public class GetNumber extends Thread {
    public static int number = 1;
    int IsFound = 0;

    @Override
    public void run() {
        while (true) {
            synchronized (GetNumber.class) {
                if (number > 100) break;
                for (int i = 2; i < number; i++) {
                    IsFound = 1;
                    if (number % i == 0) { IsFound = 0; break; }
                }
                if (IsFound == 1) System.out.print(getName() + ":" + number + " ");
                number++;
            }
        }
    }
}
```

**HW4 — 抢红包系统：**
```java
public class MyThread extends Thread {
    public static int money = 100;
    public static int count = 1;
    public static int sum = 0;

    @Override
    public void run() {
        synchronized (MyThread.class) {
            if (count == 3) {
                System.out.println(getName() + "抢到了 " + (money - sum) + "元");
                count++;
            } else if (count < 3) {
                count++;
                int rand;
                do {
                    rand = (int) (Math.random() * 100 + 1);
                } while (rand >= (money - sum) || rand > money / 2 || rand <= 20);
                sum += rand;
                System.out.println(getName() + "抢到了" + rand + "元");
            }
        }
    }
}
```

---

## 07 · IO 流

### 1. File 类基本操作

**概念：** `java.io.File` 是文件和目录路径名的抽象表示，可以操作文件元数据（是否存在、大小、权限等），但不涉及文件内容读写。

- **抽象路径：** File 对象只是路径名的抽象，不一定是真实存在的文件
- **平台无关：** File 内部处理了路径分隔符（Windows 用 `\`，Linux 用 `/`）

```java
File file = new File("F:/t1.txt");
System.out.println(file.exists());          // 是否存在
System.out.println(file.isFile());          // 是否是文件
System.out.println(file.isDirectory());     // 是否是目录
System.out.println(file.getName());         // 文件名
System.out.println(file.getPath());         // 路径
System.out.println(file.length());          // 文件大小
System.out.println(file.lastModified());    // 最后修改时间
```

### 2. 创建 / 删除文件目录

```java
File t1 = new File("F:/JavaFile");
t1.mkdir();              // 创建单级目录
File t2 = new File("F:/JavaFile/a/b/c/d");
t2.mkdirs();             // 创建多级目录
File t3 = new File("F:/JavaFile/T1.txt");
t3.createNewFile();      // 创建文件
t2.delete();             // 删除空目录
```

### 3. 目录遍历

```java
File f1 = new File("F:/JavaFile");
String[] list = f1.list();        // 文件名数组
File[] files = f1.listFiles();    // File对象数组
```

### 4. IO 流体系

**概念：** 流（Stream）是数据的有序序列，Java 的 IO 流分为两大类：

| 方向 | 字节流（8位） | 字符流（16位） |
|------|-------------|--------------|
| 输入 | `InputStream` | `Reader` |
| 输出 | `OutputStream` | `Writer` |

- **字节流 vs 字符流：** 字节流处理二进制文件（图片、视频），字符流处理文本文件（TXT、Java）
- **节点流 vs 处理流：** 节点流直接连接数据源，处理流包装节点流提供额外功能（如缓冲流）
- **关闭原则：** 只关闭最外层处理流即可（内层流会自动关闭）

### 5. FileInputStream 读取文件

```java
// 单个字节读取
InputStream s1 = new FileInputStream("F:/java/ab.txt");
int b;
while ((b = s1.read()) != -1) {
    System.out.print((char) b);
}

// 字节数组读取（推荐）
byte[] buf = new byte[1024];
int len;
while ((len = s1.read(buf)) != -1) {
    System.out.print(new String(buf, 0, len));
}
s1.close();
```

### 6. FileOutputStream 写入文件

```java
// true = 追加模式
OutputStream s1 = new FileOutputStream("F:/java/ac.txt", true);
s1.write('a');
s1.write('b');
s1.write("我".getBytes());
byte[] b = "我是".getBytes();
s1.write(b);
s1.write("\r\n".getBytes());
s1.close();
```

### 7. FileWriter 字符流写入

```java
Writer s1 = new FileWriter("F:/java/ab.txt");
s1.write("1");
s1.write('a');
s1.write(97);       // 写入字符 'a'
char[] buffer = {'我', '是', '你'};
s1.write(buffer);
s1.write(buffer, 0, 2);
s1.close();
```

### 8. 缓冲流

**概念：** `BufferedInputStream`/`BufferedOutputStream` 在字节流基础上增加缓冲区（默认 8192 字节），减少实际 I/O 次数，显著提升读写性能。

- **装饰器模式：** 缓冲流包装节点流，增加功能而不改变接口
- **flush()：** 强制清空缓冲区写出（不需要凑满缓冲区）

```java
InputStream is = new FileInputStream("F:/source.txt");
InputStream bis = new BufferedInputStream(is);
OutputStream os = new FileOutputStream("F:/dest.txt");
OutputStream bos = new BufferedOutputStream(os);

byte[] buffer = new byte[1024];
int len;
while ((len = bis.read(buffer)) != -1) {
    bos.write(buffer, 0, len);
}
bos.close();  // 只需要关闭最外层
```

### 9. 图片复制（字节流）

```java
InputStream in = new FileInputStream("source.jpg");
OutputStream out = new FileOutputStream("dest.jpg");
byte[] b = new byte[1024];
int len;
while ((len = in.read(b)) != -1) {
    out.write(b, 0, len);
}
in.close();
out.close();
```

---

## 08 · 网络编程

### 1. TCP Socket 双向通信

**概念：** TCP（传输控制协议）是面向连接的、可靠的传输协议。Java 通过 `Socket`（客户端）和 `ServerSocket`（服务端）实现 TCP 通信。

- **三次握手：** 建立连接时客户端与服务端交换三个报文
- **四次挥手：** 断开连接时交换四个报文
- **TCP 特点：** 可靠性（确认重传）、有序性（序列号）、流量控制（滑动窗口）

**服务端：**
```java
ServerSocket serverSocket = new ServerSocket(9999);
System.out.println("服务端在9999监听...");
Socket socket = serverSocket.accept();  // 阻塞等待客户端

// 读取客户端消息（字符流）
BufferedReader br = new BufferedReader(
    new InputStreamReader(socket.getInputStream()));
String s = br.readLine();
System.out.println(s);

// 回复客户端
BufferedWriter bw = new BufferedWriter(
    new OutputStreamWriter(socket.getOutputStream()));
bw.write("Hello Client 字符流");
bw.newLine();
bw.flush();

bw.close();
br.close();
socket.close();
serverSocket.close();
```

**客户端：**
```java
Socket socket = new Socket(InetAddress.getLocalHost(), 9999);

// 发送消息
BufferedWriter bw = new BufferedWriter(
    new OutputStreamWriter(socket.getOutputStream()));
bw.write("Hello Server 字符流");
bw.newLine();
bw.flush();

// 接收回复
BufferedReader br = new BufferedReader(
    new InputStreamReader(socket.getInputStream()));
String s = br.readLine();
System.out.println(s);

br.close();
bw.close();
socket.close();
```

### 2. UDP 通信

**概念：** UDP（用户数据报协议）是无连接的、不可靠的传输协议，但传输效率高。Java 通过 `DatagramSocket` 和 `DatagramPacket` 实现。

- **TCP vs UDP：**
  - TCP 面向连接，UDP 无连接
  - TCP 可靠，UDP 不可靠（丢包不重传）
  - TCP 有序，UDP 可能乱序
  - TCP 效率低（有开销），UDP 效率高
  - 适用场景：TCP → 网页、文件传输；UDP → 视频流、DNS、在线游戏

**发送端：**
```java
DatagramSocket socket = new DatagramSocket(9998);
byte[] data = "明天吃火锅".getBytes();
DatagramPacket packet = new DatagramPacket(
    data, data.length, InetAddress.getByName("172.24.176.1"), 9999);
socket.send(packet);

// 接收回复
byte[] buffer = new byte[1024];
DatagramPacket packet1 = new DatagramPacket(buffer, buffer.length);
socket.receive(packet1);
String s = new String(packet1.getData(), 0, packet1.getLength());
System.out.println(s);
socket.close();
```

**接收端：**
```java
DatagramSocket socket = new DatagramSocket(9999);
byte[] buffer = new byte[1024];
DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
socket.receive(packet);
String s = new String(packet.getData(), 0, packet.getLength());
System.out.println(s);

// 回复
byte[] data1 = "好的，明天见".getBytes();
DatagramPacket packet1 = new DatagramPacket(
    data1, data1.length, InetAddress.getByName("172.24.176.1"), 9998);
socket.send(packet1);
socket.close();
```

### 3. 文件上传（TCP + 字节流）

**工具类 StreamUtils：**
```java
public static byte[] streamToByteArray(InputStream is) throws Exception {
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    byte[] buffer = new byte[1024];
    int len;
    while ((len = is.read(buffer)) != -1) {
        baos.write(buffer, 0, len);
    }
    return baos.toByteArray();
}
```

**客户端上传文件：**
```java
Socket socket = new Socket(InetAddress.getLocalHost(), 8888);

BufferedInputStream bis = new BufferedInputStream(new FileInputStream(filePath));
byte[] bytes = StreamUtils.streamToByteArray(bis);
BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());
bos.write(bytes);
bos.flush();
bis.close();
socket.shutdownOutput();

String s = StreamUtils.streamToString(socket.getInputStream());
System.out.println(s);
```

**服务端接收文件：**
```java
ServerSocket serverSocket = new ServerSocket(8888);
Socket socket = serverSocket.accept();

BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
byte[] bytes = StreamUtils.streamToByteArray(bis);

BufferedOutputStream bos = new BufferedOutputStream(
    new FileOutputStream("src/photo1.png"));
bos.write(bytes);
bos.close();

BufferedWriter bw = new BufferedWriter(
    new OutputStreamWriter(socket.getOutputStream()));
bw.write("收到图片");
bw.flush();
socket.shutdownOutput();
```

### 4. Chapter 16 作业精选

**Serve01/Client01 — TCP 问答：**
```java
// 服务端
BufferedReader br = new BufferedReader(
    new InputStreamReader(socket.getInputStream()));
String s = br.readLine();
if ("name".equals(s)) System.out.println("nove");
else if ("hobby".equals(s)) System.out.println("badminton");

// 客户端（发送用户输入）
Scanner scanner = new Scanner(System.in);
String question = scanner.nextLine();
bw.write(question);
bw.newLine();
bw.flush();
```

**Serve03/Client03 — TCP 文件下载：**
```java
// 服务端——根据文件名发送不同图片
BufferedInputStream bis = new BufferedInputStream(new FileInputStream(s1));
byte[] buffer1 = StreamUtils.streamToByteArray(bis);
BufferedOutputStream bos = new BufferedOutputStream(socket1.getOutputStream());
bos.write(buffer1);
bos.flush();
socket1.shutdownOutput();

// 客户端——输入文件名，接收并保存
OutputStream os = socket.getOutputStream();
os.write(ResFileName.getBytes());
socket.shutdownOutput();

BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
byte[] buffer = StreamUtils.streamToByteArray(bis);
BufferedOutputStream bos = new BufferedOutputStream(
    new FileOutputStream("e:\\" + ResFileName + ".png"));
bos.write(buffer);
```

---

## 09 · 注解（待补充）

**注：此章节为空白章节，暂无实际代码。**

---

## 10 · 反射（Reflection）

### 1. 反射入门

**概念：** 反射（Reflection）是 Java 的**动态**机制，允许程序在**运行时**获取任意类的内部信息（字段、方法、构造器）并操作对象。这是 Java 被称为"动态语言"的关键特性。

- **本质：** 将类的各个组成部分（字段、方法、构造器）映射成对应的 Class 对象，反过来操作它们
- **用途：** 框架开发（Spring、MyBatis）、IDE 自动提示、动态代理、反编译工具
- **代价：** 反射有性能开销（比直接调用慢），因为需要动态解析类型信息

**从配置文件动态加载类：**
```java
Properties p = new Properties();
p.load(new FileInputStream("F:\\java\\Chapter18\\src\\re.proper"));
String path1 = p.getProperty("Path");     // Reflection.Cat
String method1 = p.getProperty("method");  // cry

Class c = Class.forName(path1);           // 加载类
Object obj = c.newInstance();             // 创建对象（调用无参构造）
Method m = c.getMethod(method1);          // 获取方法
m.invoke(obj);                            // 调用方法

Field name = c.getField("age");           // 获取 public 字段
System.out.println(name.get(obj));        // 读取字段值

Constructor constructor = c.getConstructor(); // 获取无参构造器
```

**配置文件 re.proper：**
```properties
Path = Reflection.Cat
method = cry
```

### 2. 反射 vs 直接调用性能对比

```java
// 直接调用
Cat cat = new Cat();
long l = System.currentTimeMillis();
for (int i = 0; i < 9000; i++) cat.hi();
long l1 = System.currentTimeMillis();
System.out.println((l1 - l) + " ms");

// 反射调用
Class c = Class.forName("Reflection.Cat");
Object obj = c.newInstance();
Method m = c.getMethod("hi");
long l = System.currentTimeMillis();
for (int i = 0; i < 9000; i++) m.invoke(obj);
long l1 = System.currentTimeMillis();
System.out.println((l1 - l) + " ms");
```

### 3. 获取 Class 对象的三种方式

**概念：** `Class` 对象是反射的入口，JVM 为每个类加载后生成唯一的 Class 对象。

| 方式 | 代码 | 适用场景 |
|------|------|---------|
| 1. `Class.forName()` | `Class.forName("全类名")` | 配置文件，不知道具体类 |
| 2. 类名.class | `String.class` | 已知具体类 |
| 3. 对象.getClass() | `"hello".getClass()` | 已有对象 |

### 4. 字段操作（Field）

```java
Class c = Class.forName("Reflection.Car");
Object obj = c.newInstance();

Field f = c.getField("brand");
System.out.println(f.get(obj));    // BMW
f.set(obj, "WWB");                 // 修改字段
System.out.println(f.get(obj));    // WWB

Field[] fields = c.getFields();    // 获取所有 public 字段
for (Field field : fields) {
    System.out.println(field.getName());
}
```

### 5. 类信息获取

```java
Class c = Class.forName("Reflection.Person");

c.getName();              // 全类名 Reflection.Person
c.getSimpleName();        // 简单类名 Person
c.getFields();            // public 字段（含父类）
c.getDeclaredFields();    // 所有字段（不包括父类）
c.getMethods();           // public 方法（含父类）
c.getDeclaredMethods();   // 所有方法（不包括父类）
c.getConstructors();      // public 构造器
c.getDeclaredConstructors(); // 所有构造器
c.getPackage();           // 包名
c.getSuperclass();        // 父类
c.getInterfaces();        // 实现的接口
c.getAnnotations();       // 注解
```

### 6. 构造器反射

```java
Class c = Class.forName("Reflection.User");

// 调用无参构造
Object o = c.newInstance();

// 调用 public 有参构造
Constructor constructor = c.getConstructor(String.class);
Object obj = constructor.newInstance("Jack");

// 调用 private 有参构造（爆破）
Constructor constructor1 = c.getDeclaredConstructor(int.class, String.class);
constructor1.setAccessible(true);  // 爆破
Object obj1 = constructor1.newInstance(50, "BB");
```

### 7. 爆破 private 成员

**概念：** `setAccessible(true)` 是反射的"暴力破解"机制，可以绕过 Java 的访问权限检查，直接访问 private 字段和方法。框架底层大量使用此机制。

```java
Class c = Class.forName("Reflection.Student");
Object o = c.newInstance();

// public 字段直接操作
Field age = c.getField("age");
age.set(o, 40);
System.out.println(age.get(o));

// private 字段需要爆破
Field name = c.getDeclaredField("name");
name.setAccessible(true);   // 爆破
name.set(o, "Mike");
System.out.println(name.get(o));
```

### 8. 爆破 private 方法

```java
Class c = Class.forName("Reflection.Boss");
Object o = c.newInstance();

// public 方法
Method method = c.getMethod("hi", String.class);
method.invoke(o, "hello");

// private 方法（爆破）
Method method1 = c.getDeclaredMethod("hi", String.class);
method1.setAccessible(true);
method1.invoke(o, "nihao");

// 静态 private 方法（invoke 传 null）
Method method2 = c.getDeclaredMethod("say", int.class, String.class, char.class);
method2.setAccessible(true);
System.out.println(method2.invoke(null, 30, "Mike", '女'));
```

### 9. Chapter 18 作业精选

**HW1 — 反射修改私有字段：**
```java
Class c = Class.forName("Reflection.privateTest");
Object o = c.newInstance();
Field name = c.getDeclaredField("name");
name.setAccessible(true);
name.set(o, "hi,Kitty");
System.out.println(name.get(o));
```

**HW2 — 反射创建 File 对象并创建文件：**
```java
Class c = Class.forName("java.io.File");
Constructor constructor = c.getConstructor(String.class);
Object o = constructor.newInstance("F:\\news.txt");
Method method = c.getMethod("createNewFile");
method.invoke(o);
```

---

## MySQL 数据库

### 一、数据库操作（DDL — Data Definition Language）

**概念：** DDL 是数据定义语言，用于定义数据库的结构（创建、修改、删除数据库和表）。自动提交，不可回滚。

```sql
-- 创建数据库
CREATE DATABASE db_name;
CREATE DATABASE IF NOT EXISTS db_name CHARACTER SET utf8mb4;

-- 查看所有数据库
SHOW DATABASES;

-- 查看建库语句
SHOW CREATE DATABASE db_name;

-- 删除数据库
DROP DATABASE IF EXISTS db_name;

-- 选择数据库
USE db_name;

-- 查看当前使用的数据库
SELECT DATABASE();
```

### 二、表操作（CREATE / ALTER / DROP）

**概念：** 表是数据库存储数据的基本单位，由行（记录）和列（字段）组成。DDL 中的表操作包括建表、改表结构、删表。

```sql
-- 创建表
CREATE TABLE student (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(32) NOT NULL,
    age INT,
    gender CHAR(1) DEFAULT '男',
    birthday DATE
);

-- 查看表结构
DESC student;

-- 查看建表语句
SHOW CREATE TABLE student;

-- 修改表
ALTER TABLE student ADD email VARCHAR(64);           -- 添加列
ALTER TABLE student MODIFY age TINYINT;              -- 修改列类型
ALTER TABLE student CHANGE name username VARCHAR(32); -- 重命名列
ALTER TABLE student DROP email;                      -- 删除列
ALTER TABLE student CHARACTER SET utf8mb4;           -- 修改字符集
RENAME TABLE student TO student2;                    -- 重命名表
DROP TABLE student;                                  -- 删除表
```

### 三、数据类型

**概念：** MySQL 的数据类型决定列能存储什么类型的数据以及占用的存储空间。选择合适的数据类型可以节省空间并提高查询性能。

```sql
-- 整数
INT          -- 普通整数（4字节，±21亿）
TINYINT      -- 小整数（1字节，0~255 或 -128~127）
BIGINT       -- 大整数（8字节）

-- 小数
FLOAT(4,2)   -- 单精度浮点，总4位小数2位
DOUBLE       -- 双精度浮点
DECIMAL(30,20) -- 高精度定点数（金融用！不会丢失精度）
BIT(8)       -- 位类型

-- 字符串
CHAR(n)      -- 定长（n≤255），性能好，适合固定长度（如手机号）
VARCHAR(n)   -- 变长（n≤65535），省空间，适合可变长度（如姓名）
TEXT         -- 长文本（65535字节）
MEDIUMTEXT   -- 中长文本（16MB）
LONGTEXT     -- 超长文本（4GB）

-- 日期时间
DATE         -- 2026-07-23
DATETIME     -- 2026-07-23 10:30:00（不受时区影响）
TIMESTAMP    -- 时间戳（受时区影响，自动更新用）
```

**TIMESTAMP 自动更新技巧：**
```sql
login_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
-- 插入时自动填当前时间，更新时自动刷新
```

### 四、DML（Data Manipulation Language — 增删改）

**概念：** DML 是数据操作语言，用于操作表中的数据（增、删、改）。DML 操作需要事务提交（COMMIT）才能永久生效。

```sql
-- 插入
INSERT INTO student VALUES(1, 'Jack', 20, '男', '2006-01-01');
INSERT INTO student(id, name) VALUES(2, 'Mike');  -- 插入指定列

-- 更新
UPDATE student SET age = 21 WHERE id = 1;
UPDATE student SET salary = salary + 1000, job = '经理' WHERE name = '张三';

-- 删除
DELETE FROM student WHERE id = 2;  -- 删除行（自增ID不会重置）
DELETE FROM student;                -- 清空表（自增ID不会重置）
TRUNCATE TABLE student;             -- 清空表（自增ID重置，速度快，不可回滚）
```

### 五、DQL 查询（Data Query Language）

**概念：** DQL 用于从数据库查询数据，是 SQL 中最核心、最灵活的部分。执行顺序：`FROM` → `WHERE` → `GROUP BY` → `HAVING` → `SELECT` → `ORDER BY` → `LIMIT`。

#### 5.1 基本查询

```sql
SELECT * FROM student;                                        -- 查全部
SELECT id, name FROM student;                                 -- 查指定列
SELECT DISTINCT class FROM student;                           -- 去重
SELECT (chinese + math + english) AS total FROM score;        -- 计算字段 + 别名
```

#### 5.2 WHERE 条件

**概念：** WHERE 用于过滤行记录，在分组之前执行。可以使用比较运算符、逻辑运算符、LIKE 模糊匹配、BETWEEN 范围查询。

```sql
-- 比较运算符
WHERE math > 60 AND chinese > 80
WHERE english BETWEEN 80 AND 90
WHERE name LIKE 'j%'     -- j开头
WHERE name LIKE '%j%'    -- 含j

-- ORDER BY 排序
SELECT * FROM student ORDER BY math DESC;            -- 降序
SELECT * FROM student ORDER BY total_score DESC;     -- 按别名排序
```

#### 5.3 聚合函数 + GROUP BY

**概念：** 聚合函数对一组值计算并返回单个值。`GROUP BY` 将数据按列分组，分组后 SELECT 只能出现分组列和聚合函数。
`HAVING` 在分组后过滤（WHERE 在分组前过滤）。

```sql
SELECT COUNT(*) FROM student;                        -- 行数
SELECT SUM(math) FROM score;                         -- 求和
SELECT AVG(math) FROM score;                         -- 平均值
SELECT MAX(total), MIN(total) FROM score;            -- 最大/最小

-- GROUP BY 分组
SELECT class, COUNT(*) FROM student GROUP BY class;

-- HAVING 分组后过滤
SELECT course, AVG(score) FROM score 
GROUP BY course HAVING AVG(score) > 60;

SELECT category, SUM(amount) FROM orders 
GROUP BY category HAVING SUM(amount) > 2000;
```

#### 5.4 多表查询

**概念：** 多表查询从两个或多个表中获取数据。等值连接通过 WHERE 条件匹配行，JOIN 是 ANSI SQL 标准写法，可读性更好。

- **内连接（INNER JOIN）：** 只返回匹配的行
- **外连接：**
  - `LEFT JOIN`：左表全部 + 右表匹配
  - `RIGHT JOIN`：右表全部 + 左表匹配

```sql
-- 等值连接
SELECT p.project_name, e.name 
FROM project p, employee e 
WHERE p.manager_id = e.id;

-- JOIN（推荐）
SELECT p.project_name, e.name 
FROM project p 
JOIN employee e ON p.manager_id = e.id;
```

#### 5.5 子查询

**概念：** 嵌套在另一个 SQL 语句中的查询。子查询先执行，结果为外层查询提供条件。

- **单行子查询：** 返回一个值（使用 `=`、`>` 等比较运算符）
- **多行子查询：** 返回多个值（使用 `IN`、`ANY`、`ALL`）
- **相关子查询：** 子查询引用外层查询的列（如 `EXISTS`）

```sql
-- 单行子查询
SELECT name FROM employee 
WHERE dept_id = (SELECT dept_id FROM employee WHERE name = '张伟')
  AND name != '张伟';

-- 多行子查询（IN）
SELECT dept_id, name, salary FROM employee
WHERE (dept_id, salary) IN (
    SELECT dept_id, MAX(salary) FROM employee GROUP BY dept_id
);

-- 子查询 + HAVING
SELECT name FROM employee
WHERE salary > (SELECT AVG(salary) FROM employee GROUP BY dept_id);
```

#### 5.6 LIMIT 翻页

```sql
-- 前 5 条
SELECT * FROM employee LIMIT 5;

-- 第 6~10 条（offset=5, count=5）
SELECT * FROM employee LIMIT 5, 5;
```

### 六、约束（Constraint）

**概念：** 约束是数据库保证数据完整性的规则。MySQL 支持六大约束，约束可以在建表时定义，也可以建表后添加/删除。

| 约束 | 作用 | 说明 |
|------|------|------|
| `NOT NULL` | 列值不能为 NULL | 业务必要字段 |
| `UNIQUE` | 列值唯一 | 允许一个 NULL |
| `PRIMARY KEY` | 主键 = NOT NULL + UNIQUE | 每表一个 |
| `FOREIGN KEY` | 引用另一张表的主键 | 保证引用完整性 |
| `DEFAULT` | 默认值 | 插入未指定时用默认 |
| `CHECK` | 检查约束（MySQL 8 才真正生效） | 限制值范围 |

```sql
-- 建表时加约束
CREATE TABLE student (
    id INT PRIMARY KEY AUTO_INCREMENT,      -- 主键自增
    name VARCHAR(32) NOT NULL,               -- 非空
    email VARCHAR(64) UNIQUE,                -- 唯一
    age INT DEFAULT 18,                      -- 默认18
    class_id INT,
    gender CHAR(1) CHECK (gender IN ('男','女')), -- 检查
    FOREIGN KEY (class_id) REFERENCES class(id) ON DELETE CASCADE
);

-- 外键（保持数据一致性）
-- 外键列的值必须存在于被引用表的主键中
-- 被引用的行不能直接删除（除非 ON DELETE CASCADE）
```

### 七、索引（Index）

**概念：** 索引是一种数据结构（B+ 树），用于加速数据库查询。相当于书的目录，将全表扫描（O(n)）变为树查找（O(log n)）。

- **物理结构：** MySQL InnoDB 使用 B+ 树，叶子节点存数据，非叶子节点存索引键
- **聚簇索引：** InnoDB 的主键索引，叶子节点直接存储行数据
- **二级索引：** 非主键索引，叶子节点存储主键值（回表查询）

```sql
-- 创建索引
CREATE INDEX idx_name ON student(name);          -- 普通索引
CREATE UNIQUE INDEX idx_email ON student(email); -- 唯一索引
ALTER TABLE student ADD INDEX idx_age(age);       -- 添加索引

-- 主键索引（自动创建）
ALTER TABLE student ADD PRIMARY KEY (id);

-- 删除索引
DROP INDEX idx_name ON student;
ALTER TABLE student DROP PRIMARY KEY;

-- 查看索引
SHOW INDEXES FROM student;
SHOW KEYS FROM student;

-- 索引使用原则
-- ① 频繁 WHERE 的列建索引
-- ② 频繁 ORDER BY 的列建索引
-- ③ 数据量小的表不用索引
-- ④ 频繁增删改的列慎建索引
-- ⑤ 联合索引：最左前缀原则
```

### 八、事务与隔离级别

**概念：** 事务是一组 SQL 操作的逻辑单元，要么全部成功，要么全部回滚。事务通过 ACID 特性保证数据可靠性。

**ACID 四大特性：**
| 特性 | 含义 | 实现机制 |
|------|------|---------|
| Atomicity（原子性） | 要么全成功，要么全回滚 | undo log |
| Consistency（一致性） | 数据始终满足约束 | 应用层 + 数据库层 |
| Isolation（隔离性） | 并发事务互不干扰 | 锁 + MVCC |
| Durability（持久性） | 提交后数据不丢失 | redo log |

**事务操作：**
```sql
START TRANSACTION;     -- 开启事务
SAVEPOINT sp1;         -- 设置保存点
ROLLBACK TO sp1;       -- 回滚到保存点
COMMIT;                -- 提交事务
```

**四大隔离级别对比：**

| 级别 | 脏读 | 不可重复读 | 幻读 |
|------|------|-----------|------|
| READ UNCOMMITTED | ✅ 会 | ✅ 会 | ✅ 会 |
| READ COMMITTED | ❌ | ✅ 会 | ✅ 会 |
| REPEATABLE READ（MySQL 默认） | ❌ | ❌ | ✅ 会 |
| SERIALIZABLE | ❌ | ❌ | ❌ |

**概念解释：**
- **脏读：** 读到另一个事务未提交的数据（可能被回滚）
- **不可重复读：** 同一事务两次读同一行，结果不同（期间被修改并提交）
- **幻读：** 同一事务两次查询，行数不同（期间被插入/删除）
- **MVCC（多版本并发控制）：** InnoDB 通过 undo log 实现数据多版本，每个事务看到的是某个时间点的快照。REPEATABLE READ 级别下，事务开始时创建一致性读视图，整个事务期间不变。

### 九、视图（View）

**概念：** 视图是虚拟表，本质是封装好的 SELECT 查询。视图不存储数据（非物化视图），每次查询视图时动态执行封装的 SQL。

- **优点：**
  - **简化复杂查询：** 把多表 JOIN 封装成视图，像查单表一样使用
  - **数据安全：** 隐藏敏感列（如密码），只暴露需要的列
  - **逻辑独立性：** 基表结构变化时只需修改视图定义

```sql
-- 创建视图
CREATE VIEW dept_salary_view AS
SELECT dept_name, AVG(salary) AS avg_salary
FROM employee
GROUP BY dept_name;

-- 使用视图
SELECT * FROM dept_salary_view WHERE avg_salary > 15000;

-- 修改视图
CREATE OR REPLACE VIEW dept_salary_view AS
SELECT dept_name, MAX(salary) AS max_salary FROM employee GROUP BY dept_name;

-- 删除视图
DROP VIEW IF EXISTS dept_salary_view;

-- 视图的增删改（有限制）
-- 简单视图可以 UPDATE/INSERT/DELETE
-- 含 GROUP BY / DISTINCT / 聚合函数的视图不能增删改
```

### 十、用户管理

**概念：** MySQL 的权限系统通过 "用户 + 主机" 双重认证，可以精确控制每个用户对哪些库/表有什么操作权限。

```sql
-- 创建用户
CREATE USER 'jack'@'localhost' IDENTIFIED BY '123456';

-- 删除用户
DROP USER 'jack'@'localhost';

-- 修改密码
ALTER USER 'jack'@'localhost' IDENTIFIED BY 'newpass';

-- 查询用户
SELECT user, host FROM mysql.user;

-- 授权
GRANT SELECT, INSERT ON mydb.* TO 'jack'@'localhost';
GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;

-- 查看权限
SHOW GRANTS FOR 'jack'@'localhost';

-- 回收权限
REVOKE DELETE ON mydb.* FROM 'jack'@'localhost';

-- 刷新权限
FLUSH PRIVILEGES;
```

### 十一、字符串函数速查

```sql
CONCAT('Hello', ' ', 'World')  -- 字符串拼接
INSTR('hello world', 'world')  -- 返回子串位置（6）
UCASE('hello')                  -- 转大写 → 'HELLO'
LCASE('HELLO')                  -- 转小写 → 'hello'
LEFT('hello', 2)                -- 取左边2字符 → 'he'
RIGHT('hello', 2)               -- 取右边2字符 → 'lo'
LENGTH('hello')                 -- 字节数（UTF8中文占3字节）
CHAR_LENGTH('你好')              -- 字符数（2）
REPLACE('abc', 'b', 'x')       -- 替换 → 'axc'
SUBSTRING('hello', 2, 3)       -- 截取 → 'ell'
TRIM('  hi  ')                  -- 去空格
```

### 十二、MySQL 知识图谱

```
MySQL ─┬─ 库操作（CREATE/DROP/SHOW/USE）
       ├─ 表操作（CREATE/ALTER/DROP/DESC/RENAME）
       ├─ 数据类型 ─┬─ 整数/小数（INT/DECIMAL）
       │            ├─ 字符串（CHAR/VARCHAR/TEXT）
       │            └─ 日期（DATE/DATETIME/TIMESTAMP）
       ├─ DML ─┬─ INSERT
       │       ├─ UPDATE
       │       └─ DELETE
       ├─ DQL ─┬─ 基本查询（WHERE / LIKE / BETWEEN）
       │       ├─ 排序（ORDER BY）
       │       ├─ 分组（GROUP BY / HAVING）
       │       ├─ 多表（JOIN / 等值连接）
       │       ├─ 子查询（IN / EXISTS）
       │       └─ 分页（LIMIT）
       ├─ 约束 ─┬─ NOT NULL / UNIQUE / PRIMARY KEY
       │        ├─ FOREIGN KEY
       │        ├─ DEFAULT
       │        └─ CHECK
       ├─ 索引 ─┬─ 普通索引 / 唯一索引
       │        └─ 主键索引 / B+树原理
       ├─ 事务 ─┬─ ACID
       │        ├─ 四大隔离级别
       │        └─ MVCC 原理
       ├─ 视图（VIEW）
       ├─ 用户管理（CREATE USER / GRANT / REVOKE）
       └─ 函数 ─┬─ 聚合（COUNT/SUM/AVG/MAX/MIN）
                └─ 字符串（CONCAT/UCASE/SUBSTRING...）
```

### 十三、MySQL 面试八股

**1. 为什么用 DECIMAL 不用 DOUBLE 存钱？**
> DOUBLE 有精度误差，DECIMAL 是定点数，金融场景必须用 DECIMAL。

**2. 索引为什么能加速查询？**
> B+ 树结构让查找从全表扫描 O(n) 变成 O(log n)。

**3. 什么情况索引会失效？**
> - 对索引列使用函数：`WHERE SUBSTRING(name,1,1) = 'J'`
> - 类型隐式转换：`WHERE id = '123'`（id 是 INT）
> - 联合索引没遵循最左前缀
> - LIKE 以 % 开头

**4. 事务隔离级别怎么选？**
> - MySQL 默认 REPEATABLE READ，足够用
> - 高并发场景可降级 READ COMMITTED（牺牲可重复读换性能）
> - SERIALIZABLE 基本不用（性能极差）

**5. 视图和表的区别？**
> 表存数据，视图存 SQL 查询。视图不占物理空间（物化视图除外），本质是虚拟表。

---

## JDBC 编程

### 一、JDBC 概述

**概念：** JDBC（Java Database Connectivity）是 Java 访问数据库的标准 API，定义了一套接口（`Driver`、`Connection`、`Statement`、`ResultSet`），由各数据库厂商提供实现（驱动）。

- **本质：** JDBC 是桥接模式 — Java 程序通过 JDBC 接口操作数据库，不依赖具体数据库实现
- **工作流程：** 加载驱动 → 建立连接 → 创建语句 → 执行 SQL → 处理结果 → 关闭资源
- **常用接口：**
  - `Driver` / `DriverManager` — 驱动管理和连接获取
  - `Connection` — 数据库连接（事务管理）
  - `Statement` — SQL 语句执行（有 SQL 注入风险）
  - `PreparedStatement` — 预编译 SQL（防注入，推荐）
  - `ResultSet` — 查询结果集

### 二、五种连接方式

**概念：** JDBC 提供多种获取数据库连接的方式，从直接 new Driver() 到用 Properties 配置文件 + Class.forName()，逐步解耦和简化。

**方式1 — 直接创建 Driver 实例：**
```java
Driver driver = new com.mysql.cj.jdbc.Driver();
String url = "jdbc:mysql://localhost:3306/mysql";
Properties info = new Properties();
info.setProperty("user","root");
info.setProperty("password","srq7758258");
Connection conn = driver.connect(url, info);
```

**方式2 — 反射创建 Driver（解耦）：**
```java
Class aClass = Class.forName("com.mysql.cj.jdbc.Driver");
Driver driver = (Driver) aClass.newInstance();
Connection conn = driver.connect(url, info);
```

**方式3 — DriverManager 管理驱动：**
```java
Class aClass = Class.forName("com.mysql.cj.jdbc.Driver");
Driver driver = (Driver) aClass.newInstance();
DriverManager.registerDriver(driver);
Connection conn = DriverManager.getConnection(url, user, password);
```

**方式4 — Class.forName 自动注册（最常用）：**
```java
Class.forName("com.mysql.cj.jdbc.Driver");
Connection conn = DriverManager.getConnection(url, user, password);
```

**方式5 — 配置文件读取（生产推荐）：**
```java
Properties prop = new Properties();
prop.load(new FileInputStream("src//myProperties"));
String user = prop.getProperty("user");
String password = prop.getProperty("password");
String driver = prop.getProperty("driver");
String url = prop.getProperty("url");
Class.forName(driver);
Connection conn = DriverManager.getConnection(url, user, password);
```

### 三、Statement vs PreparedStatement

**概念：** `Statement` 和 `PreparedStatement` 都用于执行 SQL，但后者更安全更高效。

| 对比 | Statement | PreparedStatement |
|------|----------|-----------------|
| SQL 拼接 | 字符串拼接，易出错 | `?` 占位符，清晰 |
| SQL 注入 | ✅ 有风险 | ❌ 预编译防止注入 |
| 预编译 | 不预编译 | 预编译，重复执行更快 |
| 性能 | 每次执行编译 | 一次编译多次执行 |
| 适合场景 | 动态 DDL | CRUD 操作 |

**Statement（有 SQL 注入风险）：**
```java
Statement stmt = conn.createStatement();
String sql = "select * from actor where id = " + id;
ResultSet rs = stmt.executeQuery(sql);
```

**PreparedStatement（预编译，防注入）：**
```java
String sql = "select * from actor where id = ? and phone = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, username);  // 参数索引从1开始
ps.setString(2, password1);
ResultSet rs = ps.executeQuery();
```

### 四、JDBC CRUD 操作

**INSERT（增）：**
```java
String sql = "insert into actor values(null,?,?,?,?)";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, name);
ps.setString(2, sex);
ps.setDate(3, birthDate);
ps.setString(4, phone);
ps.executeUpdate();
```

**DELETE（删）：**
```java
String sql = "delete from account where user_account = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, username);
int rows = ps.executeUpdate();  // 返回影响行数
```

**UPDATE（改）：**
```java
String sql = "update actor set name = ? where id = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setString(1, "周星驰");
ps.setInt(2, 1);
ps.executeUpdate();
```

**SELECT（查）：**
```java
String sql = "select * from actor where id = ?";
PreparedStatement ps = conn.prepareStatement(sql);
ps.setInt(1, 1);
ResultSet rs = ps.executeQuery();
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    Date borndate = rs.getDate("borndate");
}
```

### 五、ResultSet → Java 对象

**概念：** `ResultSet` 是查询结果的游标，初始指向第 0 行（第一行之前）。每次 `next()` 移动游标到下一行并返回 boolean。通常将 ResultSet 的数据封装到 Java 对象（POJO/JavaBean）中。

```java
// Actor 实体类
public class Actor {
    private int id;
    private String name;
    private String sex;
    private LocalDateTime borndate;
    private String phone;
    // getter/setter/toString...
}

// ResultSet → ArrayList
ArrayList<Actor> actors = new ArrayList<>();
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
    String sex = rs.getString("sex");
    LocalDateTime borndate = rs.getTimestamp("borndate").toLocalDateTime();
    String phone = rs.getString("phone");
    actors.add(new Actor(id, name, sex, borndate, phone));
}
System.out.println(actors);
```

### 六、JDBC 工具类

**概念：** 将重复的 JDBC 代码（加载驱动、获取连接、关闭资源）抽取到工具类中，避免重复代码。使用静态代码块在类加载时初始化配置。

```java
public class jdbcutil {
    private static String user;
    private static String password;
    private static String url;
    private static String driver;

    static {
        Properties prop = new Properties();
        try {
            prop.load(new FileInputStream("src//myProperties"));
            user = prop.getProperty("username");
            password = prop.getProperty("password");
            url = prop.getProperty("url");
            driver = prop.getProperty("driverClassName");
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }

    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(url, user, password);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }

    public static void close(ResultSet rs, Connection conn, PreparedStatement ps) {
        // 关闭资源（逆序关闭）
    }
}
```

### 七、批处理（Batch）

**概念：** 批处理将多条 SQL 语句打包一次性发送到数据库执行，减少网络往返次数，大幅提升批量操作性能。

- **原理：** 攒一批 → 一次发送 → 数据库依次执行
- **开启批处理：** JDBC URL 加 `?rewriteBatchedStatements=true`
- **性能对比：** 逐条 INSERT（6 秒） vs 批量 INSERT（0.2 秒）

```java
Connection conn = jdbcutil.getConnection();
String sql = "insert into account values(?,?)";
PreparedStatement ps = conn.prepareStatement(sql);
for (int i = 0; i < 5000; i++) {
    ps.setString(1, "jack" + i);
    ps.setString(2, "666");
    ps.addBatch();          // 加入批处理
}
ps.executeBatch();           // 一次性执行
```

### 八、JDBC 事务管理

**概念：** JDBC 默认每条 SQL 自动提交（`setAutoCommit(true)`）。需要事务时手动设置 `setAutoCommit(false)`，然后 `commit()` 或 `rollback()`。

**转账案例（模拟马云转给马化腾 1000 元）：**
```java
Connection conn = jdbcutil.getConnection();
conn.setAutoCommit(false);    // 关闭自动提交，开启事务
try {
    String sql = "update test1 set amount = amount - ? where name = ?";
    PreparedStatement ps = conn.prepareStatement(sql);
    ps.setDouble(1, 1000.00);
    ps.setString(2, "马云");
    ps.executeUpdate();

    int i = 1 / 0;  // 模拟异常！

    String sql1 = "update test1 set amount = amount + ? where name = ?";
    ps = conn.prepareStatement(sql1);
    ps.setDouble(1, 1000.00);
    ps.setString(2, "马化腾");
    ps.executeUpdate();

    conn.commit();   // 提交事务
} catch (Exception e) {
    conn.rollback(); // 回滚事务
    throw new RuntimeException(e);
} finally {
    jdbcutil.close(null, conn, preparedStatement);
}
```

### 九、连接池

**概念：** 数据库连接池维护一组已创建的连接，应用程序从池中"借用"连接使用，用完"归还"而不是关闭。避免了频繁创建/销毁连接的开销。

- **无连接池：** 每次请求创建连接 → 执行 SQL → 关闭连接（慢）
- **有连接池：** 从池中取连接 → 执行 SQL → 归还到池（快 50~100 倍）
- **核心参数：** 初始大小、最大活跃数、最大等待时间、空闲超时

#### C3P0 连接池

```java
ComboPooledDataSource cp = new ComboPooledDataSource();
cp.setDriverClass(driverClassName);
cp.setJdbcUrl(url);
cp.setUser(username);
cp.setPassword(password);
cp.setInitialPoolSize(10);   // 初始连接数
cp.setMaxPoolSize(50);       // 最大连接数

Connection conn = cp.getConnection();
// 使用 conn...
conn.close();  // 实际是归还到池，不是真正关闭
```

#### Druid 连接池（阿里巴巴，推荐）

```java
Properties props = new Properties();
props.load(new FileInputStream("src//myProperties"));
DataSource dataSource = DruidDataSourceFactory.createDataSource(props);

Connection conn = dataSource.getConnection();
// 使用 conn...
conn.close();  // 归还到池
```

**性能对比（50 万次获取+归还连接）：**
- C3P0：较慢
- Druid：非常快（约 0.65 秒）

### 十、Apache DBUtils

**概念：** DBUtils 是 Apache 提供的 JDBC 封装工具，简化了 ResultSet → Java 对象的转换过程，避免手动写 while(rs.next()) 映射代码。

**核心组件：**
- `QueryRunner` — SQL 执行器
- `BeanHandler` — 单行结果 → 单个 JavaBean
- `BeanListHandler` — 多行结果 → List<JavaBean>
- `ScalarHandler` — 单行单列 → 单个值

```java
Connection conn = jdbcutil.getConnection();
QueryRunner qr = new QueryRunner();

// 查询单行 → Actor 对象
String sql = "select * from actor where id = ?";
Actor actor = qr.query(conn, sql, new BeanHandler<>(Actor.class), 2);
System.out.println(actor);

// 查询多行 → List<Actor>
List<Actor> list = qr.query(conn, "select * from actor", new BeanListHandler<>(Actor.class));

// 查询单值（如 name）
String sql = "select name from actor where id = ?";
Object name = qr.query(conn, sql, new ScalarHandler(), 2);
System.out.println(name);

// DML 操作
qr.update(conn, "update actor set name = ? where id = ?", "刘德华", 1);
qr.update(conn, "insert into actor values(null,?,?,?,?)", name, sex, born, phone);
qr.update(conn, "delete from actor where id = ?", 11);

jdbcutil.close(null, conn, null);
```

### 十一、JDBC 知识图谱

```
JDBC ─┬─ 连接 ─┬─ DriverManager / Driver
      │        ├─ Class.forName() 加载驱动
      │        ├─ 5种连接方式对比
      │        └─ Properties 配置文件解耦
      ├─ SQL执行 ─┬─ Statement（有注入风险）
      │           ├─ PreparedStatement（预编译，推荐）
      │           └─ CRUD（增删改查）
      ├─ 结果集 ─┬─ ResultSet 遍历
      │          └─ ResultSet → Java对象
      ├─ 事务管理 ─┬─ setAutoCommit(false)
      │            ├─ commit() / rollback()
      │            └─ 转账案例
      ├─ 批处理 ─┬─ addBatch()
      │          └─ executeBatch()
      ├─ 连接池 ─┬─ C3P0（ComboPooledDataSource）
      │          └─ Druid（推荐）
      ├─ DBUtils ─┬─ QueryRunner
      │           ├─ BeanHandler（单行）
      │           ├─ BeanListHandler（多行）
      │           ├─ ScalarHandler（单值）
      │           └─ update() DML操作
      └─ 工具类 ─┬─ 静态代码块加载配置
                 ├─ getConnection()
                 └─ close() 资源释放
```

---

## 附：完整知识图谱

```
JavaSE ─┬─ 01 OOP核心 ── 封装 / 继承 / 多态 / super
        ├─ 02 抽象接口 ── abstract / interface / 内部类 / enum / final
        ├─ 03 常用类 ──── String / StringBuilder / Arrays / 异常 / 日期
        ├─ 04 集合 ────── List / Set / Map / Collections
        ├─ 05 泛型 ────── 泛型类 / 泛型接口 / 通配符
        ├─ 06 多线程 ──── Thread / Runnable / Lock / 线程池
        ├─ 07 IO流 ────── 字节流 / 字符流 / 缓冲流
        ├─ 08 网络编程 ── TCP / UDP / Socket
        ├─ 10 反射 ────── Class对象 / Field / Method / Constructor
        └─ 数据库 ─┬─ MySQL ── DDL / DML / DQL / 事务 / 索引 / 视图
                   └─ JDBC ─── 连接 / PreparedStatement / 连接池 / DBUtils
```
