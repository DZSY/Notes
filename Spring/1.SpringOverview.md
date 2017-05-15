# 采取4种关键策略来简化Java开发

1. 基于POJO的轻量级和最小侵入性编程(依然是POJO 框架不绑死应用
2. 通过依赖注入(DI)和面向接口实现松散耦合(eg:构造器注入
3. 基于切面和惯例进行声明式编程(
4. 1. 面向切面编程AOP 模块化 各种功能层(安全/日志/事务)包裹核心业务层
   2. 可在`<aop:config>`中把某个bean声明为一个切面 让其在其他bean并不知道它的存在的情况下应用于其他bean
5. 通过切面和模板减少样板式代码(例如JDBC封装成JdbcTemplate

# Container

是Spring框架的核心 倾向于使用ApplicationContext来实现

# 应用上下文的类型

# bean的生命周期

# 6个重要的Spring模块:

![A1863C91-A210-4308-88C4-DA7380BC0DC3](/var/folders/l1/lmxknrlj03v0k3d2vzcfmhlh0000gn/T/com.evernote.Evernote/com.evernote.Evernote/WebKitDnD.Wrf8yq/A1863C91-A210-4308-88C4-DA7380BC0DC3.png)

# Spring应用

Spring Portfolio(包括构建于核心Spring框架之上的框架和类库:

Spring Web Flow: Spring MVC框架之上 为基于流程的会话式Web应用提供支持

Spring Web Service: 服务的实现都是为了满足服务的契约而编写

Spring Security: 切面/添加到应用的Web层/如何保护方法调用

Spring Integration: 与其他应用进行交互

Spring Batch: 批处理应用 对数据进行大量操作

Spring Data: 为多种数据库类型提供自动化的仓库机制

Spring Social: 通过REST API连接Spring应用

Spring Mobile: 支持移动Web应用开发

Spring for Android: 为基于Android设备的本地应用提供简单的支持

Spring Boot:简化Spring