# 7、RESTful

## 7.1、RESTful简介

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移。

### ①资源

资源是一种看待服务器的方式，即，将服务器看作是由很多离散的资源组成。每个资源是服务器上一个可命名的抽象概念。因为资源是一个抽象的概念，所以它不仅仅能代表服务器文件系统中的一个文件、数据库中的一张表等等具体的东西，可以将资源设计的要多抽象有多抽象，只要想象力允许而且客户端应用开发者能够理解。与面向对象设计类似，资源是以名词为核心来组织的，首先关注的是名词。一个资源可以由一个或多个URI来标识。URI既是资源的名称，也是资源在Web上的地址。对某个资源感兴趣的客户端应用，可以通过资源的URI与其进行交互。

> 服务器端看待资源的方式
>
> 万物皆资源

### ②资源的表述

资源的表述是一段**对于资源在某个特定时刻的状态的描述**。可以在客户端-服务器端之间转移（交换）。资源的<u>表述</u>可以有多种格式，例如HTML/XML/**JSON**/纯文本/图片/视频/音频等等。资源的<u>表述格式</u>可以通过协商机制来<u>确定</u>。请求-响应方向的表述通常使用不同的格式。

### ③状态转移

状态转移说的是：在客户端和服务器端之间转移（transfer）代表资源状态的表述。通过转移和操作资源的表述，来间接实现操作资源的目的。

## 7.2、RESTful的实现

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：GET 用来获取资源，POST 用来新建资源，PUT 用来更新资源，DELETE用来删除资源。

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| **操作** | **传统方式**     | REST风格                                                     |
| -------- | ---------------- | ------------------------------------------------------------ |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式，user-->get请求方式(查询所有的用户信息) |
| 保存操作 | saveUser         | user-->post请求方式(数据比较多的时候通过表单提交的方式提交,而不是写在地址中) |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式                                      |
| 更新操作 | updateUser       | user-->put请求方式                                           |

## 创建工程测试GET和POST

Html页面测试代码

```html
<body>
  <h1>index.html</h1>
    <h2>RESTful风格的资源请求方式</h2>
        <h3>查询--GET</h3>
        <a th:href="@{/user}">查询所有的用户信息</a>
        <a th:href="@{/user/1}">查询所有的用户信息</a>
    <hr/>
        <h3>添加--POST</h3>
        <form th:action="@{/user}" method="post">
            用户名:<input type="text" name="username"><br/>
            密码:<input type="password" name="password"><br/>
            <input type="submit" value="添加用户信息">
        </form>
</body>
```

TestRestController

```java
@Controller
public class controllerTestRestController {
    //<a th:href="@{/user}">查询所有的用户信息</a>
//可以替换为@GetMapping
    @RequestMapping(value = "/user",method = RequestMethod.GET)
    public String testGetAllUser(){
        System.out.println("查询所有的用户信息");
        //查询所有的用户信息
        return "success";
    }

    @RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    public String testGetByID(@PathVariable("id")String id){
        System.out.println("查询用户id为:"+id+"的信息");
        //查询用户id为:1的信息
        return "success";
    }
//可以替换为@PostMapping
    //<form th:action="@{/user}" method="post">
    @RequestMapping(value = "/user",method = RequestMethod.POST)
    public String testAddByPost(User user){
        System.out.println("添加用户的信息为:"+user);
        //添加用户的信息为:User{id=null, username='Aoidayo', password='201741'}
        return "success";
    }

}
```

## 7.3、HiddenHttpMethodFilter

由于浏览器只支持发送get和post方式的请求，那么该如何发送put和delete请求呢？

SpringMVC 提供了 **HiddenHttpMethodFilter** 帮助我们**将 POST 请求转换为 DELETE 或 PUT 请求**

**HiddenHttpMethodFilter** 处理put和delete请求的条件：

a>当前请求的**请求方式必须为post**

b>当前请求必须传输**请求参数_method**,其value为我们需要转换的请求类型(delete/put)

满足以上条件，**HiddenHttpMethodFilter** 过滤器就会将当前请求的请求方式转换为请求参数`_method`的值，因此请求参数`_method`的值才是最终的请求方式

在web.xml中注册**HiddenHttpMethodFilter**

