## 面向切面编程（AOP）

#### 1.什么是切面？

​		可以简单的理解为将同一层级的不同模块代码需要干的相同的事情统一由另一个模块来执行。

举例：

​		比如大家熟知的日志操作，在同一个service类中有很多不同的逻辑需要执行，而他们都需要执行相同的添加日志操作，那么就可以将本应该写在每个函数中的日志操作提出来作为一个切面执行，这样，在这个类中的所有业务逻辑在执行到一定步骤的时候都会统一的去执行一下记录日志操作，在整个执行流程看来，这个操作就像是把不同的方法从中间切开后加上了一个都需要执行的逻辑，所以称之为切面。

像这样：

```java
public class UserServiceImpl implements IUserService {

    /**
     * Find user list.
     *
     * @return user list
     */
    @Override
    public List<User> findUserList() {
        System.out.println("execute method: findUserList");
        return Collections.singletonList(newUser("pdai", 18));
    }

    /**
     * Add user
     */
    @Override
    public void addUser() {
        System.out.println("execute method: addUser");
        // do something
    }
}
```

这张图片很形象：

![](https://pdai.tech/images/spring/springframework/spring-framework-aop-4.png)

#### 2.AOP 的相关概念（不是spring特有的）

- **连接点（Jointpoint）：**表示在程序中需要插入切面的点，这个点可以是类、方法、字段甚至是异常的相关操作等等。（spring 只支持方法执行连接点）
- **切入点（Pointcut）：**一组相关的连接点的集合，在spring中一般可以用perl5正则表达式和AspectJ切入点模式来匹配一组连接点。
- **通知（Advice）：**在连接点上执行的具体行为。包含：前置通知（before advice）、后置通知（after advice）、环绕通知（around advice）等。
- **引入（inter-type declaration）：**在连接点处为已有的类添加新的字段或方法。（spring 允许引入新的接口，但接口必须至少有一个实现类）
- **切面（Aspect）：**可以认为是**切入点、引入、通知**的组合。（spring 中一般使用 @AspectJ 来定义一个界面类）
- **目标对象（Target Object）：**即切入点指定的对象，需要被通知的对象。（spring 的 AOP 通过代理模式实现，所以这些目标对象也被称为被代理对象）
- **织入（Weaving）：**即把切面连接到目标对象（被通知对象）的过程。这个过程一般在编译时、类加载时和运行时完成。（spring 在运行时完成织入）
- **AOP代理（AOP Proxy）：**AOP框架使用代理模式创建的对象，也就是定义通知内容的对象，这个对象能在连接点织入通知从而使目标对象执行切面。

##### 2.1 通知类型

- **前置通知（Before advice）**：在某连接点之前执行的通知，但这个通知不能阻止连接点之前的执行流程（除非它抛出一个异常）。
- **后置通知（After returning advice）**：在某连接点正常完成后执行的通知：例如，一个方法没有抛出任何异常，正常返回。
- **异常通知（After throwing advice）**：在方法抛出异常退出时执行的通知。
- **最终通知（After (finally) advice）**：当某连接点退出的时候执行的通知（不论是正常返回还是异常退出）。
- **环绕通知（Around Advice）**：包围一个连接点的通知，如方法调用。这是最强大的一种通知类型。环绕通知可以在方法调用前后完成自定义的行为。它也会选择是否继续执行连接点或直接返回它自己的返回值或抛出异常来结束执行。

​		环绕通知是最常用的通知类型。和AspectJ一样，Spring提供所有类型的通知，**推荐使用尽可能简单的通知类型来实现需要的功能**。例如，如果你只是需要一个方法的返回值来更新缓存，最好使用后置通知而不是环绕通知，尽管环绕通知也能完成同样的事情。用最合适的通知类型可以使得编程模型变得简单，并且能够避免很多潜在的错误。比如，你不需要在JoinPoint上调用用于环绕通知的proceed()方法，就不会有调用的问题。

#### 3.AOP的配置方式

​	Spring AOP 的配置方式分为XML配置和使用@Aspect注解这两种方式，但是一般复杂的AOP都是用@Aspect来配置，否则XML中的东西太多太乱。

我们现在来定义一个service层接口代码并用@Aspect注解来定义切面：

```java
public interface AopService {
    void funcA();
    String funcB();
    S
}
```

