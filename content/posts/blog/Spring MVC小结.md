---
title: "Spring MVC小结"
date: 2022-02-02T02:45:59+08:00
draft: false
---
​
> description：本打算直接干到Spring MVC源码的，中途发现了算法的重要性，于是学习过半而中道崩殂，今学习三分，先把整套数据结构知识恶补，再刷常见题。刷完常见题转leetcode每日一题 + other knowledge。以下是Spring MVC常见的知识，或者你可以把它理解为 “面试题”，本人并不喜欢这么称呼，emm...正文开始。

### Spring MVC的优点有哪些？
1. 可以支持各种视图技术,而不仅仅局限于JSP；
2. 与Spring框架集成（如IOC容器、AOP等）；
3. 清晰的角色分配：
    1.  前端控制器(dispatcherServlet) ；
    2.  请求到处理器映射（handlerMapping)；
    3.  处理器适配器（HandlerAdapter)；
    4. 视图解析器（ViewResolver）；

 4. 支持各种请求资源的映射策略；
### DispatcherServlet处理流程
Spring MVC框架的控制器（Controller）解析用户输入并将其转换为一个由视图呈现给用户的模型。

控制器的核心DispatcherServlet处理请求和响应，处理流程如下：
1. DispatcherServlet向处理器映射器（HandlerMapping）请求获取Handler。
2. 返回Handler给DispatcherServlet。
3. DispatcherServlet向处理器适配器（HandlerAdapter）请求执行Handler并返回视图ModelAndView给DispatcherServlet。
4. 向视图解析器（ViewResolver）通过视图名称查找视图并返回给DispatcherServlet真正的视图对象。
5. 进行视图的渲染并返回给DispatcherServlet渲染后的视图。相当于给了controller控制器。![在这里插入图片描述](https://img-blog.csdnimg.cn/4db704ad54004a7f81659dbaf7e85dc5.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBAcnh5X25vdF9maXZl,size_20,color_FFFFFF,t_70,g_se,x_16#pic_center)
*以上png为转载，如有侵权，请联系本人。*
### 常见注解
**@ModelAttribute**：
该注解可以存一个对象，放在一个方法上存对象，依次set值，调用时只需要在方法的参数列表的参数前加上@ModelAttribute(”存放的对象名”)；有该注解的方法会率先执行。如果在方法上没有指定名称，则默认使用参数类型的首字母小写来获取，如果指定了名称，就根据指定名称来获取。  
最近工作常用该注解，有了一个更全面的认知：  
@ModelAttribute 的作用是把请求参数绑定到 model 对象上，一般用法是只加在方法体上，如果该方法返回某一类，那返回的 model 属性的名称则为类名（自动首字母小写）：隐含model.addAttribute("user","user")，例如返回值类型为 Person 自动转换为 person，如果不想要这个名称就在注解中加上 value 属性来指定 model 属性的名称，那返回的视图名就是 person ，那就意味着这个方法会优先于其他方法执行，譬如他会在里面 new 一个对象，然后往里面 set 值并把这个对象 addAttribute 到 model 了，在其他方法中是可以通过 model 拿到这个对象里的值的。如果是没有返回值，那就要在方法体内手动addAttribute 到 model 了。  
另一种常见用法是在方法体上加上该注解并在其他方法中参数列表的某参数前加上该注解，就意味着从 model 中获取值，拿到的是返回的 “model 属性名称” 为 “方法体上@ModelAttribute注解 value 的值” 的 model 中对象的参数。  
还有一种是一直困扰我的直接从 Form 表单或 URL 能拿到指定对象，这样只需要在参数前加注解而不用在方法体上。

**@ResponseBody**：
该注解实现将controller方法返回的java对象转化为json对象响应给客户。
换句话说：表示当前请求的内容直接作为响应体，用于接收。

注意：在使用 @RequestMapping后，返回值通常解析为跳转路径，但是加上 @ResponseBody 后返回结果不会被解析为跳转路径，而是直接写入 HTTP response body 中。 比如异步获取 json 数据，加上 @ResponseBody 后，会直接返回 json 数据。

**@RequestBody**：
注解实现接收http请求的json数据，将json转换为java对象。
换句话说：拿到请求当前页面request请求的body并交由对应参数，放在方法的参数列表的某个参数前。

eg：@RequestBody String body

注：@ResponseBody ＋ @Controller == @RestController

**@PathVariable**和**@RequestMapping**：
url里是可以在路径传参的，在@RequestMaping的方法构造函数列表去拿到就行了。如果用@PathVariable注解就是在@requestMapping路径后接“/{参数名}”，方法名中加@PathVariable（”路径中的参数名”） 参数类型 方法内参数名，多个参数就再接多个“/{路径中的参数名}”。其实获取参数能直接在路径后？name=“xxx”，然后在@requestMapping里如果参数名称相同就能直接获取到，不能就要使用注解@RequestParam。@PathVariable和和@RequestParam不要混淆，有区别：1.用法上不同，@PathVariable在路径后接”/{参数名}“，@RequestParam读取？后的name=”参数“，这个@RequestParam有三个参数，

1、value：获取的参数值（是人是鬼都知道）。

2、required：表示当前属性值是否必须存在（默认是true）。

3、defaultValue：如果value传递参数了，使用参数，如果没有，使用默认值。

@RequestHeader、@CookieValue也是一样的这三个参数。

### rest中的get、post、delete、put请求
get是获取资源、post新建资源、put更新资源、delete删除资源。  
在选择method只能发送post和get请求，因此可以用过滤器filter将post请求转化为put、delete请求以达到put、delete的效果。
### 编码问题详解
编码问题无外乎两种情况：
1. post： 或者在web.xml配置一个CharacterEncodingFilter过滤器，设置为utf-8。
2. get：在tomcat的server.xml文件中，添加URLEncoding=utf-8。或者对request、response参数重新编码。
3. 注意：过滤器顺序：一个应用程序中可能会包含N个过滤器，这N个过滤器一般是没有顺序要求的，但是如果设置了编码过滤器，那么一定要把它放到最上面，保证过滤器的运行。不论是Spring MVC自带的编码器还是自定义的，都要这样。
### 后端向前端传值的方式
前面都是前端往后端传数据，现在看后端往前端传数据


前面都是前端往后端传数据，现在看后端往前端传数据

1、map.put(”msg”,”hello data”)，在参数列表加Map。

2、model.addAttribute(”mac”,”hello mac”)，在参数列表中加Model。

3、modelMap.addAttribute(”mac”,”hello mac”)，在参数列表中加ModelMap。

4、也可以用ModelAndView传递数据，方法内new一个modelAndView并且setViewName(”页面路径”)，再addObject(”mac”,”hello mac”);return的是modelAndView，上述三种返回的是页面路径。

以上都可以用于数据回显，前3种方式的回显数据保存在哪个作用域？

1、page：当前页面

2、request ：当前请求        （保存在这）

3、session：当前会话

4、application：当前应用

当使用上述（map、model、modelMap）参数传递数据时会把数据都放置到Request作用域。

如果要存在session中：在类名上+@SessionAttributes(”msg”);该注解表示每次向request中设置属性值时顺带向session中保存一份。
### 转发、重定向
**转发**：在返回值前面加"forward:"，譬如"forward:user.do?name=method4”

**重定向**：在返回值前面加"redirect:"，譬如"redirect:[http://www.baidu.com](http://www.baidu.com/)"

### 处理静态资源
在处理静态资源时（例如图片），不做处理的话所有请求都会交由dispatcherServlet来处理，但是dispatcherServlet中没有处理静态资源的逻辑，所以访问不到，添加mvc默认配置后就可以了，由

```java
<mvc:default-servlet-hanlder/><mvc:annotation-driven>
```
### SpringMVC和struct2的区别
1. SpringMVC的入口是一个servlet即前端控制器（DispatchServlet），而struts2入口是一个filter过虑器（StrutsPrepareAndExecuteFilter）
2. SpringMVC是基于方法开发（一个url对应一个方法），请求参数传递到方法的形参，可以设计为单例、多例。struct2是基于类开发，传递参数通过类的属性，只能设计为多例。
### SpringMVC异常处理
1. 可以抛给Spring框架来处理。
2. 可以配置简单的异常处理器，在异常处理器中添加视图页面即可。
### SpringMVC的控制器是不是单例模式？如果是，有什么问题？怎么解决？
是单例模式。
在多线程访问的时候有线程安全问题。
解决方案是在控制器里面不能写可变状态量，如果需要使用这些可变状态，可以使用ThreadLocal机制解决，为每个线程单独生成一份变量副本，独立操作，互不影响。
### SpringMVC 里面拦截器是怎么写
1. 实现 HandlerInterceptor 接口；
2. 继承适配器类，接着在接口方法当中，实现处理逻辑，然后在 SpringMVC 的配置文件中配置拦截器即可。
### 注意
自定义类型转化器的时候一定要注意对应的属性值 跟 方法中的参数的值要对应起来。
