# AOP

aop（Aspect Oriented Programming) 翻译过来就是面向方面/切面编程

aop是一种思想而不是一种技术。

## AspectJ和Spring AOP，这两个的核心区别：

- AspectJ需要用到额外的编译器，这个比较麻烦，需要在工程外处理，不易于移植。AOP是在jdk基础上实现了，不用额外添加jdk外的执行文件。
- 因为是编译器期执行，在运行时没有开销，所以性能上AspectJ肯定是强于AOP的。

### Spring AOP默认的使用方式：

- 如果目标对象没有实现接口，则默认会采用CGLIB代理； 

- 如果目标对象实现了接口，默认会采用Java动态代理\

## 代理

https://segmentfault.com/a/1190000015262333

- 静态代理

- 动态代理

  - JDK的动态代理    :针对实现了接口的类产生代理.
  - Cglib的动态代理    :针对没有实现接口的类产生代理. 应用的是底层的字节码增强的技术 生成当前类的子类对象.


## 什么是AspectJ?

在网上一搜一大片所谓AspectJ的用法，其实都是AspectJ的“切面语法”，只是AspectJ框架的冰山一角，AspectJ是完全独立于Spring存在的一个Eclipse发起的项目，官方关于AspectJ的描述是：

> Eclipse AspectJ is a seamless aspect-oriented extension to the Java™ programming language. It is Java platform compatible easy to learn and use.

是的AspectJ甚至可以说是一门独立的语言，我们常看到的在spring中用的@Aspect注解只不过是Spring2.0以后使用了AspectJ的风格而已本质上还是Spring的原生实现，关于这点Spring的手册中有提及：

> @AspectJ使用了Java 5的注解，可以将切面声明为普通的Java类。@AspectJ样式在AspectJ 5发布的AspectJ project部分中被引入。Spring 2.0使用了和AspectJ 5一样的注解，并使用AspectJ来做切入点解析和匹配。但是，AOP在运行时仍旧是`纯的Spring AOP`，并不依赖于AspectJ的编译器或者织入器（weaver）。

so 我们常用的org.aspectj.lang.annotation包下的AspectJ相关注解只是使用了AspectJ的样式，至于全套的AspectJ以及织入器，那完全是另一套独立的东西。



AspectJ是Eclipse旗下的一个项目。至于它和Spring AOP的关系，不妨可将Spring AOP看成是Spring这个庞大的集成框架为了集成AspectJ而出现的一个模块。

毕竟很多地方都是直接用到AspectJ里面的代码。典型的比如@Aspect，@Around，@Pointcut注解等等。而且从相关概念以及语法结构上而言，两者其实非常非常相似。比如Pointcut的表达式语法以及Advice的种类，都是一样一样的。

那么，它们的区别在哪里呢？

最大的区别在于两者实现AOP的底层原理不太一样：

Spring AOP: 基于代理(Proxying)
AspectJ: 基于字节码操作(Bytecode Manipulation)
用一张图来表示AspectJ使用的字节码操作，就一目了然了：



通过编织阶段(Weaving Phase)，对目标Java类型的字节码进行操作，将需要的Advice逻辑给编织进去，形成新的字节码。毕竟JVM执行的都是Java源代码编译后得到的字节码，所以AspectJ相当于在这个过程中做了一点手脚，让Advice能够参与进来。

https://blog.csdn.net/dm_vincent/article/details/57526325

https://zhuanlan.zhihu.com/p/25522841

# spring事务

## 小心 Spring 的事务可能没有生效

生效原则 1，

除非特殊配置（比如使用 AspectJ 静态织入实现 AOP），否则只有定义在 public 方法上的 @Transactional 才能生效。

原因是，Spring 默认通过动态代理的方式实现 AOP，对目标方法进行增强，private 方法无法代理到，Spring 自然也无法动态增强事务处理逻辑。

CGLIB 通过继承方式实现代理类，private 方法在子类不可见，自然也就无法进行事务增强；



生效原则 2，

