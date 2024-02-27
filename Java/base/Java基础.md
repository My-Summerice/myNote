# Java基础

[TOC]

## 1.数据类型

### 1.1 基本类型与包装类型

​		包装类型是引用类型，它们提供了许多有用的方法和功能，例如对基本类型的操作、类型转换以及与其他对象的交互。包装类型还常用于集合类和泛型中，因为它们可以作为对象进行存储和处理。

​		Java提供了自动装箱（Autoboxing）和自动拆箱（Unboxing）的特性，使得基本类型和包装类型之间的转换更加便捷。例如，可以将基本类型直接赋值给对应的包装类型变量，或者将包装类型直接传递给期望的基本类型参数。

```java
int num = 10; // 基本类型
Integer number = num; // 自动装箱
int result = number; // 自动拆箱
```

​		需要注意的是，基本类型的变量在内存中直接存储其值，而包装类型的对象在内存中以对象的形式存在，包含了额外的开销。在使用基本类型还是包装类型时，需要根据具体的需求和场景进行选择。

八个基本类型对应的包装类型及bit(比特)数：

- boolean - Boolean : 1
- byte - Byte : 8
- char - Character: 16
- short - Short : 16
- int - Integer : 32
- float - Float : 32
- long - Long : 64
- double - Double : 64

### 1.2 缓存池

#### 1.2.1 Integer缓存池（Integer Cache）

Java中的`Integer`类维护了一个缓存池，用于存储在范围-128到127之间的整数对象。

`new Integer()` 和 `Integer.valueOf()` 是两种创建 `Integer` 对象的方式，在使用时有一些优缺点需要考虑。

**注意：**java9 之后不允许用户使用`new Integer()`且不建议使用`Integer.valueOf()`，最方便的方法是直接赋值，像基本类型一样`Integer a = 123;`。

1. `new Integer()`：创建一个新的Integer对象。

   - 优点：
     - 可以创建任意整数值的 `Integer` 对象，不受范围限制。
   - 缺点：
     - 每次调用 `new Integer()` 都会创建一个新的 `Integer` 对象，无论该值是否在缓存范围内。这可能导致频繁的对象创建和销毁，增加了内存开销。
     - 不同的 `new Integer()` 调用会创建不同的对象实例，即使它们表示相同的整数值。

2. `Integer.valueOf()`：获取一个Integer对象，会先在缓存池中判断是否存在相同值的对象，如果存在直接返回该对象，如果不存在则创建一个新的对象并放入缓存池。

   ```java
   public static Integer valueOf(int i) {
       if (i >= IntegerCache.low && i <= IntegerCache.high)
           return IntegerCache.cache[i + (-IntegerCache.low)];
       return new Integer(i);
   }
   ```

   - 优点：
     - 使用 `Integer.valueOf()` 方法可以重用缓存池中的 `Integer` 对象，以避免频繁的对象创建和销毁，节省内存和提高性能。
     - 对于在 -128 到 127 范围内的整数值，`Integer.valueOf()` 方法会返回缓存池中的对象，因此可以减少对象的创建。
   - 缺点：
     - 由于 `Integer.valueOf()` 方法使用了缓存池，对于超出缓存范围的整数值，会返回一个新创建的 `Integer` 对象。这可能导致多个相同整数值的 `Integer` 对象实际上是不同的对象实例，可能会引发意想不到的问题。

在选择使用 `new Integer()` 还是 `Integer.valueOf()` 时，可以考虑以下几点：

- 如果需要创建一个特定整数值的 `Integer` 对象，并且范围超出了 -128 到 127，可以使用 `new Integer()`。

- 如果需要频繁地创建和比较整数值，并且范围在 -128 到 127 内，可以使用 `Integer.valueOf()` 来重用对象，以提高性能和节省内存。

- 如果需要确保相同整数值的 `Integer` 对象是相同的实例，而不是通过缓存池重用的对象，应使用 `new Integer()`。

  例子：

```java
Integer a = new Integer(1);
Integer b = new Integer(1);
Integer c = Integer.valueOf(1);
Integer d = Integer.valueOf(1);
System.out.println(a == b);	// false
System.out.println(c == d);	// true
```

在 Java 8 中，Integer 缓存池的大小默认为 -128~127。

```java
static final int low = -128;
static final int high;
static final Integer cache[];

static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
        sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
        try {
            int i = parseInt(integerCacheHighPropValue);
            i = Math.max(i, 127);
            // Maximum array size is Integer.MAX_VALUE
            h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
        } catch( NumberFormatException nfe) {
            // If the property cannot be parsed into an int, ignore it.
        }
    }
    high = h;

    cache = new Integer[(high - low) + 1];
    int j = low;
    for(int k = 0; k < cache.length; k++)
        cache[k] = new Integer(j++);

    // range [-128, 127] must be interned (JLS7 5.1.7)
    assert IntegerCache.high >= 127;
}
```

​		编译器会**在缓冲池范围内的基本类型**自动装箱过程调用 valueOf() 方法，因此多个 Integer 实例使用自动装箱来创建并且值相同，那么就会引用相同的对象。

```java
Integer e = 1;
Integer f = 1;
Integer g = 128;
Integer h = 128;
System.out.println(e == f);	// true
System.out.println(g == h);	// false (超过缓存池范围就会新建对象)
```

基本类型对应的缓冲池范围如下:

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

#### 1.2.2 字符串常量池（String Pool）

​		Java中的字符串常量池是用于存储字符串字面量的缓存池。字符串常量池的目的是减少重复的字符串对象的创建，以节省内存和提高性能。当使用双引号声明字符串时，编译器会首先检查字符串常量池中是否存在相同内容的字符串，如果存在则直接返回常量池中的引用，否则创建新的字符串对象并放入常量池。

#### 1.2.3 常用对象缓存

​		在某些情况下，可以手动创建和管理常用的对象缓存，以便在需要时重复使用。例如，可以创建一个缓存池来存储数据库连接、线程池、HTTP客户端等对象，以减少创建和销毁这些资源的开销。

