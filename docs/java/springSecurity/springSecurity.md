### 1、自定义入参

```java
http.formLogin()
	// 自定义入口参数
	.usernameParameter("username123")
	.passwordParameter("password123")
```

### 2、自定义登录成功处理器

> 写一个类去继承 `AuthenticationSuccessHandler `接口

````java
public class MyAuthenticationSuccessHandler implements AuthenticationSuccessHandler {
    private String url;

    public MyAuthenticationSuccessHandler(String url) {
        this.url = url;
    }

    @Override
    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
        User user = (User) authentication.getPrincipal();
        System.out.println(user.getUsername());
        // 出于安全考虑，不会返回输出null
        System.out.println(user.getPassword());
        System.out.println(user.getAuthorities());
        response.sendRedirect(url);
    }
}
````

> security配置类

````java
 http.formLogin()
	.successHandler(new MyAuthenticationSuccessHandler("http://www.baidu.com"))
````

### 3、自定义登录失败处理器

> 写一个类去继承 `AuthenticationFailureHandler `接口

````java
public class MyAuthenticationFailureHandler implements AuthenticationFailureHandler {
    private String url;

    public MyAuthenticationFailureHandler(String url) {
        this.url = url;
    }


    @Override
    public void onAuthenticationFailure(HttpServletRequest request, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
        response.sendRedirect(url);
    }
}
````

> security配置类

````java
 http.formLogin()
     .failureHandler(new MyAuthenticationFailureHandler("/error.html"));
````

### 4、anyRequest

> 所有请求都必须认证才能访问，必须登录
>
> ```java
> .anyRequest().authenticated();
> ```

### 5、antMatchers

> 放行，不需要认证
>
> ```java
> .antMatchers("/login.html").permitAll()
> ```

### 6、regexMatchers

> 放行后缀.png，正则表达式
>
> ```java
> .regexMatchers(".+[.]png").permitAll()
> ```

### 7、mvcMatchers

> mvc 匹配
>
> ```java
> .mvcMatchers("/demo").servletPath("/xxx").permitAll()
> ```

### 8、内置控制访问方法

> ```java
> permitAll = "permitAll";
> denyAll = "denyAll";
> anonymous = "anonymous";
> authenticated = "authenticated";
> fullyAuthenticated = "fullyAuthenticated";
> rememberMe = "rememberMe";
> ```

### 9、权限判断

除了之前讲解的内置权限控制。Spring Security中还支持很多其他权限控制。这些方法一般都用于用户已经被认证后，判断用户是否具有特定的要求。

#### 9.1、基于权限

> 权限控制，严格区分大小写
>
> ```java
> .antMatchers("/main1.html").hasAuthority("admin")
> ```

#### 9.2、基于角色

> ```java
> .antMatchers("main.html").hasRole("admin")
> ```

#### 9.3、基于ip

> ```java
> .antMatchers("main1.html").hasIpAddress("127.0.0.0")
> ```

### 10、自定义403处理方案

> 写一个类去继承 `AccessDeniedHandler`接口

````java
@Component
public class MyAccessDeniedHandler implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException accessDeniedException) throws IOException, ServletException {
        // 设置响应状态
        response.setStatus(HttpServletResponse.SC_FORBIDDEN);
        response.setHeader("Content-Type", "application/json;charset=utf-8");
        PrintWriter writer = response.getWriter();
        writer.write("{\"status\":\" error\",\"msg\": \"权限不足，请联系管理员\"}");
        writer.flush();
        writer.close();
    }
}
````

> security配置类

````java
// 异常处理
http.exceptionHandling()
	.accessDeniedHandler(myAccessDeniedHandler);
````

### 11、自定义access

````java
@Service
public interface MyService {
    boolean hasPermission(HttpServletRequest request, Authentication authentication);
}
````

````java
@Service
public class MyServiceImpl implements MyService{
    @Override
    public boolean hasPermission(HttpServletRequest request, Authentication authentication) {
        // 获取主体
        Object principal = authentication.getPrincipal();
        //判断主体是否属于UserDetails
        if(principal instanceof UserDetails){
            // 获取权限
            UserDetails userDetails = (UserDetails) principal;
            Collection<? extends GrantedAuthority> authorities = userDetails.getAuthorities();
            //判断请求的URI是否在权限里
            return authorities.contains(new SimpleGrantedAuthority(request.getRequestURI()));
        }
        return false;
    }
}
````

````java
return new User(username, password, AuthorityUtils.commaSeparatedStringToAuthorityList("admin, ROLE_admin, /main.html"));
````

````java
.anyRequest().access("@myServiceImpl.hasPermission(request, authentication)");
````

### 12、access表达式写法

![image-20220207084924434](C:\Users\86157\AppData\Roaming\Typora\typora-user-images\image-20220207084924434.png)

### 13、基于注解的访问控制

>需要在启动类上标注`@EnableGlobalMethodSecurity`通过该注解的属性，开启基于注解的访问控制
>
>
>
>这些注解可以写到Service 接口或方法上，也可以写到Controller或Controller的方法上。通常情况下都是写在控制器方法上的，控制接口URL是否允许被访i问。

#### 12.1、@Secured

> @Secured 是专门用于判断是否具有角色的。能写在方法或类上。参数要以 ROLE_开头。

#### 12.2、@PreAuthorize

> 表示`访问方法或类在执行之前`先判断权限，大多情况下都是`使用这个注解`，注解的`参数和access()方法参数`取值相同，都是权限表达式。

#### 12.3、@PostAuthorize

> 表示方法或类执行结束后判断权限，此注解很少被使用到。

### 14、Remember Me功能实现

> Spring Security 中Remember Me为“记住我"功能，用户只需要在登录时`添加remember-me复选框，取值为`
>
> `true`。Spring Security会自动把用户信息存储到数据源中，以后就可以不登录进行访问

### 15、Thymeleaf中springsecurity的使用

> Spring Security可以在一些视图技术中进行控制显示效果。例如:`JSP`或 `Thymeleaf`。在非前后端分离且使
>
> Spring Boot的项目中多使用` Thymeleaf `作为视图展示技术。
>
> Thymeleaf对 Spring Security的支持都放在`thymeleaf-extras-springsecurityX`中，目前最新版本为5。所
>
> 以需要在项目中添加此jar包的依赖和thymeleaf 的依赖。。

### 16、jwt

#### 16.1、荷载

>iss : jwt签发者
>
>sub: jwt所面向的用户aud:接牧jwt的一方
>
>exp: jwt的过期时间,这个过其期脚寸间必须要大于签发时间nbf:定义在什么时间之前，该j帅t都是不可用的.
>
>iat: jwt的签发时间
>
>jti: jwt的唯一身份标识，主要用来作为一次性token,从而回避重放攻击。
