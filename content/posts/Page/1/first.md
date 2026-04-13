---
title: "First"
date: 2026-04-07T16:56:28+08:00
draft: false
---

> 本手册基于《菜鸟教程 Java 全册》及高校核心考点，对基础语法、数组、面向对象、常用API、异常处理进行了深度扩展，适合系统复习与背诵。

---

## 第一章 Java 语言基础（扩展详解）

### 1.1 Java 简介与开发环境

- **Java 三大平台**：
  - **Java SE**（标准版）：核心基础，用于桌面应用、通用程序。
  - **Java EE**（企业版）：基于SE，用于Web、企业级应用。
  - **Java ME**（微型版）：用于嵌入式设备（如旧手机、智能卡）。
- **Java 特点详解**：
  - **面向对象**：封装、继承、多态。
  - **平台无关**：一次编写，到处运行（通过JVM屏蔽底层差异）。
  - **健壮性**：强类型、垃圾回收（GC）、异常处理。
  - **多线程**：内置线程支持，适合高并发。
- **JDK / JRE / JVM 关系**：
  - **JVM**：执行 `.class` 字节码，是Java跨平台的核心。
  - **JRE** = JVM + 核心类库（如 `java.lang.*`，`java.util.*`）。
  - **JDK** = JRE + 开发工具（`javac.exe`，`java.exe`，`jar.exe`，`javadoc.exe` 等）。
- **编译与运行细节**：
  ```bash
  javac -d . HelloWorld.java   # 编译并指定输出目录（默认当前目录）
  java -cp . HelloWorld        # -cp 指定类路径，. 表示当前目录
  ```
  - 注意：类名必须与文件名完全相同（区分大小写）；若类属于包 `com.test`，则文件头部应有 `package com.test;`，运行时需在包根目录执行 `java com.test.HelloWorld`。
- **环境变量（Windows）**：
  - `JAVA_HOME`：`C:\Program Files\Java\jdk-17`
  - `Path`：添加 `%JAVA_HOME%\bin`
  - `CLASSPATH`（JDK 1.5+ 通常无需设置，但若手动设置，值为 `.;%JAVA_HOME%\lib`）

### 1.2 第一个 Java 程序结构深度解析

```java
/**
 * 文档注释：用于 javadoc 生成 API 文档
 * @author YourName
 * @version 1.0
 */
package com.example;          // 包声明（必须位于文件首行，注释之后）

import java.util.Scanner;     // 导入其他类

// 类声明：文件名必须为 HelloWorld.java
public class HelloWorld {
    // 静态变量（类变量）
    private static String message = "Hello";

    // 实例变量
    private int count;

    // 构造方法（默认无参构造器若不写，编译器自动提供；一旦写了其他构造器，默认无参消失）
    public HelloWorld() {
        count = 0;
    }

    // 程序入口 main 方法
    public static void main(String[] args) {
        // 标准输出
        System.out.println("Hello World");
        // 局部变量
        int x = 10;
        // 调用静态方法
        HelloWorld.show();
    }

    private static void show() {
        System.out.println(message);
    }
}
```

- **命名规范（必须遵守）**：
  - **包名**：全小写，域名倒置，如 `org.apache.commons`。
  - **类/接口名**：大驼峰，如 `ArrayList`，`HttpClient`。
  - **方法/变量名**：小驼峰，如 `getUserInfo()`，`studentName`。
  - **常量**：全大写下划线分隔，如 `MAX_LEVEL`，`DEFAULT_VALUE`。
  - **泛型类型参数**：单个大写字母，如 `T`，`E`，`K`，`V`。

### 1.3 基本数据类型（8种）—— 完整对比表

| 类型 | 关键字 | 字节数 | 取值范围 | 默认值 | 包装类 | 字面量示例 |
|:---|:---|:---|:---|:---|:---|:---|
| 字节型 | `byte` | 1 | -128 ~ 127 | 0 | `Byte` | `byte b = 100;` |
| 短整型 | `short` | 2 | -32768 ~ 32767 | 0 | `Short` | `short s = 30000;` |
| 整型 | `int` | 4 | -2^31 ~ 2^31-1 | 0 | `Integer` | `int i = 100000;` |
| 长整型 | `long` | 8 | -2^63 ~ 2^63-1 | 0L | `Long` | `long l = 100L;` (必须加L) |
| 单精度浮点 | `float` | 4 | 约 ±3.4E-38 ~ ±3.4E+38 | 0.0f | `Float` | `float f = 3.14f;` (必须加f) |
| 双精度浮点 | `double` | 8 | 约 ±1.8E-308 ~ ±1.8E+308 | 0.0d | `Double` | `double d = 3.1415;` (d可省略) |
| 字符型 | `char` | 2 | 0 ~ 65535 (Unicode) | '\u0000' | `Character` | `char c = 'A';` 或 `char c2 = 65;` |
| 布尔型 | `boolean` | 未明确(通常1字节) | true / false | false | `Boolean` | `boolean flag = true;` |

**重要细节**：
- `char` 可以直接赋整数（0~65535），输出时按 Unicode 字符输出。
- 浮点数运算可能产生精度丢失（如 `0.1 + 0.2`），商业计算使用 `BigDecimal`。
- 默认整数是 `int`，默认浮点是 `double`，因此 `float f = 3.14;` 会报错，必须写作 `3.14f`。

**类型转换规则**：
- **自动转换**（小→大）：`byte → short → int → long → float → double`；`char → int`。
  ```java
  int a = 100;
  long b = a;      // 自动转换
  double c = b;    // 自动转换
  ```
- **强制转换**（大→小）：可能丢失数据或精度。
  ```java
  double pi = 3.14159;
  int ipi = (int) pi;   // 结果为 3（截断，非四舍五入）
  int big = 300;
  byte small = (byte) big;   // 300 → 44 (溢出：300 - 256 = 44)
  ```
- **表达式类型提升**：当表达式中混合类型时，结果自动提升到最高级别类型。
  ```java
  byte a = 10, b = 20;
  byte c = a + b;   // 错误！a+b 被提升为 int，需要强制转换: (byte)(a+b)
  ```

### 1.4 标识符与关键字（完整列表）

- **标识符规则**：
  1. 由字母、数字、下划线 `_`、美元符 `$` 组成。
  2. 不能以数字开头。
  3. 不能是关键字或保留字（`goto`，`const` 是保留字，未使用）。
  4. 长度不限，大小写敏感。
