# JSTL核心标签库

## 在JSP中输出内容

`<c:out value="EL表达式" />`

1. 默认对XML字符转义以避免跨站脚本攻击、各种注入攻击，可以设置`escapeXml="false"`
2. 指定当value=null时使用的默认值`default="Default value"`，可以使用EL表达式
3. value中EL表达式返回值强制转化为字符串输出，除非是`java.io.Reader`，将读取其中的内容写入到输出

## 对URL进行操作

`<c:url value = "http://www.example.net/today.jsp" />`

1. 正确地对URL编码