### Spring 的 @ModelAttribute

一般来说，数据或对象是通过Controller层的`@ModelAttribute`注解添加进 Spring的 model 中；在一个request到达时，任何被`@ModelAttribute`注解的方法都会在Controller的handler方法之前调用，在handler方法内就可以使用Model中的bean了

```java
//model的key值为myRequestObject
@ModelAttribute("myRequestObject")
public MyCommandBean addStuffToRequestScope() {
	MyCommandBean bean = new MyCommandBean("Hello World",42);
  	//返回一个Bean的实例到指定的model
	return bean;
}
```

Spring model的数据存储在Java request作用域内，

为什么不直接把数据加到request对象中？以下是某本书中的解释

> 直接将元素加入 HttpServletRequest（像 request attributes 一样）看起来就像在服务同样的目标。这样做的理由是当看到我们为 MVC 框架设置的 requirements 时，能够更明确。它应尽可能与视图无关，这意味着我们可以合并视图技术，并不受 HttpServletRequest 的束缚。

