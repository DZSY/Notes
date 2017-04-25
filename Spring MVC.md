---
typora-copy-images-to: ../Documents/TyporaImages
---

# Spring MVC

# 跟踪一个请求

![BF1451E6-A2AF-4E39-B9E0-BDC10E487D68](/Users/positif/Documents/TyporaImages/BF1451E6-A2AF-4E39-B9E0-BDC10E487D68.png)

1. 当请求离开浏览器时，带有内容、URL、其他信息例如表单信息，提交给前端控制器`DispatcherServlet`
2. `DispatcherServlet`查询一或多个处理器映射来知道下一站是哪个控制器，处理器映射会根据请求携带的URL信息进行决策
3. `DispatcherServlet`将请求发送给控制器后耐心等待控制器处理
4. 控制器处理后产生model、对用户友好的view，表示用于渲染输出的**逻辑视图名称**，打包发回给`DispatcherServlet`
5. `DispatcherServlet`使用视图解析器来将逻辑试图匹配为一个特定的视图实现（可能是JSP）
6. `DispatcherServlet`交付模型数据给视图
7. 视图使用模型数据渲染输出

# 搭建Spring MVC

在网上找了很多用IDEA开发第一个Spring MVC的教程，都失败了（用IDEA run以后发现重定位失败，仍然是tomcat首页，最后发现把tomcat shutdown之后再run就可以成功重定位了

下面是我最后遵照的教程，是使用maven配置的（不用maven的其他教程应该也可以，我没试过，但是要记得shutdown啊

[https://my.oschina.net/gaussik/blog/385697](https://my.oschina.net/gaussik/blog/385697)



## 配置DiapatcherServlet

### 使用XML配置

```xml
<display-name>SpringMVCDemo Web Application</display-name>
  <servlet>
    <servlet-name>mvc-dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>mvc-dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

使用Java配置：

```java
public class SpitterWebInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    /* 指定配置类 */
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { WebConfig.class };
    }

    @Override
    /* 将DispatcherServlet映射到"/" */
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
```