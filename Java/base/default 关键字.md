## default 关键字

#### 1.是什么？

​	default 关键字与 public 、private 同属一类关键字，都是用来修饰方法的作用范围的。

只不过不同的是：

- default 是 Java8 引入的新关键字。
- default 只能用于修饰 interface 中的方法。（它本身也是因为这个而诞生的，后面会详细讲）

#### 2.干嘛的？

​	interface 一直存在一个不太方便的点，那就是如果实现这个接口的类太多，那么如果想完善或补充一个接口的功能，就必须同步修改所有实现了他的类。就比如说你现在想给一个接口新增一个方法签名，那么你必须去之前实现了这个接口的若干个类中都加上该方法的具体实现，这很痛苦，所以在 Java8 中，Java 开发者提供了一个新的关键字 **default** ，而这个关键字的效果就是能让 interface 中的方法拥有具体的实现而不是只能写一个方法签名，这也可以说是改变了接口的传统定义。而所有实现了该接口的类都会默认拥有该方法，可以直接通过类名调用，同样的，它也可以像类的继承那样去重写该方法。这样就可以在新增功能的时候很好地兼容所有的历史代码。

#### 3.怎么用？

- interface:

  ```java
  public interface UserService {
      String getName();
      
      default String writeName() {
          return "真的可以";
      }
      
      default String overrideName() {
          return "没被重写";
      }
  }
  ```

- class:

  ```java
  @Service
  public class UserServiceImpl implements UserService {
      
      public String getName() {
          return "正常的实现接口";
      }
      
      public String defaultName() {
          // 可以用这种方式调用接口的原有默认方法
          return UserService.super.overrideName();
      }
      
      @Override
      public String overrideName() {
          return "被重写啦";
      }
      
  	public static void main(String[] args) {
          UserServiceImpl userService = new UserServiceImpl();
          
          // 正常实现接口
          System.out.println(userService.getName());
          // 接口原有默认方法
          System.out.println(UserService.defaultName());
          // 重写接口原有默认方法
          System.out.println(userService.overrideName());
          // 实现类默认拥有接口的默认方法
          System.out.println(userService.writeName());
      }   
  }
  ```

#### 4.问题？

在使用 default 关键字时确实可能出现一些问题：

- 可以知道 default 这个机制有点类似与类的继承，但是呢，类是只能继承一个类的，而类却可以同时实现多个接口，那么此时就会产生一个问题：

  - 如果多个接口都存在相同的默认方法A（返回值、名称、参数均相同），此时有一个类同时实现了这些接口，那么这个类的A方法究尽用的是哪个接口的呢，没错，这里就产生了冲突，编译器会直接报错，为了解决这个问题，我们可以用重写来覆盖掉这些方法，我们可以在重写的方法中自定义一个新的逻辑，也可以用上面演示过的 interface名.super.方法名来指定使用某个接口的默认方法，从而使该类的A方法成为确定的。

- 同样，我们也知道一个类可以同时继承一个类且实现若干个接口，那么，如果类 A 实现了一个有默认方法 F 的接口 I ，且重写了该方法 F ，那么当类 B 继承类 A 且同时实现接口 I 的时候，类 B 的方法 F 取的是谁的返回值呢？

  ```java
  public class Default extends A implements I {
  
      public static void main(String[] args) {
          Default b = new Default();
          System.out.println(b.F());
      }
  }
  
  class A implements I {
      @Override
      public String F() {
          return "重写了";
      }
  }
  
  interface I {
      default String F() {
          return "返回默认方法";
      }
  }
  ```

  输出：

  ```java
  重写了
  ```

  可以看到输出的是父类重写的方法而不是接口中的默认方法，所以，当这种情况发生的时候，**类的方法的优先级高于接口的默认方法。**