​		需要注意的是，缓存池的使用需要谨慎，特别是对于可变对象。在某些情况下，缓存池可能会导致对象的状态共享和不可预测的行为。因此，在使用缓存池时，需要确保对象的线程安全性和可变性问题，并在适当的时候进行清理和刷新。

## 2.常用数据结构

### 2.1 String、StringBuilder、StringBuffer

#### 2.1.1 String

- **概念：**String类是不可变的，一旦创建，就不能被修改。每次对字符串进行操作，都会创建一个新的字符串对象。

- **使用示例：**

  ```java
  String str = "Hello";
  str = str + " World"; // 会创建新的字符串对象
  ```

- **注意事项：**由于String是不可变的，**频繁的字符串连接操作会导致大量的对象创建和内存开销**。如果需要经常修改字符串，考虑使用StringBuilder或StringBuffer。

#### 2.1.2 StringBuilder

- **概念：**StringBuilder类是可变的，适用于频繁修改字符串的场景。**它的方法不是线程安全的**。

- **使用示例：**

  ```java
  StringBuilder sb = new StringBuilder();
  sb.append("Hello");
  sb.append(" World");
  String result = sb.toString();
  ```

- **注意事项：**StringBuilder的操作是在原始对象上进行的，不会创建新的StringBuilder对象，因此效率较高。但它不是线程安全的，如果在多线程环境下使用，应考虑使用StringBuffer。

#### 2.1.3 StringBuffer

- **概念：**StringBuffer类与StringBuilder类类似，也是可变的字符串序列，但**它的方法是线程安全的**。

- **使用示例：**

  ```java
  StringBuffer sb = new StringBuffer();
  sb.append("Hello");
  sb.append(" World");
  String result = sb.toString();
  ```

- **注意事项：**StringBuffer的操作是在原始对象上进行的，与StringBuilder一样，不会创建新的StringBuffer对象。由于线程安全性的开销，StringBuffer的性能可能稍低于StringBuilder。如果在单线程环境下使用，建议使用StringBuilder。

#### 2.1.4 StringBuilder & StringBuffer 常用方法

```java
package String;

public class Main {
    public static void main(String[] args) {
        // String
        String s1 = "s1";
        s1 = s1 + " aaaaaaaaaa";    // 会创建一个新的对象，且由于变量名被覆盖导致无法再使用原有字符串"s1"

        // StringBuilder
        StringBuilder s2 = new StringBuilder("s2");
        s2.append(s1); // 字符串末尾追加内容（追加的内容不限数据类型，需要注意的是，如果追加的是一个对象，则默认追加的是该对象的toString()的结果）
        s2.insert(2, s1); // 相当于比append()多了一个指定下标（第一个参数）的功能
        s2.delete(6, 7); // 删除指定下标区间（左闭右开）内的字符串内容，比如这里就是删掉下标为6的内容
        s2.replace(6, 7, " replace "); // 与delete类似，delete相当于替换的内容为空
        s2.reverse(); // 反转字符串，完全倒序 abcd -> dcba
        int s2Len = s2.length(); // 返回字符串长度
        System.out.println(s2Len);
        char s2Char = s2.charAt(1); // 返回指定下标位置的字符，只有一个参数，不可以选择区间
        System.out.println(s2Char);

        // StringBuffer
        StringBuffer s3 = new StringBuffer("s3");
        // StringBuffer的方法与StringBuilder的完全一致，只不过前者线程安全性能较低，后者性能较高线程不安全

        System.out.println(s1);
        System.out.println(s2);
        System.out.println(s3);
    }
}
```

输出：

```shell
35
a
s1 aaaaaaaaaa
aaaaaaaaaa 1saaaaaaa ecalper a 1s2s
s3
```

### 2.2 数组

Java中的数组是一种用于存储固定数量元素的数据结构。下面是关于Java数组的概念、示例以及使用时需要注意的事项：

1. 概念：

   - 数组是一个容器，用于存储具有相同数据类型的元素。
   - 数组在内存中是连续分配的一块存储空间。
   - 数组的长度在创建时确定，并且不能更改。

2. 示例：

   - 声明和创建一个整数数组：

     ```java
     int[] numbers = new int[5];
     ```

   - 初始化数组并赋值：

     ```java
     int[] numbers = {1, 2, 3, 4, 5};
     ```

3. 使用时需要注意的事项：

   - 数组索引从0开始，因此访问第一个元素时使用索引0。
   - 数组越界：要确保不超出数组的有效索引范围，否则会引发`ArrayIndexOutOfBoundsException`异常。
   - 数组长度：使用`length`属性获取数组的长度，而不是使用固定的常数。
   - 数组元素的类型必须一致，即所有元素具有相同的数据类型。
   - 数组可以存储任意基本类型（如`int`、`double`、`boolean`等）和对象类型（如`String`、`Object`等）。
   - 数组可以是多维的，例如二维数组或三维数组。
   - 数组是引用类型，因此可以将数组分配给其他变量，而不会复制数组的内容。
   - 数组是固定长度的，因此无法在运行时直接改变数组的大小。如果需要动态调整大小，可以使用`ArrayList`类。

4. 示例代码：

   ```java
   // 声明和创建一个字符串数组
   String[] fruits = new String[3];
   
   // 初始化数组并赋值
   fruits[0] = "Apple";
   fruits[1] = "Banana";
   fruits[2] = "Orange";
   
   // 访问数组元素
   System.out.println(fruits[0]);  // 输出：Apple
   
   // 数组长度
   int length = fruits.length;  // 获取数组长度
   
   // 遍历数组
   for (int i = 0; i < fruits.length; i++) {
       System.out.println(fruits[i]);
   }
   ```

​		使用数组时要注意索引和边界条件，以避免出现错误。此外，Java还提供了`Arrays`类和`System.arraycopy()`方法等工具来操作和处理数组。

### 2.3 集合

#### 2.3.1 List（列表）

- **List是有序集合，允许重复元素。**

- 常见的实现类有`ArrayList`和`LinkedList`。

- 可以通过索引访问元素，提供了插入、删除和修改元素的方法。

- 示例代码：

  ```java
  List<String> list = new ArrayList<>();
  list.add("Apple");
  list.add("Banana");
  list.add("Orange");
  System.out.println(list.get(0));  // 输出：Apple
  ```