- **常用关键字（共53个，含保留字）**：
  - 基本类型：`byte`, `short`, `int`, `long`, `float`, `double`, `char`, `boolean`
  - 访问控制：`public`, `protected`, `private`
  - 类、接口、对象：`class`, `interface`, `extends`, `implements`, `enum`, `new`
  - 方法控制：`abstract`, `static`, `final`, `synchronized`, `native`, `strictfp`
  - 流程控制：`if`, `else`, `switch`, `case`, `default`, `for`, `while`, `do`, `break`, `continue`, `return`
  - 异常处理：`try`, `catch`, `finally`, `throw`, `throws`
  - 其他：`this`, `super`, `package`, `import`, `instanceof`, `transient`, `volatile`, `assert`
  - 未使用保留字：`goto`, `const`

### 1.5 运算符与表达式（详细优先级表）

| 优先级 | 运算符 | 结合性 | 说明 |
|:---|:---|:---|:---|
| 1 | `()`, `[]`, `.` | 从左到右 | 括号、数组下标、成员访问 |
| 2 | `++`, `--`, `+`, `-`, `~`, `!` | 从右到左 | 单目（正负号、自增、取反） |
| 3 | `*`, `/`, `%` | 从左到右 | 乘除取模 |
| 4 | `+`, `-` | 从左到右 | 加减 |
| 5 | `<<`, `>>`, `>>>` | 从左到右 | 移位 |
| 6 | `<`, `<=`, `>`, `>=`, `instanceof` | 从左到右 | 关系 |
| 7 | `==`, `!=` | 从左到右 | 相等 |
| 8 | `&` | 从左到右 | 位与（非短路逻辑与） |
| 9 | `^` | 从左到右 | 位异或 |
| 10 | `\|` | 从左到右 | 位或（非短路逻辑或） |
| 11 | `&&` | 从左到右 | 短路逻辑与 |
| 12 | `\|\|` | 从左到右 | 短路逻辑或 |
| 13 | `?:` | 从右到左 | 三目条件 |
| 14 | `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `&=`, `\|=`, `^=`, `<<=`, `>>=`, `>>>=` | 从右到左 | 赋值 |

**常见考点**：
- **短路效应**：`&&` 左边为 `false` 时不计算右边；`||` 左边为 `true` 时不计算右边。
  ```java
  int a = 10;
  if (a > 5 && (a = 0) > 0) { }  // a 不会被赋值为 0，因为左边已 true? 不，左边 true，右边会执行！注意：a>5为true，所以右边会执行，a变成0。
  // 正确例子：避免空指针异常
  if (str != null && str.length() > 0) { }
  ```
- **自增自减**：
  ```java
  int i = 1;
  int j = i++;   // j=1, i=2
  int k = ++i;   // i=3, k=3
  ```
- **三目运算符嵌套**：`a > b ? (a > c ? a : c) : (b > c ? b : c)` 求三者最大值。
- **`instanceof`**：判断对象是否属于某个类（或其子类/实现类）。
  ```java
  String s = "hello";
  boolean b = s instanceof String;   // true
  ```

### 1.6 流程控制语句（扩展）

**if-else if-else**：
- 注意 `else` 与最近的 `if` 结合（避免悬空 else）。
- 建议始终使用 `{}` 包裹代码块，即使只有一条语句。

**switch-case**：
- **支持类型**：`byte`, `short`, `char`, `int`，及其包装类（自动拆箱），枚举（`enum`），`String`（JDK7+）。
- **穿透现象**：若 `case` 后没有 `break`，会继续执行下一个 `case`（直到 `break` 或 `switch` 结束）。
- **箭头语法（JDK12+）**：`case 1 -> System.out.println("one");` 无需 break。
- **yield（JDK13+）**：在 `case` 中返回一个值给 `switch` 表达式。
  ```java
  int num = 2;
  String result = switch (num) {
      case 1 -> "one";
      case 2 -> "two";
      default -> "unknown";
  };
  ```

**循环结构**：
- **while**：先判断后执行，可能一次都不执行。
- **do-while**：先执行一次再判断，至少执行一次。
- **for**：
  ```java
  for (int i = 0; i < 10; i++) { ... }
  ```
  - 初始化部分可以声明多个变量，用逗号隔开：`for (int i=0, j=0; i<10; i++, j+=2)`
- **增强 for**：
  ```java
  int[] arr = {1,2,3};
  for (int n : arr) { System.out.println(n); }   // 不能修改原数组元素（基本类型），引用类型可修改属性。
  ```

**跳转语句**：
- `break`：退出当前最内层循环或 `switch`。可带标签退出外层循环：
  ```java
  outer:
  for (int i = 0; i < 3; i++) {
      for (int j = 0; j < 3; j++) {
          if (i == 1 && j == 1) break outer;   // 直接跳出外层循环
      }
  }
  ```
- `continue`：跳过本次循环剩余语句，进入下一次迭代。也可带标签。
- `return`：结束当前方法，若返回类型为 `void` 可单独 `return;`。

---

## 第二章 数组（扩展详解）

### 2.1 数组的声明、创建与内存分配

```java
// 推荐风格：数据类型[] 数组名;
int[] arr1;          // 声明，栈中引用
arr1 = new int[5];   // 创建，堆中分配空间，默认值0

// 不推荐但合法的风格：int arr1[];

// 声明并创建
double[] arr2 = new double[10];

// 静态初始化（编译时确定元素）
String[] names = {"Alice", "Bob", "Charlie"};
// 等价于
String[] names2 = new String[]{"Alice", "Bob", "Charlie"};  // 匿名数组方式
```

- **内存细节**：
  - 数组变量（引用）存放在栈内存，指向堆中的数组对象。
  - 数组对象包含 `length` 属性以及连续的元素存储区。
  - 元素默认值：数值型 `0`，`char` 为 `'\u0000'`，`boolean` 为 `false`，引用类型为 `null`。

- **常见错误**：
  - `ArrayIndexOutOfBoundsException`：索引超出 [0, length-1]。
  - `NullPointerException`：数组引用为 `null` 时访问元素。

### 2.2 多维数组（以二维为例）

```java
// 规则二维数组：3行4列
int[][] matrix = new int[3][4];

// 访问与赋值
matrix[0][1] = 10;

// 不规则数组（锯齿数组）
int[][] jagged = new int[3][];
jagged[0] = new int[2];
jagged[1] = new int[3];
jagged[2] = new int[1];

