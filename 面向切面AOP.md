<!-- TOC -->

- [1. AOP基础](#1-aop基础)
    - [1.1. AOP的概念](#11-aop的概念)
- [2. 通过切点来选择连接点](#2-通过切点来选择连接点)
    - [2.1. Spring AOP支持的AspectJ切点指示器](#21-spring-aop支持的aspectj切点指示器)
    - [2.2. 切点的应用](#22-切点的应用)
        - [2.2.1. 使用切点](#221-使用切点)
        - [2.2.2. 在的切点中选择bean](#222-在的切点中选择bean)
- [3. 使用JavaConfig注解创建切面](#3-使用javaconfig注解创建切面)
    - [3.1. 定义切面](#31-定义切面)
        - [3.1.3. 利用@Pointcut定义可重用的切点](#313-利用pointcut定义可重用的切点)
        - [3.1.4. 启用AspectJ的自动代理](#314-启用aspectj的自动代理)
    - [3.2. 创建@Around通知](#32-创建around通知)
    - [3.3. 处理通知中的参数](#33-处理通知中的参数)
    - [3.4. 通过注解引入新功能](#34-通过注解引入新功能)
- [4. 在XML中声明切面](#4-在xml中声明切面)

<!-- /TOC -->
# 1. AOP基础

## 1.1. AOP的概念

- 通知（Advice）: 定义了切面是什么/何时使用/何时执行这个工作

- 连接点（Join point）：应用执行过程中能够插入切面的一个点

- 切点（Pointcut）：匹配通知所要织入的一个或多个连接点

- 切面（Aspect）：通知和切点共同定义了切面的全部内容：何时+何处完成功能

- 引入（Introduction）：向现有的类添加新方法/属性

- 织入（Weaving）：切面在指定的连接点被织入到目标对象中；目标对象生命周期织入时机：

  - 编译期：编译时被织入
  - 类加载期：需要`ClassLoader`
  - 运行期：AOP容器为目标对象动态创建一个代理对象，不需要特殊的编译器

  ## Spring的支持和一些基本知识

  1. 基于代理的经典Spring AOP
  2. 纯POJO切面（`aop`命名空间 ）
  3. 使用`@AspectJ`注解驱动的切面（本质上仍然基于代理）
  4. 注入式AspectJ切面（*推荐*）

  ### Java编写Spring通知

  但AspectJ是Java语言扩展，更强大更细粒度，丰富的AOP工具

  ### Spring运行时通知对象

  直到应用需要被代理的bean时（如果是`ApplicationContext`，在`ApplicationContext`从`BeanFactory`中加载所有bean的时候），Spring才创建代理对象

  ### Spring只支持方法级别的连接点

  Spring基于动态代理；AspectJ除了方法切点还提供**字段**和**构造器**接入点

  通过切点来选择连接点




# 2. 通过切点来选择连接点

## 2.1. Spring AOP支持的AspectJ切点指示器

| AspectJ指示器               | 描述                             |
| :----------------------- | ------------------------------ |
| `arg()` / `@args()`      | 限制连接点匹配参数为指定类型的方法 / 由指定注解标注的方法 |
| `execution()`            | 匹配连接点则执行方法                     |
| `this()`                 | 限制连接点匹配AOP代理的bean引用为指定类        |
| `target` /`@target`      | 限制连接点匹配目标对象为指定的类 / 具有指定类型注解的类  |
| `within()` / `@within()` | 限制连接点匹配指定类型 / 由指定注解标注的类型       |
| `@annotation`            | 限定匹配带有指定注解的连接点                 |

如果尝试使用其他AspectJ指示器，会抛出`IllegalArgumentException`异常

## 2.2. 切点的应用

### 2.2.1. 使用切点

`execution (* 包名.类名.方法名(..))` 在方法执行时触发通知，其中：

1. `*`表示返回任意类型
2. `..`表示方法参数列表是任意的

`execution (* 包名.类名.方法名(..)) && within(包名.*))`

1. 表示需要配置的切点仅匹配这个包
2. 当这个包内任意类的方法被调用时执行前面的调用
3. `&& || !`可以用`and or not`代替

### 2.2.2. 在的切点中选择bean

`execution (* 包名.类名.方法名(..)) and bean('bean的id')`为特定ID的bean应用通知

`execution (* 包名.类名.方法名(..)) and !bean('bean的id')` 为除了特定ID以外其他bean应用通知



# 3. 使用JavaConfig注解创建切面

## 3.1. 定义切面

对一个类使用`@Aspect`进行标注，标明这个类不仅是一个POJO（能正常装配为bean），还是一个**切面**。

对其中的方法使用注解来声明**通知方法调用的时机**

| 注解                         | 通知              |
| -------------------------- | --------------- |
| `@After("切点表达式")`          | 在目标方法返回或抛出异常后调用 |
| `@AfterReturning("切点表达式")` | 在目标方法返回后调用      |
| `@AfterThrowing("切点表达式")`  | 在目标方法抛出异常后调用    |
| `@Around("切点表达式")`         | 将目标方法封装起来       |
| `@Before("切点表达式")`         | 在目标方法调用之前执行     |

### 3.1.3. 利用@Pointcut定义可重用的切点

```java
@Pointcut( "execution (** concert.Performance.perform(..))")
public void performance() {} //这个方法本身只是一个标识

//每次使用注解来重用
@Before("performance()")
public void method() {;}
```

### 3.1.4. 启用AspectJ的自动代理

1. 在JavaConfig中启用 ： 在**配置类**上使用`@EnableAspectJAutoProxy`注解
2. 在XML中加入`<aop:aspectj-autoproxy>`
3. 启用自动代理后，就会自动为所有使用`@Aspect`注解的bean创建一个代理

这种方法仍然限于代理方法的调用，如果想利用AspectJ的所有能力，必须在运行时使用AspectJ并且不依赖Spring来创建基于代理的切面

## 3.2. 创建@Around通知

- 最为强大，相当于把一个将被通知的目标方法包装起来
- 用`@Around`注解的环绕通知方法必须有一个`ProceedingJoinPoint`对象作为参数
- 调用`ProceedingJoinPoint`对象的`proceed()`方法就是调用被通知的方法
  - 可以不调用：阻塞对被通知方法的调用
  - 可以多次调用：eg.重试逻辑
- 方法内可以做任何事情，在任何地方调用`proceed()`方法以指定目标方法调用的时机

## 3.3. 处理通知中的参数

指定参数，会传递到通知方法中，例如：

```java
@Pointcut( "execution(* soundsystem.CD.playTrack(int))" + 
           "&& args(trackNumber)")
public void trackPlayed(int trackNumber) {}

@Before("trackPlayed(trackNumber)")
public void countTrack(int trackNumber) {; /*在播放前计数*/}
```

这样传递给`playTrack()`方法的int也会传递到通知方法`countTrack()`中去

## 3.4. 通过注解引入新功能

前面讲到的都是为已有的方法添加新功能，现在要为一个对象**增加新的方法**；当引入接口的方法被调用时，代理会把此调用委托给实现了新接口的某个其他对象（这样一个bean的实现被拆分到了多个类中

借助于AOP的引入功能，可以不必改动现有的实现，只需要创建一个新的切面，通过`@DeclareParents`注解来把某个接口引入到某个bean中：

```java
@Aspect
public class EncoreableIntroducer {
  /**
   * value属性指定了bean类型
   **/
  @DeclareParents( value="concert.Performance+",
                   defaultImpl=DefaultEncoreable.class)
  public static Encoreable encoreable;
}
```

三部分组成：

- value属性指定了要引入该接口的bean的类型，这里`+`表示是这个类的**子类型**而不是这个类本身
- defaultImpl属性指定了为引入的功能提供实现的类
- 用static标注引入的接口名





# 4. 在XML中声明切面

没有源码，或不想把注解放到代码里，还可以选择在XML中声明；以非侵入性的方式声明切面

| AOP配置元素                                  | 用途                   |
| ---------------------------------------- | -------------------- |
| `<aop:config>`                           | 顶层配置元素               |
| `<aop:advisor>`                          | 定义AOP通知器             |
| `<aop:after>` / `<aop:after-returning>` / `<aop:around>` | 定义AOP在*各种时机*的通知      |
| `<aop:aspect>`                           | 定义一个切面               |
| `<aop:aspectj-autoproxy>`                | 启用`@Aspect`注解驱动的切面   |
| `<aop:declare-parents>`                  | 以透明的方式为被通知的对象引入额外的接口 |
| `<aop:pointcut>`                         | 定义一个切点               |

> 真的很讨厌XML，能不学就不学吧