##### 2.3.1.1 ArrayList 与 数组互转

- ArrayList 转数组：

  ```java
  List<String> list = new ArrayList<>();
  list.add("apple");
  list.add("banana");
  list.add("orange");
  
  String[] fruits = new String[list.size()];
  fruits = list.toArray(fruits);
  
  for (String item : fruits) {
      System.out.println(item);
  }
  ```

- 数组转 ArrayList：

  ```java
  // 数组转list
  List<String> fruitsList = Arrays.asList(fruits);
  System.out.println(fruitsList);
  ```

#### 2.3.2 Set（集）

- **Set是无序集合，不允许重复元素。**

- 常见的实现类有`HashSet`、`LinkedHashSet`和`TreeSet`。

- 不提供索引访问元素的方法，主要用于判断元素是否存在、去重等操作。

- 示例代码：

  ```java
  Set<String> set = new HashSet<>();
  set.add("Apple");
  set.add("Banana");
  set.add("Orange");
  System.out.println(set.contains("Apple"));  // 输出：true
  ```

#### 2.3.3 Map（映射）

- **Map是键值对的集合，每个键对应一个值，键是唯一的。**

- 常见的实现类有`HashMap`、`LinkedHashMap`和`TreeMap`。

- 可以通过键来访问值，提供了插入、删除和修改键值对的方法。

- 示例代码：

  ```java
  Map<String, Integer> map = new HashMap<>();
  map.put("Apple", 1);
  map.put("Banana", 2);
  map.put("Orange", 3);
  System.out.println(map.get("Apple"));  // 输出：1
  ```

#### 2.3.4 Queue（队列）

- **Queue是一种先进先出（FIFO）的集合。**

- 常见的实现类有`LinkedList`和`PriorityQueue`。

- 提供了添加、删除和查看元素的方法，常用于任务调度等场景。

- 示例代码：

  ```java
  Queue<String> queue = new LinkedList<>();
  queue.offer("Apple");
  queue.offer("Banana");
  queue.offer("Orange");
  System.out.println(queue.poll());  // 输出：Apple
  ```

## 3.数据运算

### 3.1 参数传递

Java 的参数传递是值传递而不是引用传递。

```java
// demo/Book.java
public class Book {
    String name;

    Book(String name) {
        this.name = name;
    }

    String getName() {
        return this.name;
    }

    void setName() {
        this.name = name;
    }

    String getObjectAddress() {
        return super.toString();
    }
}

// demo/EditBook.java
public class EditBook {
    public static void main(String[] args) {
        Book book = new Book("《Java 从入门到放弃》");
        System.out.println(book.getName());
        System.out.println(book.getObjectAddress());

        // 测试修改书名
        editBook(book);

        System.out.println(book.getObjectAddress());
        System.out.println(book.getName());
    }

    private static void editBook(Book book) {
        System.out.println(book.getObjectAddress());

        book = new Book("真的吗？");

        System.out.println(book.getName());
        System.out.println(book.getObjectAddress());
    }
}
```

输出：

```shell
《Java 从入门到放弃》
ParamPass.Book@10f87f48
ParamPass.Book@10f87f48
真的吗？
ParamPass.Book@b4c966a
ParamPass.Book@10f87f48
《Java 从入门到放弃》
```

​		从结果可以看出来把新创建的对象赋值给之前的对象后，不会变更之前对象的内容，但是，如果不新建对象，而是在`editBook()`函数中调用book对象的`setName()`方法是可以成功编辑的，因为改变的是同一个地址指向的内容，如下：

```java
public class EditBook {
    public static void main(String[] args) {
        Book book = new Book("《Java 从入门到放弃》");
        System.out.println(book.getName());
        System.out.println(book.getObjectAddress());

        // 测试修改书名
        editBook(book);

        System.out.println(book.getObjectAddress());
        System.out.println(book.getName());
    }

    private static void editBook(Book book) {
        System.out.println(book.getObjectAddress());

//        book = new Book("真的吗？");
        book.setName("学不动了");

        System.out.println(book.getName());
        System.out.println(book.getObjectAddress());
    }
}
```

输出：

```shell
《Java 从入门到放弃》
ParamPass.Book@10f87f48
ParamPass.Book@10f87f48
学不动了
ParamPass.Book@10f87f48
ParamPass.Book@10f87f48
学不动了
```

​		可以看出来，虽然是值传递，但如果传参是一个对象，那么这次传参也会带上对象的地址参数，所以，在用对象的相关方法时操作的都是指向该地址的对象。

**总结：**

​		在Java中，无论是基本数据类型还是对象类型，方法参数的传递都是按值传递的。这意味着在将参数传递给方法时，传递的是参数的副本，而不是参数本身。

- 对于基本数据类型（如int、double、boolean等），传递的是值的副本，方法中对参数的修改不会影响原始值。

- 对于对象类型，传递的是对象引用的副本。这意味着方法中的参数和原始引用都指向同一个对象。通过这个引用，可以修改对象的属性，这些修改在方法返回后对原始对象是可见的。

```java
public class Main {
    public static void main(String[] args) {
        int num = 5;
        System.out.println(num); // 输出 5

        modifyPrimitive(num);

        System.out.println(num); // 输出 5

        Person person = new Person("Alice");
        System.out.println(person.name); // 输出 "Alice"

        modifyObject(person);

        System.out.println(person.name); // 输出 "Bob"
    }

    private static void modifyPrimitive(int value) {
        value = 10;
    }

    private static void modifyObject(Person p) {
        p.name = "Bob";
    }
}
```

​		在上面的示例中，`modifyPrimitive` 方法尝试修改基本数据类型 `num` 的值，但这个修改对于主方法中的 `num` 变量没有影响。而 `modifyObject` 方法修改了 `person` 对象的属性，在主方法中的 `person.name` 的访问显示了这个修改。

### 3.2 赋值与类型转换

Java 中的数值类型按照大小顺序分为 `byte`、`short`、`int`、`long`、`float` 和 `double`。较小的类型可以自动提升为较大的类型，以便进行运算。