// 静态初始化
int[][] arr = {
    {1, 2},
    {3, 4, 5},
    {6}
};
```

- **遍历多维数组**：嵌套循环或增强 for。
  ```java
  for (int[] row : matrix) {
      for (int val : row) {
          System.out.print(val + " ");
      }
      System.out.println();
  }
  ```

### 2.3 Arrays 工具类（全部常用方法）

| 方法签名 | 说明 | 示例 |
|:---|:---|:---|
| `static String toString(int[] a)` | 返回数组内容的字符串表示 | `Arrays.toString(arr)` → `"[1,2,3]"` |
| `static void sort(int[] a)` | 升序排序（优化后的快排/归并） | `Arrays.sort(arr)` |
| `static void sort(Object[] a, Comparator c)` | 自定义排序 | `Arrays.sort(strs, (s1,s2)->s1.length()-s2.length())` |
| `static int binarySearch(int[] a, int key)` | 二分查找，返回索引（要求已排序），找不到返回负插入点 `-(insertion point)-1` | `int idx = Arrays.binarySearch(arr, 5)` |
| `static void fill(int[] a, int val)` | 将数组所有元素赋值为 val | `Arrays.fill(arr, 0)` |
| `static boolean equals(int[] a, int[] a2)` | 比较两个数组内容是否相等 | `if (Arrays.equals(arr1, arr2))` |
| `static int[] copyOf(int[] original, int newLength)` | 复制数组，截断或补零 | `int[] copy = Arrays.copyOf(arr, 10)` |
| `static int[] copyOfRange(int[] original, int from, int to)` | 复制指定范围 `[from, to)` | `int[] part = Arrays.copyOfRange(arr, 1, 4)` |
| `static void parallelSort(int[] a)` | 并行排序（多线程，大数据时更快） | `Arrays.parallelSort(arr)` |
| `static Stream<int> stream(int[] array)` | 转为流式处理 | `Arrays.stream(arr).sum()` |

**二维数组的 toString**：`Arrays.deepToString(matrix)` 输出 `"[[1,2],[3,4]]"`。

**数组与集合转换**：
```java
// 数组转 List（注意：返回的 List 不可增删，但可修改元素）
List<String> list = Arrays.asList("A","B","C");
// 若要完全可变，使用 new ArrayList<>(Arrays.asList(arr))
// 集合转数组
String[] arr = list.toArray(new String[0]);
```

---

## 第三章 面向对象编程（核心重点·深度扩展）

### 3.1 类与对象的内存模型

- **类**：蓝图，定义属性和行为。
- **对象**：根据类创建的实例，存储在堆内存中。
- **引用**：栈内存中的变量，指向堆中的对象地址。

```java
Person p = new Person("Tom", 20);
```
1. `new Person(...)` 在堆中分配内存，初始化成员变量（默认值）。
2. 调用构造方法，执行初始化代码。
3. 将堆中对象的地址赋给栈中的变量 `p`。

### 3.2 构造方法深度解析

- **构造方法特点**：
  - 名称与类名相同，没有返回值（连 `void` 都不能写）。
  - 可以重载（参数列表不同）。
  - 如果程序员没有定义任何构造器，编译器自动生成一个无参构造器（内容为空）。
  - 如果定义了至少一个构造器，编译器不再自动生成无参构造器。
- **构造器链**：
  - 子类构造器第一行必须调用父类构造器（`super(...)`），如果没写，编译器自动添加 `super()`（调用父类无参构造器）。
  - 若父类没有无参构造器，则子类构造器必须显式 `super(参数)`。
  - `this(...)` 调用本类其他构造器，也必须在第一行。因此 `super` 和 `this` 不能同时出现。

```java
class Parent {
    Parent(int x) { }
}

class Child extends Parent {
    Child() {
        super(10);  // 必须显式调用，因为父类没有无参构造
    }
}
```

### 3.3 封装（Encapsulation）—— 信息隐藏

- **实现步骤**：
  1. 属性私有化：`private` 修饰。
  2. 提供公共的 `getter` 和 `setter` 方法。
  3. 在 `setter` 中加入业务逻辑校验。

- **优点**：数据安全、可维护性、便于增加控制逻辑。

```java
public class BankAccount {
    private double balance;
    
    public double getBalance() {
        return balance;
    }
    
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        } else {
            throw new IllegalArgumentException("余额不足");
        }
    }
}
```

### 3.4 继承（Inheritance）—— 代码复用

- **语法**：`class Child extends Parent`
- **特点**：
  - Java 单继承，一个子类只能有一个直接父类。
  - 所有类默认继承 `Object`（包括数组）。
  - 子类不能继承父类的 `private` 成员，但可以通过 `public/protected` 方法间接访问。
  - 子类可以重写父类非 `final`、非 `static` 的方法。

- **方法重写（Override）规则**：
  - 方法名、参数列表、返回值类型必须相同（返回值可以是子类型，即协变返回类型）。
  - 访问修饰符不能比父类更严格（如父类 `protected`，子类可以是 `public`，但不能是 `private`）。
  - 不能抛出比父类更宽泛的检查异常（运行时异常不受限）。
  - 使用 `@Override` 注解避免写错。

```java
class Animal {
    protected String name;
    public void speak() { System.out.println("动物叫"); }
}

class Cat extends Animal {
    @Override
    public void speak() { System.out.println("喵喵"); }
}
```

- **super 关键字**：
  - 访问父类成员：`super.name`，`super.speak()`。
  - 调用父类构造器：`super(参数)` 必须位于子类构造器第一行。

### 3.5 多态（Polymorphism）—— 动态绑定

- **前提**：继承 + 重写 + 父类引用指向子类对象。
- **编译时类型 vs 运行时类型**：
  - 编译时类型：声明引用的类型（左边）。
  - 运行时类型：实际对象的类型（右边）。
- **方法调用**：编译看左边（检查方法是否存在），运行看右边（执行子类重写的方法）。
- **成员变量访问**：编译和运行都看左边（变量没有多态）。

```java
Animal a = new Cat();
a.speak();   // 输出“喵喵”（多态）
System.out.println(a.name); // 访问 Animal 的 name，即使 Cat 也有同名变量
```

- **向上转型（Upcasting）**：自动，安全，丢失子类特有方法。
- **向下转型（Downcasting）**：强制，可能抛出 `ClassCastException`，建议先 `instanceof`。

```java
if (a instanceof Cat) {
    Cat c = (Cat) a;
    c.catchMouse();   // 调用子类特有方法
}
```

### 3.6 抽象类（Abstract Class）

- **定义**：使用 `abstract` 关键字修饰的类和方法。
- **特点**：
  - 抽象类不能实例化（不能 `new`）。
  - 抽象方法没有方法体，子类必须重写（除非子类也是抽象类）。
  - 抽象类可以拥有构造器（供子类调用）、成员变量、普通方法。
  - 抽象类可以没有抽象方法（但意义不大，通常用来阻止实例化）。

```java
abstract class Shape {
    protected String color;
    
    public Shape(String color) { this.color = color; }
    
    public abstract double area();   // 抽象方法
    
    public void display() {          // 普通方法
        System.out.println("颜色：" + color);
    }
}