必须通过代理过的类从外部调用目标方法才能生效。

this 指针代表对象自己，Spring 不可能注入 this，所以通过 this 访问方法必然不是代理。



## 事务即便生效也不一定能回滚

第一，只有异常传播出了标记了 @Transactional 注解的方法，事务才能回滚

第二，默认情况下，出现 RuntimeException（非受检异常）或 Error 的时候，Spring 才会回滚事务。

​	除了RuntimeException以外的异常，都属于checkedException，它们都在java.lang库内部定义。Java编译器要求程序必须捕获或声明抛出这种异常。

第三，方法内 catch 了所有异常，异常无法从方法传播出去，事务自然无法回滚。

```java

@Service
@Slf4j
public class UserService {
    @Autowired
    private UserRepository userRepository;
    
    //异常无法传播出方法，导致事务无法回滚
    //因为运行时异常没有逃出了 @Transactional 注解标记的 createUserWrong1 方法，Spring 当然不会回滚事务了
    @Transactional
    public void createUserWrong1(String name) {
        try {
            userRepository.save(new UserEntity(name));
            throw new RuntimeException("error");
        } catch (Exception ex) {
            log.error("create user failed", ex);
        }
    }

    //即使出了受检异常也无法让事务回滚
    @Transactional
    public void createUserWrong2(String name) throws IOException {
        userRepository.save(new UserEntity(name));
        otherTask();
    }

    //因为文件不存在，一定会抛出一个IOException
    private void otherTask() throws IOException {
        Files.readAllLines(Paths.get("file-that-not-exist"));
    }
}
```



![img](../../../pic/markdown/2019101117003396.png)

如何解决:

- 可以手动设置让当前事务处于回滚状态：

- ```java
  try { userRepository.save(new UserEntity(name)); throw new RuntimeException("error"); } catch (Exception ex) { log.error("create user failed", ex); TransactionAspectSupport.currentTransactionStatus().setRollbackOnly(); }
  ```

- @Transactional(rollbackFor = Exception.class)

  - 期望遇到所有的 Exception 都回滚事务（来突破默认不回滚受检异常的限制）：

## 请确认事务传播配置是否符合自己的业务逻辑

### Spring中七种事务传播行为

| 事务传播行为类型          | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是最常见的选择。 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| PROPAGATION_MANDATORY     | 使用当前的事务，如果当前没有事务，就抛出异常。               |
| PROPAGATION_REQUIRES_NEW  | 新建事务，如果当前存在事务，把当前事务挂起。                 |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。   |
| PROPAGATION_NEVER         | 以非事务方式执行，如果当前存在事务，则抛出异常。             |
| PROPAGATION_NESTED        | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

https://segmentfault.com/a/1190000013341344



```java

@Transactional
public void createUserRight(UserEntity entity) {
    createMainUser(entity);
    try{
        
        //会回滚,因为子事务回滚标记了回滚,但是此方法被捕获异常，按道理是不会回滚的
        //原因是，主方法注册主用户的逻辑和子方法注册子用户的逻辑是同一个事务，子逻辑标记了事务需要回滚，主逻辑自然也不能提交了。
        //subUserService.createSubUserWithExceptionRight1(entity);
        
        //不会回滚,子事务是一个新开的事务,和主事务无关
        //subUserService.createSubUserWithExceptionRight2(entity);
    } catch (Exception ex) {
        // 捕获异常，防止主方法回滚
        log.error("create sub user error:{}", ex.getMessage());
    }
}
```

```

@Transactional(propagation = Propagation.REQUIRES_NEW)
public void createSubUserWithExceptionRight1(UserEntity entity) {
    log.info("createSubUserWithExceptionRight start");
    userRepository.save(entity);
    throw new RuntimeException("invalid status");
}
```

```java

@Transactional
public void createSubUserWithExceptionRight2(UserEntity entity) {
    log.info("createSubUserWithExceptionRight start");
    userRepository.save(entity);
    throw new RuntimeException("invalid status");
}
```



