可以将一个字面量赋值给与它类型相同，或比它类型范围更大的变量。

```java
// 错误的写法
float f = 1.23;	// 1.23字面量类型为double, 编译报错
// 超出范围的赋值将导致编译错误
short num5 = 50000;
short num6 = -40000;

// 正确的写法
double d = 1.23;
float f = 1.23f;
double d = 1.23f;
short num1 = 100;
short num2 = -200;
short num3 = 32767;
short num4 = -32768;
```

特殊的：

```java
public class DataType {
    public static void main(String[] args) {
        short s = 11111;
        float f = 1.1f;

        s += f;

        System.out.println(s);
        System.out.println(f);
        
        char c = 'A';
        int asciiValue = c + 1;
        System.out.println(asciiValue);
    }
}
```

输出：

```shell
11112
1.1
66
```

可以看到比short范围更大的float被转为了short再参与运算，而字符'A'在参与整型运算时直接被转为了ascii码对应的数字了。

### 3.3 switch允许的数据类型

基础数据类型除了long和它的包装数据类型Long都是可以的。

从 Java 7开始，可以在switch条件判断语句中使用String对象。(但这仅仅是个语法糖，内部实现中使用的是字符串对象的hashCode())

```java
String s = "a";
switch (s) {
    case "a":
        System.out.println("aaa");
        break;
    case "b":
        System.out.println("bbb");
        break;
}
```

但是，switch的条件判断语句中是不支持long/Long类型的，因为switch的设计初衷就是对少数的几个值进行等值判断，如果值过于复杂，建议还是使用if。

```java
Long x = 111;	// Long/long都不可以
switch (x) {
     case 111:
         System.out.println(111);
         break;
     case 222:
         System.out.println(222);
         break;
}
```

编译报错：

```shell
Incompatible types. Found: 'java.lang.Long', required: 'char, byte, short, int, Character, Byte, Short, Integer, String, or an enum'
```

## 4.继承

### 4.1 访问权限

​		在Java中，有三个访问权限修饰符：`private`、`protected`和`public`。这些修饰符用于控制类、类的成员（字段、方法、内部类等）的可访问性。

- `private`：`private` 修饰的成员只能在同一个类中访问。它们对于类的外部是不可见的。这意味着其他类无法直接访问 `private` 成员。`private` 成员通常用于隐藏类的内部实现细节，只允许类自身的方法访问这些成员。

- `protected`：`protected` 修饰的成员可以在同一个包内的其他类以及该类的子类中访问。它们对于不在同一个包内的非子类是不可见的。`protected` 成员提供了一种限制访问的方式，使得只有相关的类能够访问和使用这些成员。

- `public`：`public` 修饰的成员对于所有类都是可见的。它们可以被任何类访问，无论是否在同一个包中，也无论是否是该类的子类。
- 需要注意的是，访问权限修饰符不仅适用于类的成员，还适用于类本身。一个类可以被声明为 `public`、`protected` 或者没有访问修饰符（默认修饰符，包级可见）。

下面是一个示例，演示了这些访问权限修饰符的使用：

```java
public class MyClass {
    private int privateField;
    protected int protectedField;
    public int publicField;
    
    private void privateMethod() {
        // 只能在类内部访问
    }
    
    protected void protectedMethod() {
        // 在同一个包内的其他类以及子类中可访问
    }
    
    public void publicMethod() {
        // 对所有类可访问
    }
}

public class AnotherClass {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        
        // 对于 privateField，privateMethod 是不可见的
        obj.protectedField = 10;
        obj.protectedMethod();
        
        obj.publicField = 20;
        obj.publicMethod();
    }
}
```

在上面的示例中，`MyClass` 中的成员根据不同的访问权限修饰符进行了标记。在 `AnotherClass` 类中，可以访问 `protectedField` 和 `protectedMethod`，因为 `AnotherClass` 和 `MyClass` 在同一个包中。而 `publicField` 和 `publicMethod` 可以在任何地方访问。

**总结：**

- 设计良好的模块会隐藏所有的实现细节，把它的 API 与它的实现清晰地隔离开来。模块之间只通过它们的 API 进行通信，一个模块不需要知道其他模块的内部工作情况，这个概念被称为信息隐藏或封装。因此**访问权限应当尽可能地使每个类或者成员不被外界访问**。

- 如果子类的方法重写了父类的方法，那么**子类中该方法的访问级别不允许低于父类的访问级别**。这是为了确保可以使用父类实例的地方都可以使用子类实例，也就是确保满足里氏替换原则。

- **字段决不能是公有的**，因为这么做的话就失去了对这个字段修改行为的控制，客户端可以对其随意修改。例如下面的例子中，AccessExample 拥有 id 公有字段，如果在某个时刻，我们想要使用 int 去存储 id 字段，那么就需要去修改所有的客户端代码。（特殊的：如果是包级私有的类或者私有的嵌套类，那么直接暴露成员不会有特别大的影响）
  - 所以一般都是使用该类共有的`setter()`和`getter()`方法来操作类中的字段。

### 4.2 抽象类与接口

#### 4.2.1 抽象类

​		抽象类使用`abstract`关键字声明。抽象类一般会包含抽象方法，抽象方法也只能位于抽象类中，同时，抽象方法也是使用`abstract`关键字声明。

​		抽象类与普通类最大的区别是：抽象类不能被实例化，只能通过实例化继承它的子类来实现对抽象类中的字段和方法的访问。

​		如果一个类继承自一个抽象类且该抽象类中有至少一个抽象方法，那么子类必须实现（重写）这些抽象方法，除非子类本身也是一个抽象类。

```java
// AbstractClass/Main
public class main {
    public static void main(String[] args) {
        AbstractExtendClass testClass = new AbstractExtendClass();
        testClass.func1();
        testClass.func2();
    }
}

// AbstractClass/AbstractClass
public abstract class AbstractClass {
    public abstract void func1();

    public void func2() {
        System.out.println("func2");
    }
}

// AbstractClass/AbstractExtendClass
public class AbstractExtendClass extends AbstractClass {
    @Override
    public void func1() {
        System.out.println("func1");
    }
}
```

#### 4.2.2 接口