class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}
```

### 3.7 接口（Interface）—— 完全抽象的行为规范

- **语法**：`interface 接口名 { ... }`，类实现使用 `implements`。
- **JDK 8 之前**：只能有 `public abstract` 方法和 `public static final` 常量。
- **JDK 8 新增**：
  - **默认方法**（`default`）：可以有方法体，实现类可重写也可不重写。用于接口演化。
  - **静态方法**（`static`）：直接通过接口名调用。
- **JDK 9 新增**：**私有方法**（`private`），供接口内部默认方法复用逻辑。

```java
interface Vehicle {
    int MAX_SPEED = 120;   // 等价于 public static final int MAX_SPEED = 120;
    
    void start();           // 抽象方法，等价于 public abstract void start();
    
    default void stop() {   // 默认方法
        System.out.println("车辆停止");
        privateHelper();
    }
    
    static void honk() {    // 静态方法
        System.out.println("鸣笛");
    }
    
    private void privateHelper() {   // 私有方法
        System.out.println("内部辅助");
    }
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("汽车启动");
    }
}
```

- **多实现**：一个类可以实现多个接口，用逗号分隔。
- **接口多继承**：接口可以 `extends` 多个接口。

```java
interface A { void a(); }
interface B { void b(); }
interface C extends A, B { void c(); }   // 多继承
class D implements C {
    public void a() {}
    public void b() {}
    public void c() {}
}
```

### 3.8 抽象类 vs 接口（全面对比表）

| 特性 | 抽象类 | 接口 |
|:---|:---|:---|
| 关键字 | `abstract class` | `interface` |
| 实例化 | 不可以 | 不可以 |
| 构造器 | 可以有 | 不能有 |
| 成员变量 | 任意修饰符（`private`，`protected`，`public`，`static`，`final`） | 只能是 `public static final`（常量） |
| 普通方法 | 可以有具体实现 | JDK8 前无，JDK8+ 可以有 `default`/`static` 方法 |
| 抽象方法 | 使用 `abstract` 关键字 | 隐式 `public abstract`（不用写） |
| 访问修饰符 | 灵活（`protected`，`public` 等） | 接口中方法默认 `public`（不能 `protected`/`private`，JDK9 私有方法除外） |
| 继承/实现 | 单继承（`extends`） | 多实现（`implements`），接口多继承（`extends` 多个接口） |
| 使用场景 | 模板设计（is-a 关系，有公共代码） | 行为规范（has-a / can-do 关系，多角色） |

### 3.9 修饰符详解（含 static 与 final）

**static（静态）**：
- **静态变量**：类变量，所有对象共享，内存中只一份。可通过类名或对象名访问（推荐类名）。
- **静态方法**：不能访问实例成员（因为没有 `this`），可直接通过类名调用。
- **静态代码块**：类加载时执行一次，常用于初始化静态资源。
  ```java
  class Demo {
      static int count;
      static {
          count = 100;
          System.out.println("静态块执行");
      }
  }
  ```
- **静态内部类**：可以不依赖外部类实例，只能访问外部类的静态成员。

**final（最终）**：
- **final 类**：不能被继承（如 `String`，`Math`）。
- **final 方法**：不能被重写。
- **final 变量**：只能赋值一次，即常量。基本类型值不可变，引用类型地址不可变（但对象内容可变）。
  ```java
  final int[] arr = {1,2,3};
  arr[0] = 10;   // 允许，因为 arr 引用未变
  // arr = new int[5];  // 错误，引用不可变
  ```
- **空白 final**：声明时不赋值，必须在构造器中完成赋值。

**权限修饰符可见性表**：

| 修饰符 | 本类 | 同包类 | 子类（不同包） | 任意类 |
|:---|:---|:---|:---|:---|
| `private` | ✓ | ✗ | ✗ | ✗ |
| (默认) | ✓ | ✓ | ✗ | ✗ |
| `protected` | ✓ | ✓ | ✓ | ✗ |
| `public` | ✓ | ✓ | ✓ | ✓ |

---

## 第四章 核心类库与常用 API（扩展详解）

### 4.1 String 类（不可变字符序列）—— 深度剖析

- **不可变性**：`String` 对象一旦创建，其内容不可改变。任何修改（如 `concat`，`replace`，`substring`）都会产生新的 `String` 对象。
- **字符串常量池**：
  - 字面量（`String s = "hello"`）直接放入常量池（堆中，JDK7+ 从方法区移至堆）。
  - `new String("hello")` 会先在堆中创建对象，然后检查常量池是否有 `"hello"`，若没有则创建一份，但对象引用指向堆中的实例。
  - `intern()` 方法：手动将字符串对象放入常量池，返回常量池中的引用。

```java
String s1 = "abc";
String s2 = "abc";
String s3 = new String("abc");
System.out.println(s1 == s2);   // true，同一常量池引用
System.out.println(s1 == s3);   // false，堆中不同对象
System.out.println(s1.equals(s3)); // true，内容相同
```

- **String 常用方法（扩展）**：

| 方法 | 示例 | 说明 |
|:---|:---|:---|
| `length()` | `"abc".length()` → 3 | 返回字符数（非字节数） |
| `charAt(int index)` | `"abc".charAt(1)` → `'b'` | 索引从 0 开始 |
| `toCharArray()` | `"abc".toCharArray()` → `{'a','b','c'}` | 转为字符数组 |
| `equals(Object obj)` | 比较内容，区分大小写 | 必须用此方法比较内容 |
| `equalsIgnoreCase(String)` | `"ABC".equalsIgnoreCase("abc")` → true | 忽略大小写 |
| `compareTo(String)` | `"abc".compareTo("abd")` → -1 | 字典序，返回差值 |
| `indexOf(String/char)` | `"hello".indexOf('l')` → 2；`indexOf("ll")` → 2 | 首次出现位置，未找到 -1 |
| `lastIndexOf(...)` | 最后一次出现位置 | |
| `substring(int begin)` | `"hello".substring(2)` → `"llo"` | 从 begin 到末尾 |
| `substring(int begin, int end)` | `"hello".substring(1,3)` → `"el"` | `[begin, end)` |
| `replace(char old, char new)` | `"hello".replace('l','w')` → `"hewwo"` | 替换所有匹配字符 |
| `replaceAll(String regex, String replacement)` | 支持正则表达式 | `"a1b2c".replaceAll("\\d","-")` → `"a-b-c"` |
| `split(String regex)` | `"a,b,c".split(",")` → `["a","b","c"]` | 分割成数组 |
| `trim()` | `"  hi  ".trim()` → `"hi"` | 去除首尾空白字符（≤空格） |
| `strip()` (JDK11) | 去除 Unicode 空白字符 | 比 `trim()` 更全面 |
| `toLowerCase()` / `toUpperCase()` | 大小写转换 | 注意 locale 问题 |
| `startsWith(String prefix)` / `endsWith(String suffix)` | 判断前缀/后缀 | |
| `isEmpty()` | 判断是否长度为 0 | |
| `isBlank()` (JDK11) | 判断是否空白（空格、制表符等） | |
| `join(CharSequence delimiter, CharSequence... elements)` | `String.join("-", "a","b","c")` → `"a-b-c"` | 静态方法 |
| `valueOf(任意类型)` | `String.valueOf(123)` → `"123"` | 基本类型/对象转字符串 |

### 4.2 StringBuilder 与 StringBuffer

- **区别**：
  - `StringBuffer` 是线程安全的（方法用 `synchronized` 修饰），效率较低，适用于多线程环境。
  - `StringBuilder` 非线程安全，效率高，优先使用（单线程）。
- **常用方法**（两者相同）：

```java
StringBuilder sb = new StringBuilder();        // 默认容量16
sb.append("Hello");                            // 追加
sb.append(" ").append("World");                // 链式调用
sb.insert(5, ",");                             // 在索引5插入","
sb.delete(5, 6);                               // 删除一个字符
sb.replace(0, 5, "Hi");                        // 替换 [0,5) 为 "Hi"
sb.reverse();                                  // 反转
String result = sb.toString();                 // 转为不可变 String
int len = sb.length();                         // 长度
sb.setLength(3);                               // 截断或补空字符
```

- **扩容机制**：当容量不足时，新容量 = 旧容量 * 2 + 2，若仍不足则直接扩展至所需大小。

### 4.3 包装类与自动装箱/拆箱

- **装箱**：基本类型 → 包装类对象（`Integer i = 10;` 编译器调用 `Integer.valueOf(10)`）。
- **拆箱**：包装类对象 → 基本类型（`int n = i;` 编译器调用 `i.intValue()`）。
- **缓存池**：
  - `Integer` 缓存 `-128 ~ 127`（可通过 `-XX:AutoBoxCacheMax` 调整上限），在这个范围内的 `valueOf()` 返回缓存对象，`==` 比较可能为 `true`。
  - `Byte`, `Short`, `Long` 也缓存 `-128~127`；`Character` 缓存 `0~127`；`Boolean` 缓存 `TRUE` 和 `FALSE`。

```java
Integer a = 100;   // 使用缓存
Integer b = 100;
System.out.println(a == b);   // true