> 需要注意的是:_method的值是需要程序员在后台设置的,而不是由用户手动填写,所以我们可以将表单的类型设置为一个隐藏域
>
> ```html
> <input type="hidden" name="_method" value="put"><br/>
> ```
>
> 

```xml
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filterclass>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 注：
>
> 目前为止，SpringMVC中提供了两个过滤器：CharacterEncodingFilter和HiddenHttpMethodFilter
>
> 在web.xml中注册时，必须先注册CharacterEncodingFilter，再注册HiddenHttpMethodFilter
>
> 原因：
>
> - 在 CharacterEncodingFilter 中通过 request.setCharacterEncoding(encoding) 方法设置字符集的
>
> - request.setCharacterEncoding(encoding) 方法要求前面不能有任何获取请求参数的操作
>
> - 而 HiddenHttpMethodFilter 恰恰有一个获取请求方式的操作：
>
> - ```java
>   String paramValue = request.getParameter(this.methodParam);
>   ```

测试:

```html
    <hr/>
        <h3>修改--PUT</h3>
        <!--method中设置为put,报文发送的时候会默认地转换为GET-->
        <form th:action="@{/user}" method="post">
            <input type="hidden" name="_method" value="put">
          用户名:<input type="text" name="username"><br/>
          密码:<input type="password" name="password"><br/>
          <input type="submit" value="修改用户信息">
        </form>
    <hr/>
        <h3>删除--Delete</h3>
        <!--method中设置为put,报文发送的时候会默认地转换为GET-->
        <form th:action="@{/user/1}" method="post">
          <input type="hidden" name="_method" value="delete">
          <input type="submit" value="删除用户">
        </form>
```

```java
    @RequestMapping(value = "/user",method = RequestMethod.PUT)
//可以替换为@PutMapping
    public String testFixByPut(User user){
        System.out.println("修改用户的信息为:"+user);
        //修改用户的信息为:User{id=null, username='Aoidayo', password='201741'}
        return "success";
    }
//可以替换为@DeleteMapping
    @RequestMapping(value = "/user/{id}",method = RequestMethod.DELETE)
    public String testDeleteByDelete(@PathVariable("id")Integer id){
        System.out.println("删除用户的id为:"+id);
        //删除用户的id为:1
        return "success";
    }
```

> 过滤器是如何将post请求转换为put和delete请求的?
>
> 1.寻找过滤器方法
>
> - 参数列中有response,request,FilterChain的方法
>
> - ```java
>   @Override
>   protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
>       throws ServletException, IOException {
>   
>       HttpServletRequest requestToUse = request;
>   //源码中的转换代码
>       //requset的method是post时考虑是否需要转换
>       if ("POST".equals(request.getMethod()) && request.getAttribute(WebUtils.ERROR_EXCEPTION_ATTRIBUTE) == null) {//无异常时为空,true
>           //获取需要转换的请求类型的参数,所以这个过滤器需要放在参数过滤器之后
>           /*
>           public static final String DEFAULT_METHOD_PARAM = "_method";
>           private String methodParam = DEFAULT_METHOD_PARAM;
>           */
>           String paramValue = request.getParameter(this.methodParam);
>           //paramValue不为空
>           if (StringUtils.hasLength(paramValue)) {
>               //所以说_method的值可以忽略大小写
>               String method = paramValue.toUpperCase(Locale.ENGLISH);
>               /*
>   		private static final List<String> ALLOWED_METHODS =
>   			Collections.unmodifiableList(Arrays.asList(HttpMethod.PUT.name(),
>   					HttpMethod.DELETE.name(), HttpMethod.PATCH.name()));
>   					可以转换的请求类型:PUT,DELETE,PATCH;
>               */
>               if (ALLOWED_METHODS.contains(method)) {
>                   //将请求封装为目标类型的请求并赋值
>   /*
>   	private static class HttpMethodRequestWrapper extends HttpServletRequestWrapper {
>   
>   		private final String method;
>   
>   		public HttpMethodRequestWrapper(HttpServletRequest request, String method) {
>   			super(request);
>   			this.method = method;
>   		}
>   
>   		@Override
>   		public String getMethod() {
>   			return this.method;
>   		}
>   	}
>   */
>                   requestToUse = new HttpMethodRequestWrapper(request, method);
>               }
>           }
>       }
>   	//过滤器放行,向下传递
>       filterChain.doFilter(requestToUse, response);
>   }
>   ```
>     