​		在 Java 中，接口（Interface）是一种抽象类型，它定义了一组方法的签名（方法名、参数列表和返回类型），但没有提供方法的具体实现。接口允许定义一组相关的操作，而不需要指定这些操作的具体实现逻辑。其他类可以实现（implement）接口，并提供方法的具体实现。

接口在 Java 中起到了以下几个重要的作用：

1. 定义契约：接口定义了一组方法的契约，表示实现该接口的类将提供这些方法的实现。通过接口，可以明确规定其他类应该实现的方法。
2. 实现多态：通过接口，可以实现多态性。可以将实现了相同接口的不同类的对象视为同一类型，从而可以以统一的方式操作它们。
3. 解耦合：接口可以用于解耦合，将接口作为抽象层，使得代码可以针对接口编程而不依赖具体实现类，从而提高代码的灵活性和可维护性。

**注意：**

- 接口的成员（字段加方法）默认都是`public`的，且不允许被定义为`private`或者`protected`。
- 接口的字段都是默认`static`和`final`的。

在 Java 中，通过 `interface` 关键字来定义接口。以下是一个示例：

```java
public interface Drawable {
    int A = 111;

    void draw();
    double calculateArea();
}
```

在上面的示例中，`Drawable` 是一个接口，定义了两个方法：`draw()` 和 `calculateArea()`。这两个方法没有具体的实现，只有方法签名。

其他类可以实现接口，通过 `implements` 关键字来实现接口，并提供方法的具体实现。以下是一个示例：

```java
public class Circle implements Drawable {
    private final double radius;
    
    public Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        // 实现绘制圆的逻辑
        System.out.println("Drawing a circle");
    }
    
    @Override
    public double calculateArea() {
        // 实现计算圆面积的逻辑
        return Math.PI * radius * radius;
    }
}
```

Main函数：

```java
public class Main {
    public static void main(String[] args) {
        Circle draw = new Circle(1.2);

        draw.draw();

        double area = draw.calculateArea();
        System.out.println("Area: " + area);
        System.out.println(draw.a);
    }
}
```

输出：

```shell
Drawing a circle
Area: 4.523893421169302
111
```

​		在上面的示例中，`Circle` 类实现了 `Drawable` 接口，并提供了 `draw()` 和 `calculateArea()` 方法的具体实现。

​		需要注意的是，一个类可以实现多个接口，通过逗号分隔。接口之间通过接口继承（`extends`）也可以建立继承关系。

​		接口的使用可以提高代码的可扩展性和可维护性，促进代码的模块化和解耦合。它在 Java 中广泛应用于面向对象的编程和设计模式中。

#### 4.2.3 抽象类与接口比较

​		抽象类和接口是 Java 中用于实现抽象类型的两种机制，它们有一些相似之处，但也存在一些不同点。下面是抽象类和接口的比较：

1. 定义和用途：
   - 抽象类（Abstract Class）：抽象类是一种类，用于表示一种通用的、抽象的概念，并可以包含实例变量、实例方法和构造方法。抽象类可以有部分或全部的抽象方法，这些抽象方法没有具体的实现，在子类中必须被实现。抽象类用于提供一种模板或基类，供子类继承和扩展。
   - 接口（Interface）：接口是一种规范或契约，它只包含方法的签名而没有具体的实现。接口定义了一组与特定行为相关的方法，表示实现该接口的类将提供这些方法的实现。接口用于实现多态性、解耦合和定义类之间的协议。
2. 继承关系：
   - 抽象类：可以使用关键字 `extends` 继承一个抽象类。一个类只能继承一个抽象类，但可以实现多个接口。
   - 接口：可以使用关键字 `implements` 实现一个或多个接口。一个类可以实现多个接口，但不能继承多个类。
3. 构造方法：
   - 抽象类：可以有构造方法，用于初始化抽象类的实例变量。
   - 接口：不能有构造方法，因为接口不能被实例化。
4. 成员变量：
   - 抽象类：可以包含实例变量，这些变量可以有不同的访问修饰符。
   - 接口：不能包含实例变量，接口只能定义常量（使用 `final static` 修饰）。
5. 默认实现：
   - 抽象类：可以包含具体的方法实现，也可以有抽象方法。子类可以选择性地覆盖抽象方法，也可以直接使用抽象类中的方法实现。
   - 接口：不能包含具体的方法实现，所有的方法都是抽象的。实现接口的类必须提供方法的具体实现。
6. 设计思想：
   - 抽象类：用于表示一种层次结构和类的继承关系，适用于那些具有共同特征的类之间的关系。
   - 接口：用于定义一组相关的操作，独立于具体的类，适用于描述类之间的协议和行为。

​		选择抽象类还是接口取决于具体的需求和设计。一般来说，当需要表示一种层次结构和类的继承关系时，可以使用抽象类；当需要定义一组相关的操作，独立于具体的类时，可以使用接口。在实际应用中，常常会同时使用抽象类和接口来实现更灵活的设计。

### 4.3 super

​		在 Java 中，`super` 是一个关键字，可用于引用父类的成员变量、成员方法或构造方法。下面是 `super` 的几种用法：

1. 引用父类的成员变量和成员方法：

   - 使用 `super` 关键字可以在子类中访问父类的成员变量和成员方法。这在子类中存在与父类同名的成员变量或方法时特别有用，以区分引用的是父类的还是子类的成员。例如：

   ```java
   class ParentClass {
       protected int value;
       
       public void printValue() {
           System.out.println("Parent value: " + value);
       }
   }
   
   class ChildClass extends ParentClass {
       private int value;
       
       public void setValue(int value) {
           this.value = value;
       }
       
       public void printValue() {
           super.printValue(); // 调用父类的 printValue() 方法
           System.out.println("Child value: " + value);
       }
   }
   
   public class Main {
       public static void main(String[] args) {
           ChildClass child = new ChildClass();
           child.setValue(10);
           child.printValue();
       }
   }
   ```

   输出：

   ```shell
   Parent value: 0
   Child value: 10
   ```