Integer c = 200;
Integer d = 200;
System.out.println(c == d);   // false，因为超出缓存范围，创建新对象
```

- **包装类常用方法**：
  - `parseXxx(String s)`：字符串转基本类型（抛出 `NumberFormatException`）。
  - `valueOf(String s)`：字符串转包装类。
  - `XxxValue()`：包装类转基本类型。
  - `compareTo()`：比较两个包装类对象。

### 4.4 Math 类（全静态方法）

| 方法 | 说明 | 示例 |
|:---|:---|:---|
| `abs(x)` | 绝对值 | `Math.abs(-5)` → 5 |
| `max(a,b)` / `min(a,b)` | 最大值/最小值 | `Math.max(3,5)` → 5 |
| `pow(a,b)` | a 的 b 次幂 | `Math.pow(2,3)` → 8.0 |
| `sqrt(x)` | 平方根 | `Math.sqrt(9)` → 3.0 |
| `cbrt(x)` | 立方根 | `Math.cbrt(27)` → 3.0 |
| `random()` | 随机数 [0.0, 1.0) | `(int)(Math.random()*100)` → 0~99 |
| `round(x)` | 四舍五入（返回 long） | `Math.round(3.5)` → 4 |
| `floor(x)` | 向下取整 | `Math.floor(3.9)` → 3.0 |
| `ceil(x)` | 向上取整 | `Math.ceil(3.1)` → 4.0 |
| `sin/cos/tan` | 三角函数（参数弧度） | `Math.sin(Math.PI/2)` → 1.0 |
| `toRadians(degrees)` | 角度转弧度 | |
| `log(x)` | 自然对数 | |
| `log10(x)` | 常用对数 | |

### 4.5 日期时间类（经典版与新 API 对比）

#### 旧版（java.util.Date, Calendar, SimpleDateFormat）—— 线程不安全，需注意

- **Date**：表示特定瞬间，精确到毫秒。
  ```java
  Date now = new Date();              // 当前时间
  Date future = new Date(1000000L);   // 从1970-1-1开始的毫秒数
  System.out.println(now.getTime());  // 获取毫秒数
  ```
- **SimpleDateFormat**：格式化和解析，线程不安全（使用时应加锁或创建新实例）。
  ```java
  SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
  String formatted = sdf.format(new Date());
  Date parsed = sdf.parse("2025-12-31 23:59:59");
  ```
- **Calendar**：抽象类，用于日期字段计算。
  ```java
  Calendar cal = Calendar.getInstance();   // 默认当前时间
  cal.set(2025, Calendar.JANUARY, 1);      // 月份从0开始！1月是0
  int year = cal.get(Calendar.YEAR);
  int month = cal.get(Calendar.MONTH) + 1; // 需+1
  cal.add(Calendar.DAY_OF_MONTH, 10);      // 加10天
  ```

#### 新版（java.time，JDK8+）—— 推荐使用

- **LocalDate**：日期（年-月-日）
- **LocalTime**：时间（时:分:秒.纳秒）
- **LocalDateTime**：日期+时间
- **DateTimeFormatter**：线程安全，格式化。

```java
LocalDate today = LocalDate.now();
LocalDate specific = LocalDate.of(2025, 1, 1);   // 月份正常1-12
LocalDateTime dt = LocalDateTime.now();
DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy/MM/dd HH:mm:ss");
String str = dt.format(dtf);
LocalDateTime parsed = LocalDateTime.parse("2025-01-01T12:00:00"); // 默认ISO格式
```

### 4.6 正则表达式（Pattern & Matcher）扩展

- **常用元字符**：
  - `\d` 数字，`\D` 非数字
  - `\w` 单词字符（字母、数字、下划线），`\W` 非单词字符
  - `\s` 空白字符（空格、制表符、换行等），`\S` 非空白
  - `.` 任意字符（除换行外，若需匹配换行用 `(?s)` 模式）
- **量词**：
  - `*` 0次或多次
  - `+` 1次或多次
  - `?` 0次或1次
  - `{n}` 恰好n次
  - `{n,}` 至少n次
  - `{n,m}` n到m次
- **边界匹配**：
  - `^` 行首，`$` 行尾
  - `\b` 单词边界
- **分组与捕获**：
  - `(pattern)` 捕获组，`\1` 反向引用，`(?:pattern)` 非捕获组

```java
// 邮箱验证简单示例
String email = "user@example.com";
String regex = "^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$";
boolean matches = email.matches(regex);   // 便捷方法

