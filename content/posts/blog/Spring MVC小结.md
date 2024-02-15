---
title: "Spring MVC小结"
date: 2022-02-02T02:45:59+08:00
author: ["小任同学"]
draft: false # 是否为草稿
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
lastmod: 2022-05-13T21:55:37+08:00	#更新文章的时候手动改一下时间就可以
description: "正文开始。"
tags: 
- Spring MVC
- 框架
---
  
  
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
**@PathVariable** 和 **@RequestMapping** 和 **@RequestParam**：  
@PathVariable和@RequestParam不要混淆，  
区别是用法上不同：  
@PathVariable在 @RequestMapping请求路径后接”/{参数名}“，多个参数就再接多个“/{路径中的参数名}”，在对应的参数列表的参数前加上 @PathVariable 注解。该请求路径中参数名和参数列表 @PathVariable 注解的 value，再在网页路径上传入参数。  
而@RequestParam取参读取 /路径？后的name=”参数“，@RequestParam注解有三个参数，

1、value：参数名。

2、required：表示当前属性值是否必须存在（默认是true）。

3、defaultValue：如果value传递参数了，使用参数，如果没有，使用默认值。

@RequestHeader、@CookieValue也是一样的这三个参数。
 
**@RequestBody**：
注解实现接收http请求的json数据，将json转换为java对象。  
注：@ResponseBody ＋ @Controller == @RestController  
例如：前台Ajax传递到controller的json格式数据绑定到后台方法的参数列表的某个参数上。  
```js
       $.ajax({
　　　　　　　　url:"/login",
　　　　　　　　type:"POST",
　　　　　　　　data:'{"userName":"admin","pwd","admin123"}',
　　　　　　　　content-type:"application/json charset=utf-8",
　　　　　　　　success:function(data){
　　　　　　　　　　alert("request success ! ");
　　　　　　　　}
　　　　});
``` 
 
 ```java
　　　　@requestMapping("/login")
　　　　public void login(@requestBody String userName,@requestBody String pwd){   
//也可换做是user对象来绑定，会根据属性名赋值，  
//但是必须属性名和json的key对应上，否则请求不过去。
　　　　　　System.out.println(userName+" ："+pwd);
　　　　}
```

**@ResponseBody**：
该注解实现将controller方法返回的java对象转化为json对象响应给客户。
换句话说：表示当前请求的内容直接作为响应体，用于接收。

注意：在使用 @RequestMapping后，返回值通常解析为跳转路径，但是加上 @ResponseBody 后返回结果不会被解析为跳转路径，而是直接写入 HTTP response body 中。 比如前台异步获取 json 数据，加上 @ResponseBody 后，会直接返回 json 数据。  
使用：
```java 
　　@RequestMapping("/login")
　　@ResponseBody
　　public User login(User user){
　　　　return user;
　　}
　　User字段：userName pwd;
　　那么在前台接收到的数据为：'{"userName":"xxx","pwd":"xxx"}'

　　效果等同于如下代码：
　　@RequestMapping("/login")
　　public void login(User user, HttpServletResponse response){

              //通过response对象输出指定格式的数据
　　　　response.getWriter.write(JSONObject.fromObject(user).toString());
　　}
```

**@ModelAttribute**：  
@ModelAttribute最主要的作用是将数据添加到模型对象中，用于视图页面展示时使用。说白了就是数据回显。  但远远不仅于此.....  
<br>
在方法**参数**上使用 @ModelAttribute 注解:  
可以描述为**数据绑定**，注解在方法参数上说明了该方法的该参数将由 model 中取得，如果model找不到，那么该参数会被优先实例化，然后被添加进model，再把请求中所有名称与之匹配的参数填充到该参数中。  
<br>
对**方法**使用 @ModelAttribute ：  
添加该注解的方法会率先其他方法执行。  
如果方法无返回值，就在添加该注解的方法中 model.addAttribute ，在该控制器的其他方法返回的页面可以拿到这个 model。  
有无返回值的 @ModelAttribute 方法区别是，无返回值的是 model.addAttribute(String key,Object value)来向model增加参数，而有返回值的直接将需要增加的参数返回。过程说出来就是赘述，联想一下。  
以上都是在 @ModelAttribute 中不加 value ，这样如果是有返回值的 @ModelAttribute 方法，就会默认返回名称为“返回值类型的小驼峰化的对象名称”，如果指定 value，那就是返回指定的，比如返回值类型是User，那默认名称则为user，指定名称为 pp，那返回参数名称就是pp。




### rest中的get、post、delete、put请求
get是获取资源、post新建资源、put更新资源、delete删除资源。  
在选择method只能发送post和get请求，因此可以用过滤器filter将post请求转化为put、delete请求以达到put、delete的效果。
### 编码问题详解
编码问题无外乎两种情况：
1. post： 在 web.xml 配置一个 CharacterEncodingFilter 过滤器，设置为 utf-8。
2. get：在tomcat的 server.xml 文件中，添加 URLEncoding=utf-8。或者对 request、response 参数重新编码。
3. 注意：过滤器顺序：一个应用程序中可能会包含N个过滤器，这N个过滤器一般是没有顺序要求的，但是如果设置了编码过滤器，那么一定要把它放到最上面，保证过滤器的运行。不论是Spring MVC自带的编码器还是自定义的，都要这样。
### 后端向前端传值的方式
前面都是前端往后端传数据，现在看后端往前端传数据


前面都是前端往后端传数据，现在看后端往前端传数据

1、map.put(”msg”,”hello data”)，在参数列表加Map。return 的是页面路径。

2、model.addAttribute(”mac”,”hello mac”)，在参数列表中加Model 对象名。return 的是页面路径。

```java
@RequestMapping("/testModel")
public String testModel(Model model){
    model.addAttribute("name","赵六");
    model.addAttribute("age",12);
    model.addAttribute("address","上海");
    return "user";
}
```

3、modelMap.addAttribute(”mac”,”hello mac”)，在参数列表中加ModelMap 对象名。return 的是页面路径。

4、也可以用ModelAndView传递数据，方法内new一个modelAndView对象并且“该对象”.setViewName(”页面路径”)，再addObject(”mac”,”hello mac”);return的是“该对象”，上述三种返回的是页面路径。
```java
@RequestMapping("testModelAndView")
public ModelAndView testModelAndView(){
	ModelAndView modelAndView = new ModelAndView();
	//设置跳转页面名称
	modelAndView.setViewName("user");
	//设置携带的参数
	modelAndView.addObject("name","赵六");
	modelAndView.addObject("age",12);
	modelAndView.addObject("address","上海");
	return modelAndView;
}
```
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