2. 调用父类的构造方法：

   - 在子类的构造方法中，使用 `super(arguments)` 可以调用父类的构造方法，并传递相应的参数。这样可以确保父类的初始化代码得到执行。`super()` 调用父类的无参构造方法，而 `super(arguments)` 调用父类的有参构造方法。

   例如：

   ```java
   class ParentClass {
       private int value;
       
       public ParentClass(int value) {
           this.value = value;
       }
   }
   
   class ChildClass extends ParentClass {
       private int childValue;
       
       public ChildClass(int value, int childValue) {
           super(value); // 调用父类的构造方法
           this.childValue = childValue;
       }
       
       public int getChildValue() {
           return childValue;
       }
   }
   
   public class Main {
       public static void main(String[] args) {
           ChildClass child = new ChildClass(10, 20);
           System.out.println("Parent value: " + child.getValue());
           System.out.println("Child value: " + child.getChildValue());
       }
   }
   ```

   输出：

   ```shell
   Parent value: 10
   Child value: 20
   ```

​		通过使用 `super` 关键字，我们可以在子类中访问父类的成员和构造方法，以及确保父类的初始化代码得到执行。这样可以在继承关系中更灵活地使用和扩展父类的功能。

### 4.4 重写与重载

**1. 重写(Override)**

存在于继承体系中，指子类实现了一个与父类在方法声明上完全相同的一个方法。

为了满足里式替换原则，重写有以下两个限制:

- 子类方法的访问权限必须大于等于父类方法；
- 子类方法的返回类型必须是父类方法返回类型或为其子类型。

使用 @Override 注解，可以让编译器帮忙检查是否满足上面的两个限制条件。

**2. 重载(Overload)**

存在于同一个类中，指一个方法与已经存在的方法名称上相同，但是参数类型、个数、顺序至少有一个不同。

应该注意的是，返回值不同，其它都相同不算是重载。

## 5.Object

### 5.1 常用方法

1. `toString()`: 该方法返回对象的字符串表示。默认情况下，`toString()` 返回的是对象的类名，后跟 `@` 符号和对象的哈希码。建议在自定义类中重写该方法，以提供更有意义的对象字符串表示。

2. `equals(Object obj)`: 该方法用于比较两个对象是否相等。默认情况下，`equals()` 方法比较的是对象的引用是否相等（即两个对象是否指向同一个内存位置）。建议在自定义类中重写该方法，以根据对象的属性进行比较。

3. `hashCode()`: 该方法返回对象的哈希码值。哈希码是由对象的内部状态计算得出的整数值，用于在哈希表等数据结构中快速查找对象。如果重写了 `equals()` 方法，也应该重写 `hashCode()` 方法，以保证相等的对象具有相同的哈希码。

   - 需要注意的是，如果在自定义类中重写了 `equals()` 方法，通常也需要同时重写 `hashCode()` 方法，以遵循 `equals()` 和 `hashCode()` 方法之间的一致性约定。这样可以确保对象在集合类（如 `HashSet`、`HashMap`）等使用哈希码的数据结构中正常工作。

     ```
     public class Equals {
         private int id;
         private String name;
     
         public Equals(int id, String name) {
             this.id = id;
             this.name = name;
         }
     
         @Override
         public boolean equals(Object obj) {
             // 如果 obj 为null这个if判断的结果直接是false
             if (obj == this) {
                 return true;
             }
     
             // getClass() 获取对象运行时类
             if (obj == null || getClass() != obj.getClass()) {
                 return false;
             }
     
             Equals other = (Equals) obj;
     
             return Objects.equals(other.name, name) && other.id == id;
         }
     
         @Override
         public int hashCode() {
             System.out.printf("this is parent's hashCode() : %d\n", super.hashCode());
             return 114514;
         }
     }
     ```

4. `getClass()`: 该方法返回对象的运行时类。它返回一个 `Class` 对象，该对象包含有关对象所属类的信息。

5. `finalize()`: 该方法是一个垃圾回收器在回收对象之前调用的方法。通常不建议重写和使用该方法，因为现代的 Java 垃圾回收机制很少需要显式的对象清理。

​		除了上述方法外，`Object` 类还提供了 `clone()`、`notify()`、`notifyAll()`、`wait()` 等方法，用于处理线程同步和对象克隆等操作。这些方法在更复杂的场景中使用。

## 6.关键字

### 6.1 final

1. **数据**

​		如果声明对象为数据，则该数据被视为常量（常量名全部大写用_分隔），可以是编译时常量，也可以是运行时被初始化后不能被改变的常量。

- 编译时常量：编译时常量是在编译阶段被解析和替换的常量。它们在编译期间就已经确定，并且在运行时保持不变。编译时常量使用 `final` 关键字和基本数据类型或字符串字面值进行定义。示例代码如下：

```java
final int MAX_VALUE = 100;
final String MESSAGE = "Hello, World!";
```

- 运行时常量：运行时常量是在程序运行时才被确定的常量。它们的值可能取决于程序的输入、配置或其他运行时条件。运行时常量可以是由运行时表达式计算得出的结果，也可以是通过静态方法调用等方式获得的值。示例代码如下：

```java
final int MAX_VALUE = calculateMaxValue();
final String MESSAGE = getMessageFromConfig();
```

2. **方法**

​		如果被声明的对象为方法，则该方法不能被子类重写。

​		需要注意的是，`pravite` 关键字被隐式地指定为 `final`，也就是说如果父类中的一个方法使用 `pravite` 来声明，那么对子类就无法感知到该方法，也就不存在使用和重写该方法的情况；而如果用 `final` 来声明父类中的方法，则是子类是可以使用但不可重写。

3. **类**

​		如果被生命的对象为类，则该类不能被继承。

### 6.2 static

1. 静态变量

   - 静态变量：又称为类变量（与类绑定），及所有类的实例都会共享该静态变量，可以直接通过类的实例化对象来访问；注意：静态变量在内存中只存在一份。

   - 实例变量：及每创建一个类的实例就会产生一个实例变量，该变量的生命周期就是该实例的生命周期。

     例子：

2. 静态方法

   - 静态方法在类加载的时候就已经存在了，不需要被实例化就可以调用。所以静态方法必须有实现（即有函数体），不能是抽象方法（`abstract`）。
   - 只能访问所属类的静态字段和静态方法，静态方法中不能有 `this` 和 `super` 关键字。