// 使用 Pattern 和 Matcher 提取信息
Pattern p = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
Matcher m = p.matcher("日期：2025-01-15");
if (m.find()) {
    String year = m.group(1);   // 2025
    String month = m.group(2);  // 01
    String day = m.group(3);    // 15
}
```

---

## 第五章 异常处理（扩展详解）

### 5.1 异常体系完整图

```
Throwable (根类)
├── Error (严重错误，程序无法处理，如 OutOfMemoryError, StackOverflowError, VirtualMachineError)
│   └── 系统内部错误、资源耗尽等，不要求捕获
└── Exception
    ├── RuntimeException (非检查异常，unchecked)
    │   ├── NullPointerException (空指针)
    │   ├── ArithmeticException (除零等)
    │   ├── ArrayIndexOutOfBoundsException (数组越界)
    │   ├── ClassCastException (类型转换异常)
    │   ├── IllegalArgumentException (非法参数)
    │   └── NumberFormatException (数字格式错误，是 IllegalArgumentException 的子类)
    └── 检查异常 (checked)
        ├── IOException (输入输出，如 FileNotFoundException)
        ├── SQLException (数据库)
        ├── ClassNotFoundException (类未找到)
        ├── InterruptedException (线程中断)
        └── 自定义检查异常 (extends Exception)
```

- **检查异常**：编译器强制要求处理（`try-catch` 或 `throws`），否则编译错误。
- **非检查异常**：运行时异常，编译器不强制处理，但通常应由程序员避免（如检查 null、边界）。

### 5.2 异常处理机制

**try-catch-finally 完整语法**：
```java
try {
    // 可能抛出异常的代码
} catch (SpecificException e) {
    // 处理特定异常
    e.printStackTrace();  // 打印堆栈信息
} catch (AnotherException e) {
    // 可以有多个 catch 块，子类异常在前
} finally {
    // 无论是否发生异常，都会执行（除非 JVM 退出或 finally 块中抛异常）
    // 用于释放资源（关闭文件、数据库连接等）
}
```

- **try-with-resources (JDK7+)**：自动关闭实现了 `AutoCloseable` 的资源。
  ```java
  try (FileInputStream fis = new FileInputStream("file.txt");
       BufferedReader br = new BufferedReader(new InputStreamReader(fis))) {
      String line = br.readLine();
  } catch (IOException e) {
      e.printStackTrace();
  }   // 无需 finally，资源自动关闭
  ```

### 5.3 throws 与 throw

- **throws**：声明方法可能抛出的异常（告知调用者处理）。
  ```java
  public void readFile(String path) throws IOException, NumberFormatException {
      // 可以抛出多个异常，用逗号分隔
  }
  ```
- **throw**：手动抛出异常对象。
  ```java
  if (age < 0 || age > 150) {
      throw new IllegalArgumentException("年龄必须在0-150之间");
  }
  ```

### 5.4 自定义异常

- 步骤：
  1. 继承 `Exception`（检查异常）或 `RuntimeException`（非检查异常）。
  2. 提供带消息参数的构造器，调用父类构造器。
  3. 可选提供无参构造器。

```java
// 自定义检查异常
class InsufficientFundsException extends Exception {
    public InsufficientFundsException() {
        super();
    }
    public InsufficientFundsException(String message) {
        super(message);
    }
}

// 使用
public void withdraw(double amount) throws InsufficientFundsException {
    if (amount > balance) {
        throw new InsufficientFundsException("余额不足，需要" + amount + "，当前余额" + balance);
    }
    balance -= amount;
}
```

### 5.5 异常处理最佳实践

1. **不要吞掉异常**：至少打印日志或向上抛出。
2. **捕获具体异常**，不要直接 `catch (Exception e)` 或 `catch (Throwable t)`。
3. **finally 块中不要返回或抛出异常**，否则会覆盖正常返回或原始异常。
4. **使用 try-with-resources** 代替手动关闭资源。
5. **抛出的异常应包含有意义的消息**，便于调试。
6. **不要在循环中使用 `try-catch`**，尽量将循环放在 `try` 内部。

```java
// 错误：吞掉异常
try {
    // ...
} catch (Exception e) { 
    // 什么都没做 
}

