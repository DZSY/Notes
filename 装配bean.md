
[TOC]
## 1. 三种装配机制 
###	1.1. 隐式的bean发现机制和自动装配(最强大
- 组件扫描(自动发现应用上下文中所创建的bean
- 对组件使用`@Component`注解 (或使用`@Named`
  - bean的默认id是**首字母小写**的类名
  - `@Component("id名")`指定bean的id
- 启用组件扫描
  - 方法1:**配置类**加上`@ComponentScan`注解,会在包内扫描
  - `@Configuration("包名")`扫描指定的包
  - `@Configuration(basePackages={"包名1","包名2"})`扫描指定基础包(以String类型名指定包，类型不安全
  - `@Configuration(basePackageClasses={"类1.class","类2.class"})`扫描这些类所在的包
  - 方法2:`<beans>`内加入`<context:component-scan base-package="包名">`
- 自动装配 : 使用`@Autowired`注解(或使用`@Inject`
- 自动满足bean之间的依赖 / 寻找匹配某bean需求的其他bean
- 可以用在类的任何方法上
- 有且只有一个bean匹配需求 : 装配
- 没有匹配的bean : 抛出异常
  - 避免抛出异常 : `@Autowired(required=false)` (但要进行null检查
- 多个匹配 : 抛出异常（后面会专门处理自动装配的歧义性

### 1.2. 显式配置
#### 1.2.1. 在Java中进行显式配置
1. 创建**配置类** : 添加`@Configuration`注解
2. 为方法添加`@Bean`注解，会为**返回的对象**创建bean
- 默认id=方法名
- `@Bean(name="id名")`指定id
3. 装配bean
- 引用一个其他bean的创建方法(默认bean都是**单例singleton**的,每次引用都会是**同一个bean实例**


#### 1.2.2 在XML中显式配置
> 不喜XML,如果以后要用到再做笔记

## 2. Spring profile
根据环境决定该创建哪个bean和不创建哪个bean（运行时确定
### 2.1. 指定bean属于profile
- 用`@Profile("profile名")`注解一个类，则这个类中的bean在某个profile激活时才会创建
- Spring3.2开始可以在方法上注解
- XML中直接`<beans profile="profile名">`


### 2.2. 激活profile
- DispatcherServlet的初始化参数

```xml
<!--为上下文设置默认profile-->
<context-param>
	<param-name>spring.profiles.default</param-name>
	<param-value>profile名1</param-value>
</context-param>

<!--为Servlet设置默认profile-->
<init-param>
	<param-name>spring.profiles.default</param-name>
	<param-value>profile名2</param-value>
</init-param>
```


- Web应用的上下文参数
- JNDI条目
- 环境变量
- JVM系统属性
- 在集成测试类上使用`@ActiveProfiles("profile名")`注解设置


##3. Spring4.x中:条件化的bean
在带有`@Bean`的方法上使用`@Condition`，true才创建bean

- eg. `@Condition(类1.class)` 
- 类1必须实现`Condition`接口，提供`match()`方法的实现

##4. 处理自动装配的歧义性
###4.1. 标示首选的bean
- 在`@Conponent`或`@Bean`注解的后面添加`@Primary`注解
- 或在`<bean ... primary="true">`
- 不能标示多个bean（相当于没标示


###4.2. 限定自动装配的bean
- 在`@Autowired`/`@Inject`注解后面使用`@Qualifier("bean的限定符")`
- 可以限定符=默认的bean的id，但重构了类以后会无法匹配
- 设置bean的限定符
- 在`@Component`/`@Autowired`/`Bean`的下方都可以分配限定符:`@Qualifier("限定符名")`
- 限定符最好选择一个和bean特征相符的**描述性术语**
- 如果撞了限定符，也不能再加一个`@Qualifier`注解，可以使用自定义的限定符注解:
  - 这样就可以同时使用两个注解`@限定符1`，`@限定符2`了:

```java
@Qualifier
public @interface 自定义限定符名 {}
```

##5. bean的作用域

1. 单例Singleton: 整个应用中一个
2. 原型Prototype: 每次**注入**或者**通过ApplicationContext获取**时创建
- 在`@Bean`下面使用`@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)`注解
- 或`<bean ... scope="prototype">`
3. 会话Session: 每个会话创建
4. 请求Request: 每个请求创建


- JavaConfig配置方法
   - `proxyMode`设置为`ScopedProxyMode.INTERFACES`后，每次会话/请求时，把一个**到这个bean的代理**注入到单例bean中，每当单例bean调用这个bean的方法，代理会对其进行懒惰解析并将调用委托给作用域内实际的bean（如果这个bean是接口就可以，如果是具体类必须设置为`ScopedProxyMode.TARFER_CLASS`然后使用CGLib来生成基于类的代理

```java
@Scope(
	value=WebApplicationContext.SCOPE_SESSION,
	proxyMode=ScopedProxyMode.INTERFACES)
```
- XML配置方法
  - 默认情况下使用CGLib创建目标类代理，如果是接口就使用`<aop:scoped-proxy oroxy-target-class="false"/>`

```xml
<bean 
	id="test"
	class="com.myapp.Test"
	scope="session">
	<aop:scoped-proxy />
</bean>
```
##6. 运行时值注入

属性占位符（Property placeholder）/ Spring表达式语言 (SpEL) 两种方式


###6.1. 注入外部的值
1. 在配置类中声明属性源`@PropertySource("classpath:/com/x/y.properties")`，这样属性文件就被加载到Spring的`Environment`中了
2. 然后通过`Environment`的`getProperty("属性名")`来检索属性，得到属性值


####6.1.1 深入学习Environment（可以调用的方法）

```java
String getProperty(String key);
//指定属性不存在时使用一个默认值
String getProperty(String key, String defaultValue);
//eg. Class<T>填入Integer.class
T getProperty(String key, Class<T> type);
T getProperty(String key, Class<T> type, T defaultValue);
```
以上，如果没有指定默认值且该属性没有定义会获取到null
如果希望这个属性必须要定义可以调用`getRequiredProperty()`方法，没有定义会抛出异常
检查属性是否存在，可以调用`containsProperty()`方法
如果想将属性解析为类，可以调用`getPropertyAsClass(String key, 类.class)`方法

######6.1.1.1 检查profile的激活

```java
//返回激活状态的profile名称
String[] getActiveProfiles();
//返回默认profile的名称
String[] getDefaultProfiles();
//environment是否支持给定profile
boolean acceptsProfiles(String[] profiles);
```
####6.1.2 解析属性占位符
- 属性占位符 : `"${属性名}"`
- 解析外部属性能将值的处理推迟到运行时，根据名称解析
- 如果要使用占位符，必须配置一个`PropertyPlaceholderConfigurer`的bean或`PropertySourcesPlaceholderConfigurer`的bean，推荐使用后者，基于Environment解析：


```java
@Bean
public static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
	return new PropertySourcesPlaceholderConfigurer();
}
```
- 使用XML配置:加入`<context:property-placeholder />`

###6.2. 使用Spring表达式语言SpEL进行装配
- 常量 `#{1}`，`#{'Hello'}`
- 使用bean的id引用bean `#{bean的id名}`
- 调用方法 `#{bean的id名.方法名()}`
- 访问对象的属性 `#{bean的id名.属性名}`
- 引用系统属性 `#{systemProperties['属性名']}`
- 访问类作用域的方法 `#{T(java.lang.Math').random()}`
- 访问类作用域的值 `#{T(java.lang.Math').PI}`
- 算术运算 `+ - * / % ^`
- 关系运算 `< > == <= >= lt gt eq le ge`
- 逻辑运算 `and or not |`
- 条件运算 三元`x.y > 10 ? 4 : 5` 或 `x.z?: 1`
- 正则表达式匹配 `#{admin.email matchs '[a-zA-z0-9_]+@[a-zA-z]+\\.com'}`
- 对集合操作 
- `#{jukebox.songs[4].title}`取index=4的元素
- `#{jukebox.songs.?[artist eq 'Dzsy'].title}`过滤得到Dzsy**所有**歌曲的title
- `.^[]`过滤得到第一个匹配项
- `.$[]`过滤得到最后一个匹配项
- `#{jukebox.![title]}`得到所有歌曲title的集合
- 尽量让表达式保持简洁和准确，否则测试会困难