3. 静态语句块

   - 静态语句块在类加载过程中执行，只执行一次，并且在类的所有静态成员初始化之前执行。

   - 它主要用于执行一些初始化操作、加载资源或进行其他需要在类加载时执行的操作。

   - 多次实例化同一个类时，静态语句块只会执行一次，因为只与类加载有关，与实例化无关。

     ```java
     public class Static {
         static {
             System.out.println("123");
         }
     }
     
     public class Main {
         public static void main(String[] args) {
             // static 关键字
             Static xhz = new Static();
             Static xhz2 = new Static();
         }
     }
     ```

     输出：

     ```shell
     123
     ```

4. 静态内部类

- 非静态内部类依赖于外部类的实例，而静态内部类不需要。

- 静态内部类不能访问外部类的非静态的变量和方法。

- 注意：内部类方法与外部类方法重名时不构成重写和重载，可以视为两个独立的类。

  ```java
  public class Static {
      private static String CXK = "jntm";
      private String KUN_KUN = "香翅捞饭";
  
      static void outerKun() {
          System.out.println("外部鲲鲲");
      }
  
      static class InnerStaticClass {
          public void print() {
              System.out.println(CXK);
              // 静态内部类只能访问外部类的静态变量和方法
  //            System.out.println(KUN_KUN);  // Non-static field 'KUN_KUN' cannot be referenced from a static context
          }
  
          public void innerKun() {
              System.out.println("内部鲲鲲");
          }
  
          // 不构成重写，因为没有继承关系
          public void outerKun() {
              Static.outerKun();
          }
      }
  }
  
  public class Main {
      public static void main(String[] args) {
          // 静态内部类
          Static.InnerStaticClass innerStaticClass = new Static.InnerStaticClass();
          innerStaticClass.print();
          innerStaticClass.innerKun();
          innerStaticClass.outerKun();
      }
  }
  ```

  输出：

  ```shell
  jntm
  内部鲲鲲
  外部鲲鲲
  ```

5. 静态导包

​	在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但可读性大大降低。（不建议使用）

```java
import static com.xxx.ClassName.*    
```

6. 初始化顺序

  - 在Java中，一个类的各部分内容的初始化顺序如下：

    1. **静态成员变量的初始化：**首先，所有的静态成员变量（包括静态变量和静态常量）按照声明的顺序进行初始化。静态变量会在类加载时进行初始化，而静态常量在编译时就已经被赋值。
    2. **静态语句块的执行：**紧接着，静态语句块按照在类中的定义顺序执行。静态语句块用于对静态成员进行初始化或执行其他静态操作。
    3. **实例成员变量的初始化：**接下来，实例成员变量（非静态变量）按照声明的顺序进行初始化。实例变量的初始化发生在创建类的实例时，每个实例变量都会被赋予其对应的默认值或指定的初始值。
    4. **初始化块的执行：**如果类中包含初始化块（实例初始化块），它们会在构造函数执行之前按照声明的顺序执行。初始化块用于在创建对象时执行一些通用的初始化操作。
    5. **构造函数的执行：**最后，构造函数会被调用，完成对象的实际初始化。构造函数负责执行特定于对象的初始化操作，并可以接收参数来进行定制化的初始化。

    需要注意的是，静态部分的初始化只在类加载时执行一次，而实例部分的初始化在每次创建对象时都会执行。**此外，继承关系中的初始化顺序是先初始化父类的静态部分，然后是子类的静态部分，接着是父类的实例部分，最后是子类的实例部分。**

    总结起来，类的初始化顺序可以简化为：静态成员变量初始化 -> 静态语句块执行 -> 实例成员变量初始化 -> 初始化块执行 -> 构造函数执行。

## 7.反射（todo: 再深入研究下）

Java反射是一种机制，它允许在运行时检查和操作类、接口、字段、方法和构造函数等程序的内部信息。通过反射，可以动态地获取类的成员信息并进行操作，而无需事先知道类的具体细节。

Java的反射功能主要通过`java.lang.reflect`包提供支持。下面是一些常见的反射操作：

1. 获取Class对象：可以使用三种方式获取Class对象：通过类名调用`Class.forName()`方法、通过实例对象调用`getClass()`方法，以及直接使用类名`.class`。
2. 获取类的信息：通过Class对象可以获取类的名称、修饰符、包信息、父类、接口等信息。
3. 获取构造函数：通过Class对象的`getConstructor()`或`getDeclaredConstructor()`方法可以获取类的公共或私有构造函数，并可以使用Constructor对象创建对象实例。
4. 获取字段：通过Class对象的`getField()`或`getDeclaredField()`方法可以获取类的公共或私有字段，并可以使用Field对象读取或修改字段的值。
5. 获取方法：通过Class对象的`getMethod()`或`getDeclaredMethod()`方法可以获取类的公共或私有方法，并可以使用Method对象调用方法。
6. 调用方法：使用Method对象的`invoke()`方法可以调用方法，可以传递实例对象或静态方法的类对象。
7. 调用字段：使用Field对象的`get()`和`set()`方法可以获取和设置字段的值，可以传递实例对象或静态字段的类对象。

反射功能使得在运行时动态地创建对象、调用方法、访问字段等成为可能。它在框架、库和工具的开发中得到广泛应用，但由于反射操作相对较慢，应谨慎使用，并在必要时考虑性能影响。

## 8.异常

在Java中，异常（Exception）是在程序执行过程中发生的意外或异常情况的事件。异常用于表示程序无法正常执行的情况，并提供了一种机制来处理和传播这些异常情况。

Java中的异常分为两种类型：可检查异常（Checked Exception）和不可检查异常（Unchecked Exception）。

1. 可检查异常（Checked Exception）：这些异常在代码中必须显式地进行处理，否则会导致编译错误。可检查异常通常表示程序中遇到的一些外部情况，需要在代码中进行适当的处理。例如，`IOException`、`SQLException`等。
2. 不可检查异常（Unchecked Exception）：这些异常是运行时异常，不需要显式地进行处理，也不会导致编译错误。不可检查异常通常表示程序中的错误或逻辑问题，例如除以零、空指针引用等。常见的不可检查异常包括`NullPointerException`、`IllegalArgumentException`、`ArithmeticException`等。