// 正确：记录日志或重新抛出
catch (Exception e) {
    logger.error("发生错误", e);
    throw new MyAppException("处理失败", e);
}
```

### 5.6 常见异常示例

| 异常 | 出现场景 |
|:---|:---|
| `NullPointerException` | 调用 `null` 对象的方法或属性 |
| `ArrayIndexOutOfBoundsException` | 访问数组索引超出范围 |
| `ArithmeticException` | 整数除以零 |
| `NumberFormatException` | `Integer.parseInt("abc")` |
| `ClassCastException` | `(String) new Integer(1)` |
| `IllegalArgumentException` | 方法参数不合法 |
| `FileNotFoundException` | 文件不存在或无法访问 |
| `IOException` | 读取/写入失败 |

---
根据你的复习手册内容，我为你配套设计了一套完整的自测题，涵盖前五章核心知识点，题型与《java复习题.pdf》风格一致但内容独立，便于自我检测和巩固。

---

## Java 程序设计自测题（基于前五章扩展详解版）

### 一、选择题

1. 下列关于 Java 基本数据类型的描述，正确的是（ ）  
A. `float` 类型的字面量默认是 `double`，因此 `float f = 3.14;` 可以正常编译  
B. `char` 类型占用 1 个字节，取值范围是 0 ~ 255  
C. 自动类型转换的顺序是 `byte → short → int → long → float → double`  
D. `boolean` 类型的默认值是 `true`

2. 以下代码的输出结果是（ ）  
```java
int a = 10;
int b = a++ + ++a;
System.out.println(b);
```
A. 21  
B. 22  
C. 23  
D. 20

3. 关于 `static` 关键字的描述，错误的是（ ）  
A. 静态方法中可以直接访问实例变量  
B. 静态代码块在类加载时执行且只执行一次  
C. 静态变量属于类，被所有对象共享  
D. 静态内部类可以不依赖外部类实例而创建

4. 下列代码中，会发生编译错误的是（ ）  
```java
class Parent {
    Parent(int x) { }
}
class Child extends Parent {
    Child() { }
}
```
A. `Parent` 类缺少无参构造方法  
B. `Child` 类的构造方法没有显式调用 `super(int)`  
C. `Child` 类不能继承 `Parent` 类  
D. 没有错误

5. 关于抽象类和接口的描述，正确的是（ ）  
A. 抽象类中可以没有抽象方法，但抽象方法必须定义在抽象类中  
B. 接口中的方法默认都是 `public static` 的  
C. 一个类可以实现多个接口，也可以继承多个抽象类  
D. JDK 8 之后接口中可以定义静态方法和默认方法

6. 以下关于 `String` 的说法，错误的是（ ）  
A. `String` 对象一旦创建，其内容不可改变  
B. 通过 `new String("hello")` 创建的对象，一定会放入字符串常量池  
C. `"abc".equals("abc")` 返回 `true`  
D. `StringBuilder` 是线程不安全的，`StringBuffer` 是线程安全的

7. 阅读以下代码，输出结果为（ ）  
```java
Integer i1 = 100;
Integer i2 = 100;
Integer i3 = 200;
Integer i4 = 200;
System.out.println(i1 == i2);
System.out.println(i3 == i4);
```
A. `true` `true`  
B. `true` `false`  
C. `false` `true`  
D. `false` `false`

8. 关于异常处理，描述正确的是（ ）  
A. `Error` 类属于检查异常，必须被捕获或声明抛出  
B. `finally` 块中的代码在任何情况下都会执行  
C. 多个 `catch` 块时，父类异常应该写在子类异常之前  
D. `try-with-resources` 可以自动关闭实现了 `Closeable` 接口的资源

9. 下列方法定义中，属于方法重写（Override）的是（ ）  
A. 在同一个类中定义两个同名方法，参数列表不同  
B. 子类中定义一个与父类同名、同参数列表、返回值相同的方法  
C. 子类中定义一个与父类同名、同参数列表、但返回值不同的方法  
D. 父类 `private` 方法在子类中重新定义

10. 以下代码的输出结果是（ ）  
```java
class A {
    String s = "A";
    void print() { System.out.print(s); }
}
class B extends A {
    String s = "B";
    void print() { System.out.print(s); }
}
public class Test {
    public static void main(String[] args) {
        A obj = new B();
        System.out.print(obj.s);
        obj.print();
    }
}
```
A. `AA`  
B. `AB`  
C. `BA`  
D. `BB`

---

### 二、填空题

1. Java 源文件编译后生成的字节码文件扩展名是 `.class`，执行字节码的命令是 `________`。

2. 面向对象程序设计的四个主要特征是：抽象性、________、继承性、________。

3. Java 中，使用 `________` 关键字定义常量；使用 `________` 关键字修饰的方法不能被子类重写。

4. 数组工具类 `Arrays` 中，用于将数组内容转换为字符串的静态方法是 `________`，用于对数组进行升序排序的方法是 `________`。

5. 接口中定义的成员变量默认修饰符是 `________`，成员方法默认修饰符是 `________`。

6. `Math.random()` 方法返回一个 `[0.0, 1.0)` 之间的 `________` 类型随机数。

7. 异常体系的总父类是 `________`，它有两个子类：`________` 和 `Exception`。

8. 子类构造方法中，调用父类构造方法的关键字是 `________`，且该调用必须位于构造方法的 `________` 行。

9. 字符串类中，用于比较内容是否相等（忽略大小写）的方法是 `________`；用于按指定正则表达式分割字符串的方法是 `________`。

10. `int[] arr = {1, 2, 3};` 则 `arr.length` 的值是 `________`，数组最后一个元素的索引是 `________`。

---

### 三、判断题（正确的打“√”，错误的打“×”）

1. （ ）一个 `.java` 源文件中可以包含多个 `public` 修饰的类。  
2. （ ）`protected` 修饰的成员可以被同一包中的类和其他包中的子类访问。  
3. （ ）`final` 修饰的变量一旦赋值就不能改变，因此 `final int[] arr = {1,2,3}; arr[0] = 5;` 是错误的。  
4. （ ）接口之间可以通过 `extends` 实现多继承。  
5. （ ）抽象类中可以定义构造方法，但抽象方法不能有方法体。  
6. （ ）`StringBuilder` 的 `append` 方法返回的是新的 `StringBuilder` 对象，原对象内容不变。  
7. （ ）`catch` 多个异常时，必须先捕获子类异常，再捕获父类异常。  
8. （ ）静态方法中可以直接使用 `this` 关键字。  
9. （ ）`Integer.parseInt("123")` 可能抛出 `ArithmeticException`。  
10. （ ）`instanceof` 运算符用于判断一个对象是否是某个类（或其子类）的实例。

---

### 四、程序阅读题（写出运行结果）

1. 阅读以下程序，写出输出结果。
```java
class Parent {
    static { System.out.print("A"); }
    { System.out.print("B"); }
    public Parent() { System.out.print("C"); }
}
class Child extends Parent {
    static { System.out.print("D"); }
    { System.out.print("E"); }
    public Child() { System.out.print("F"); }
}
public class Test {
    public static void main(String[] args) {
        new Child();
    }
}
```

2. 阅读以下程序，写出输出结果。
```java
public class Test {
    public static void main(String[] args) {
        String s1 = "hello";
        String s2 = new String("hello");
        String s3 = "he" + "llo";
        System.out.println(s1 == s2);
        System.out.println(s1 == s3);
        System.out.println(s1.equals(s2));
    }
}
```

3. 阅读以下程序，写出输出结果。
```java
class Animal {
    public void speak() { System.out.println("动物叫"); }
}
class Dog extends Animal {
    public void speak() { System.out.println("汪汪"); }
    public void run() { System.out.println("狗跑"); }
}
public class Test {
    public static void main(String[] args) {
        Animal a = new Dog();
        a.speak();
        // a.run();   // 如果取消注释，会编译错误吗？
    }
}
```

4. 阅读以下程序，写出输出结果。
```java
public class Test {
    public static int test() {
        int x = 1;
        try {
            x = 2;
            return x;
        } catch (Exception e) {
            x = 3;
            return x;
        } finally {
            x = 4;
        }
    }
    public static void main(String[] args) {
        System.out.println(test());
    }
}
```

---

### 五、编程题

1. **设计一个学生类**  
定义一个 `Student` 类，包含私有属性：姓名 `name`（String）、年龄 `age`（int）、成绩 `score`（double）。  
要求：
- 提供无参构造方法和带全部参数的有参构造方法。
- 为每个属性提供 `getter` 和 `setter` 方法，其中年龄必须在 0~150 之间，成绩必须在 0~100 之间，否则抛出 `IllegalArgumentException`。
- 重写 `toString()` 方法，返回格式：`"姓名：xxx，年龄：xx，成绩：xx.x"`。
- 编写 `main` 方法测试该类的使用。

2. **自定义异常与银行账户类**  
定义自定义检查异常 `InsufficientFundsException`，包含一个带消息参数的构造方法。  
定义 `BankAccount` 类，包含私有属性 `balance`（double），提供：
- 有参构造方法初始化余额。
- `deposit(double amount)` 存款方法（金额必须大于 0，否则抛出 `IllegalArgumentException`）。
- `withdraw(double amount)` 取款方法，当取款金额大于余额时，抛出 `InsufficientFundsException`。
- 编写测试类，演示存款、取款及异常捕获。

3. **多态与抽象类应用**  
定义抽象类 `Shape`，包含抽象方法 `double area()` 和 `double perimeter()`。  
定义两个子类：
- `Circle`（圆形）：属性 `radius`，实现面积和周长计算。
- `Rectangle`（矩形）：属性 `width` 和 `height`，实现面积和周长计算。  
编写测试类，使用 `Shape` 引用分别指向 `Circle` 和 `Rectangle` 对象，调用 `area()` 和 `perimeter()` 方法输出结果。

---

## 参考答案与解析

### 一、选择题
1. **C**  
   A 错，`float f = 3.14f;` 必须加 `f`；B 错，`char` 占 2 字节；D 错，`boolean` 默认 `false`。

2. **B**  
   `a++` 先取值后自增，此时 `b = 10 + (++a)`，`a` 自增后为 12，因此 `b = 10 + 12 = 22`。

3. **A**  
   静态方法中没有 `this`，不能直接访问实例变量，只能访问静态成员。

4. **B**  
   父类定义了有参构造器，默认无参构造器消失；子类构造器默认调用 `super()`，但父类没有无参构造器，必须显式调用 `super(int)`。

5. **D**  
   A 错，抽象方法只能定义在抽象类或接口中；B 错，接口方法默认是 `public abstract`；C 错，Java 单继承。

6. **B**  
   `new String("hello")` 会在堆中创建新对象，并检查常量池，但返回的引用指向堆对象，不一定是常量池中的。

7. **B**  
   `Integer` 缓存了 -128~127，100 在范围内返回同一对象，200 不在范围内，各自创建新对象。

8. **D**  
   A 错，`Error` 是非检查异常；B 错，若 JVM 退出或 `finally` 中抛异常，可能不执行；C 错，子类异常在前。

9. **B**  
   A 是重载；C 返回值类型不同不是重写；D 父类 `private` 方法对子类不可见，不算重写。

10. **B**  
    成员变量没有多态性，`obj.s` 访问的是编译时类型 `A` 的 `s` 字段，值为 `"A"`；方法有多态性，执行 `B` 的 `print` 输出 `"B"`，故结果为 `AB`。

---

### 二、填空题
1. `java`  
2. 封装性、多态性  
3. `final`、`final`  
4. `Arrays.toString()`、`Arrays.sort()`  
5. `public static final`、`public abstract`  
6. `double`  
7. `Throwable`、`Error`  
8. `super`、第一  
9. `equalsIgnoreCase()`、`split()`  
10. `3`、`2`

---

### 三、判断题
1. ×（只能有一个 `public` 类）  
2. √  
3. ×（`final` 修饰引用类型时，引用地址不可变，但对象内容可变，因此修改数组元素是允许的）  
4. √  
5. √  
6. ×（`append` 返回的是 `this`，即原对象自身，支持链式调用）  
7. √  
8. ×（静态方法没有 `this`）  
9. ×（可能抛出 `NumberFormatException`，属于 `IllegalArgumentException` 子类）  
10. √

---

### 四、程序阅读题

1. **输出结果：** `ADBCEF`  
   **解析：**  
   - 类加载时先执行父类静态块（A），再执行子类静态块（D）  
   - 创建子类对象时，先执行父类实例初始化块（B）和构造器（C），再执行子类实例初始化块（E）和构造器（F）  
   - 顺序：A → D → B → C → E → F

2. **输出结果：**  
   ```
   false
   true
   true
   ```
   **解析：**  
   - `s1` 指向常量池 `"hello"`，`s2` 指向堆中新对象，`==` 比较地址为 `false`  
   - `s3` 是编译期常量折叠，同样指向常量池中的 `"hello"`，与 `s1` 地址相同，为 `true`  
   - `equals` 比较内容，均为 `"hello"`，返回 `true`

3. **输出结果：** `汪汪`  
   若取消注释，编译错误（父类引用不能调用子类特有方法）。

4. **输出结果：** `2`  
   **解析：**  
   `try` 块中 `return x` 时，会将返回值暂存（值为 2），然后执行 `finally` 块，`x` 被改为 4，但返回值已在 `finally` 执行前确定，因此返回 2。

---

### 五、编程题（参考代码）

1. **Student 类**
```java
public class Student {
    private String name;
    private int age;
    private double score;

