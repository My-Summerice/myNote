## 控制反转与依赖注入

- 控制反转是什么？

​		控制反转（IOC, Inversion of Control），不是一种技术，而使一种设计思想。IOC意味着你需要将设计好的对象交给spring容器控制，而不是传统的在一个对象内部调另一个对象。

- 谁控制谁了？

​		传统的操作是在程序中需要的地方去new一个对象来使用，是我们主动的去控制他的发生，也就是正常的正转；而反转则是将创建和注入对象的这个操作交给spring容器，程序中需要的地方只是被动的去接收这个对象，所以就反过来了。

- 为什么反转？

​		传统的MVC流程会导致你需要在main函数里去从DAO层一直new对象到C层，这很繁琐，且有强关联关系，而使用了IOC后就不需要关注对象的创建，只需要使用注解标注就能在程序运行后从spring容器中拿到对应的对象，使代码耦合度降低，更利于开发与阅读，且能减少在不同地方多次创建同一类对象带来的资源浪费。

**注意：**

​		因为 Spring IOC 容器会统一管理创建和注入对象，所以一般他**只会创建一个对象实例**，并在程序中多处用到的地方进行共享，但是这个**共享可能会带来一定的问题**，比如在程序中的一个地方操作了这个对象的某个属性值，那么在其它需要使用这个对象的地方值都会改变，因为本质上就是同一个实例，所以**可能会对其它程序产生影响**。



#### IOC 与 DI

- DI 是什么？

  DI(Dependency injection, 依赖注入)，主要是指IOC容器将依赖对象注入到目标对象以降低对象之间的强关

  联关系的这个过程。

- IOC 与 DI 的关系？

  在我看来，IOC 是一种规范，一种思想，而 DI 是符合这个思想的一个具体操作方式，**可以把 IOC 想象成代码中的一个 interface ，而 DI 就是来实现这个 interface 的具体类**。



#### 依赖注入的三种方式

- 构造函数注入：

  ```java
  public class MyService {
      private final Dependency dependency;
  
      public MyService(Dependency dependency) {
          this.dependency = dependency;
      }
  
      // ...
  }
  ```

- setter注入：

  ```java
  public class MyService {
      private Dependency dependency;
  
      public void setDependency(Dependency dependency) {
          this.dependency = dependency;
      }
  
      // ...
  }
  ```

- 字段注入：

  通常使用 @Resource 或 @Autowired 注解完成。

  ```java
  public class MyService {
      @Autowired
      private Dependency dependency;
  
      // ...
  }
  ```

