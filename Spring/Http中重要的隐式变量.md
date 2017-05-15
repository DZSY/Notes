# Http中重要的隐式变量

## pageContext

`pageContext = _jspxFactory.getPageContext(this, request, response, null, true, 8192, true);`

`PageContext`的实例（编写自定义jsp标签很有用）

## request与response

request是`HttpServletRequest`的实例

response是`HttpServletResponse`的实例

在JSP中能够获取请求参数、获取和设置特性、从响应正文中读取内容

## session

`session = pageContext.getSession`

`HttpSession`的实例

默认在所有的JSP中都可以使用（除非page指令的session特性为null）

## out

`out = pageContext.getOut()`

`JspWriter`的实例，能够直接向响应中输出内容

## application

`application = pageContext.getServletContext()`

`ServletContext`接口的实例，提供对Web应用程序配置的访问，包括上下文初始化参数

## config

`config = pageContext.getServletConfig()`

`ServletConfig`接口的一个实例，访问JSP Servlet的配置

## page

`page = this`

可以强制转换为`Servlet`/`JspPage`/`HttpJspPage`

## exception