    public Student() { }

    public Student(String name, int age, double score) {
        this.name = name;
        setAge(age);
        setScore(score);
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return age; }
    public void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("年龄必须在0-150之间");
        }
        this.age = age;
    }

    public double getScore() { return score; }
    public void setScore(double score) {
        if (score < 0 || score > 100) {
            throw new IllegalArgumentException("成绩必须在0-100之间");
        }
        this.score = score;
    }

    @Override
    public String toString() {
        return String.format("姓名：%s，年龄：%d，成绩：%.1f", name, age, score);
    }

    public static void main(String[] args) {
        Student s = new Student("张三", 20, 88.5);
        System.out.println(s);
    }
}
```

2. **自定义异常与银行账户**
```java
class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}

class BankAccount {
    private double balance;

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("存款金额必须大于0");
        }
        balance += amount;
    }

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount > balance) {
            throw new InsufficientFundsException("余额不足，当前余额：" + balance);
        }
        balance -= amount;
    }

    public double getBalance() { return balance; }
}

// 测试
public class TestBank {
    public static void main(String[] args) {
        BankAccount account = new BankAccount(1000);
        try {
            account.deposit(500);
            account.withdraw(2000);
        } catch (InsufficientFundsException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

3. **抽象类多态**
```java
abstract class Shape {
    public abstract double area();
    public abstract double perimeter();
}

class Circle extends Shape {
    private double radius;
    public Circle(double r) { radius = r; }
    public double area() { return Math.PI * radius * radius; }
    public double perimeter() { return 2 * Math.PI * radius; }
}

class Rectangle extends Shape {
    private double width, height;
    public Rectangle(double w, double h) { width = w; height = h; }
    public double area() { return width * height; }
    public double perimeter() { return 2 * (width + height); }
}

public class TestShape {
    public static void main(String[] args) {
        Shape s1 = new Circle(5);
        Shape s2 = new Rectangle(4, 6);
        System.out.printf("圆面积：%.2f，周长：%.2f%n", s1.area(), s1.perimeter());
        System.out.printf("矩形面积：%.2f，周长：%.2f%n", s2.area(), s2.perimeter());
    }
}
```

---

> **复习建议**：本扩展手册覆盖了 Java 基础到异常处理的核心内容。请结合代码示例，重点理解面向对象的三大特性、字符串不可变性、集合与异常的区别。建议动手编写每个章节的典型代码（如学生类、动物多态、自定义异常），并默写常见 API 方法签名。祝考试顺利！
> 