Java中的异常处理机制是通过 `try-catch-finally` 代码块来实现的。下面是一个简单的异常处理示例：

```java
public class Main {
    public static void main(String[] args) {
        BufferedReader reader = null;
        try {
            // 尝试打开文件并读取内容
            reader = new BufferedReader(new FileReader("file.txt"));
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (IOException e) {
            // 处理可能的 IOException
            System.out.println("文件读取错误: " + e.getMessage());
        } finally {
            // 确保关闭文件资源
            try {
                if (reader != null) {
                    reader.close();
                }
            } catch (IOException e) {
                System.out.println("文件关闭错误: " + e.getMessage());
            }
        }
    }
}
```

在上述示例中，`try` 代码块包含可能抛出异常的代码。如果在 `try` 代码块中发生了异常，程序会跳转到相应的 `catch` 代码块进行异常处理。可以有多个 `catch` 代码块来处理不同类型的异常。`finally` 代码块中的代码无论是否发生异常都会执行。

除了 `try-catch-finally` 机制，还可以使用 `throws` 关键字将异常向上层调用者抛出，让调用者处理异常。

```java
public void someMethod() throws SomeException {
    // 可能抛出 SomeException 的代码
    // ...
}
```

在上述示例中，`someMethod()` 方法声明了 `throws SomeException`，表示该方法可能抛出 `SomeException` 异常。调用该方法的代码需要进行相应的异常处理或再次将异常向上层抛出。

通过合理地处理异常，可以提高程序的健壮性和可靠性，以应对各种异常情况。

## 9.泛型

下面实现了一个传参为泛型数组的泛型方法，会根据不同的传参类型返回对应的结果。

```java
package Generics;

public class GenericMethodExample {
    // 该泛型方法可以接受一个泛型数组的参数，并根据不同类型返回对应相加的结果
    // Number 数组：将数组的元素转为 double 类型相加后返回
    // String 数组：将数组的元素逐个拼接后返回
    public static <T> String processArray(T[] array) {
        if (array.length == 0) {
            throw new IllegalArgumentException("Array is empty");
        }

        if (array[0] instanceof Number) {
            double sum = 0.0;
            for (T element : array) {
                if (element instanceof Number) {
                    sum += ((Number) element).doubleValue();
                } else {
                    throw new IllegalArgumentException("Array contains non-numeric elements");
                }
            }
            return Double.toString(sum);
        } else if (array[0] instanceof String) {
            StringBuilder sb = new StringBuilder();
            for (T element : array) {
                if (element instanceof String) {
                    sb.append((String) element);
                } else {
                    throw new IllegalArgumentException("Array contains non-string elements");
                }
            }
            return sb.toString();
        } else {
            throw new IllegalArgumentException("Unsupported array type");
        }
    }

    public static void main(String[] args) {
        Integer[] numbers = {1, 2, 3, 4, 5};
        Double[] decimalNumbers = {1.5, 2.5, 3.5};
        String[] strings = {"Hello", " ", "World"};

        String sum = processArray(numbers);
        System.out.println("Sum of numbers: " + sum);

        sum = processArray(decimalNumbers);
        System.out.println("Sum of decimalNumbers: " + sum);

        String concatenatedString = processArray(strings);
        System.out.println("Concatenated string: " + concatenatedString);
    }
}
```

## 10.注解

Java注解（Annotation）是一种元数据（metadata）机制，它提供了在代码中添加标记和信息的方式。注解可以用于为程序元素（如类、方法、字段等）添加额外的信息，这些信息可以被编译器、工具和框架用来进行特定的处理。

注解以`@`符号开始，紧跟着注解的名称和一对括号，括号内可以包含参数。下面是一个简单的注解示例：

```java
@Author(name = "John Doe", date = "2022-01-12")
public class MyClass {
    // 类的定义
}
```

在上面的示例中，`@Author`是一个自定义的注解，它有两个参数：`name`和`date`。我们在`MyClass`类上使用了这个注解，并为参数提供了相应的值。

注解可以用于不同的场景和目的，例如：

1. 提供编译器指示：通过注解，可以向编译器提供额外的信息，帮助编译器进行检查和验证。例如，`@Override`注解用于标记方法覆盖（重写）父类方法，如果方法的签名不正确，编译器会给出错误提示。
2. 生成文档：通过注解，可以为代码添加文档和说明信息，这些信息可以被工具（如Javadoc）用来生成文档。例如，`@param`注解用于为方法参数添加说明，`@return`注解用于为方法返回值添加说明。
3. 运行时处理：通过注解，可以在运行时获取类、方法、字段等的注解信息，并根据注解进行相应的处理。这种机制被广泛应用于框架和库中，用于实现各种功能，如依赖注入、数据库映射等。

Java提供了一些内置的注解，如`@Override`、`@Deprecated`、`@SuppressWarnings`等。此外，开发人员还可以自定义注解，通过`@interface`关键字来定义注解，指定注解的元素和默认值。

以下是一个自定义注解的示例：

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyAnnotation {
    String value() default "";
    int count() default 0;
}
```

在上面的示例中，我们定义了一个名为`MyAnnotation`的注解，它有两个元素：`value`和`count`。`@Retention`注解用于指定注解的保留策略，`@Target`注解用于指定注解可以应用的目标元素类型。

使用自定义注解时，可以直接在目标元素上添加注解，并为注解的元素提供值。例如：

```java
public class MyClass {
    @MyAnnotation(value = "example", count = 10)
    public void myMethod() {
        // 方法的定义
    }
}
```

上面的示例中，我们在`myMethod`方法上使用了自定义注解`@MyAnnotation`，并为注解的`value`和`count`元素提供了相应的值。

总结来说，Java注解是一种用于为程序元素添加额外信息的机制。它可以提供编译器指示、生成文档和支持运行时处理等功能。Java提供了内置的注解，同时也允许开发人员自定义注解来满足特定的需求。
