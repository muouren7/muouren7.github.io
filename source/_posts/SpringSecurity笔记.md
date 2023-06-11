---
  
title: SpringSecurity笔记（未完成）
tags:
- SpringSecurity
---

# 准备

## 导入SpringSecurity包

由于springsecurity会继承其父工程**springboot**，所以在添加依赖的时候不需要添加版本，有springboot容器自动指定。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

# 认证

## 登录认证流程

![认证流程](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211061831436.png)

## spring security认证与校验流程

![认证流程](https://img-blog.csdnimg.cn/20200824163620756.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d1MjM3NDYzMzU4Mw==,size_16,color_FFFFFF,t_70#pic_center)

![校验流程](https://cdn.jsdelivr.net/gh/muouren7/PIC/img202211102033013.png)



## 自定义实例

### 添加依赖

```xml
<!--redis依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--fastjson依赖-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.33</version>
</dependency>
<!--jwt依赖-->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.0</version>
</dependency>
```

自定义示例

继承UserDetailsService接口，重写loadUserByUsername方法

流程：只会传入username，需要自己实现通过username对整个用户信息的查询，并将其封装为UserDetails对象，SpringSecurity会根据查询后封装的UserDetails对象中的密码进行判断是否登录成功

```java
@Service
public class UserDetailsServiceImp implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
        if(!s.equals("muouren7")){
            throw new RuntimeException("用户名或密码错误");
        }
        return new LoginUser(s,"{noop}123456");	//{加密方法}加密后的密码
    }
}
```

LoginUser.java

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class LoginUser implements UserDetails {

    private String username;
    private String password;

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return null;
    }

    @Override
    public String getPassword() {
        return this.password;
    }

    @Override
    public String getUsername() {
        return this.username;
    }

    @Override
    public boolean isAccountNonExpired() {
        return true;
    }

    @Override
    public boolean isAccountNonLocked() {
        return true;
    }

    @Override
    public boolean isCredentialsNonExpired() {
        return true;
    }

    @Override
    public boolean isEnabled() {
        return true;
    }
}
```

### 加密存储

​	SpringSecurity默认在UserDetails中返回的对象里的密码必须按照 **{加密方式}加密后的密码** 的格式进行封装。例如，{noop}12345就表示加密方式为明文，且按照明文的方式加密后的密码是12345，如果不用明文，则需要在往数据库中存入密码前就加密

SpringSecurity自带加密的工具类

需要将其注入Spring容器（注入第三方对象使用@Bean）

```java
@Configuration
public class SecurotyConfig extends WebSecurityConfigurerAdapter {
    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }
}
```

```java
@SpringBootTest()
public class TestDemo {

    @Resource
    private BCryptPasswordEncoder passwordEncoder;

    @Test
    public void test(){
        System.out.println(passwordEncoder.encode("123456"));
    }
}
//$2a$10$VYtMkvhEhrOt6FrisGd45uJJDZ.sUbByrDfSBsLkLB/OEd3g0LZJ6		另外每次加密结果都不同
```

无需修改验证部分的代码

# 自定义登录接口

## Controller

```java
@Controller
public class Index {

    @Autowired
    private LoginService loginService;

    @RequestMapping("/login")
    @ResponseBody
    public RestRult login(@RequestBody UserInfo userInfo){
        RestRult<UserInfo> restRult = loginService.login(userInfo);
        return restRult;
    }
}
```

## Config

```java
@Configuration
public class config extends WebSecurityConfigurerAdapter {

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //super.configure(http);
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                //对于登录接口允许匿名访问
                .antMatchers("/login").anonymous()
                //除上面的请求，其余全部需要鉴权认证
                .anyRequest().authenticated();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }
}
```

## Service

实现**UserDetailsService**接口

```java
@Service
public class UserDetailsServiceImp implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserInfo userInfo = new UserInfo("muouren7","$2a$10$VYtMkvhEhrOt6FrisGd45uJJDZ.sUbByrDfSBsLkLB/OEd3g0LZJ6");

        //这里通过查询数据库查询用户账户密码
        if(!username.equals("muouren7")){
            throw new RuntimeException("登录错误");
        }

        return new LoginUser(userInfo.getUsername(),userInfo.getPassword());
    }
}
```

```java
public interface LoginService {
    RestRult login(UserInfo userInfo);
}
```

自定义登录方法（需要），在Controller中调用。

```java
@Service
public class LoginServiceImp implements LoginService{

    @Autowired
    private AuthenticationManager authenticationManager;

    @Override
    public RestRult login(UserInfo userInfo) {
        
        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(userInfo.getUsername(),userInfo.getPassword());

        Authentication authenticate = authenticationManager.authenticate(authenticationToken);

        //如果未通过
        if(Objects.isNull(authenticate)){
            throw new RuntimeException("登录失败");
        }

        return new RestRult(200,"登录成功",userInfo);
    }
}
```

# 自定义校验过滤

## 添加过滤器

```java
/*
* 登录之后每次的请求都会携带Token，
* 添加过滤器对请求的TOken进行验证
*
* 这里使用的Sprin提供的过滤器接口，
* 默认的过滤器接口可能出现一次请求会调用多次
* */
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        //获取Token
        String token = request.getHeader("token");
        if(!StringUtils.hasText(token)){
            filterChain.doFilter(request,response);
            return;
        }
        //解析Token  如果解析失败，抛出异常 throw new RuntimeException("token无效");
        //解析成功，从Redis中获取用户信息 LoginUser

        if(token.equals("111")){
            throw new RuntimeException("token无效");
        }

        LoginUser loginUser = new LoginUser("muouren7","$2a$10$VYtMkvhEhrOt6FrisGd45uJJDZ.sUbByrDfSBsLkLB/OEd3g0LZJ6");

        //存放SecurityContextHolder
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null, null);
        SecurityContextHolder.getContext().setAuthentication(usernamePasswordAuthenticationToken);

        //放行
        filterChain.doFilter(request,response);
    }
}
```

## 配置过滤器认证

将自定义的token校验添加到过滤器链中

```java
@Configuration
public class config extends WebSecurityConfigurerAdapter {

    @Autowired
    private JwtFilter jwtFilter;

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //super.configure(http);
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                //对于登录接口允许匿名访问
                .antMatchers("/login").anonymous()
                //除上面的请求，其余全部需要鉴权认证
                .anyRequest().authenticated();
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);//将token过滤器加入到过滤器链中
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }


}
```

# 退出登录

​	 如果按照上述的方式将Token存入Redis并且在每次请求前验证Token，将权限信息存入SecurityContextHoder的方法。那么退出登录需要将Redis中的Token清除且新建一个Token未失效的黑名单，另外退出登录的接口不能是**/logout**，因为/logout已经被SpringSecurity占用，会被拦截。

```java
@Service
public class LoginServiceImp implements LoginService{

    @Autowired
    private AuthenticationManager authenticationManager;

    @Override
    public RestRult logout() {
        //获取SecurityContextHoder中的用户信息，每一次有效请求都会往SecurityContextHoder存入信息
        //注销Token
        System.out.println("aaaa");
        return new RestRult(200,"退出成功",new LoginUser("muouren7","123456"));
    }


}
```

```java
@Controller
public class Index {

    @Autowired
    private LoginService loginService;

    @RequestMapping("/user/logout")
    @ResponseBody
    public RestRult logout(){
        return loginService.logout();
    }
}
```

# 授权

​	开启授权注解配置，在配置类中开启

```java
@EnableGlobalMethodSecurity(prePostEnabled = true)
```

```java
@Controller
public class Index {

    @Autowired
    private LoginService loginService;

    @RequestMapping("/test")
    @PreAuthorize("hasAuthority('test')")	//写入所需要的权限
    @ResponseBody
    public RestRult test(){
        return loginService.logout();
    }


}
```

登录时放入权限（也可以不放，登录不需要权限验证就可以不放）

```java
@Service
public class UserDetailsServiceImp implements UserDetailsService {
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserInfo userInfo = new UserInfo("muouren7","$2a$10$VYtMkvhEhrOt6FrisGd45uJJDZ.sUbByrDfSBsLkLB/OEd3g0LZJ6");

        //这里通过查询数据库查询用户账户密码
        if(!username.equals("muouren7")){
            throw new RuntimeException("登录错误");
        }


        Collection<SimpleGrantedAuthority> collection = new ArrayList<>();
        collection.add(new SimpleGrantedAuthority("test"));			//名为test的权限
        return new LoginUser(userInfo.getUsername(),userInfo.getPassword(),collection);
    }


}
```

过滤器里验证时需要存入权限

```java
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        //获取Token
        String token = request.getHeader("token");
        if(!StringUtils.hasText(token)){
            filterChain.doFilter(request,response);
            return;
        }
        System.out.println(token);
        //解析Token  如果解析失败，抛出异常 throw new RuntimeException("token无效");
        //解析成功，从Redis中获取用户信息 LoginUser

        if(token.equals("111")){
            throw new RuntimeException("token无效");
        }

        Collection<SimpleGrantedAuthority> collection = new ArrayList<>();
        collection.add(new SimpleGrantedAuthority("test"));
        LoginUser loginUser = new LoginUser("muouren7","$2a$10$VYtMkvhEhrOt6FrisGd45uJJDZ.sUbByrDfSBsLkLB/OEd3g0LZJ6",collection);	//放入权限，这里是把权限写死了的

        //存放SecurityContextHolder
        UsernamePasswordAuthenticationToken usernamePasswordAuthenticationToken = new UsernamePasswordAuthenticationToken(loginUser, null, collection   );
        SecurityContextHolder.getContext().setAuthentication(usernamePasswordAuthenticationToken);

        //放行
        filterChain.doFilter(request,response);
    }
}
```

过滤器中存入的权限，在访问接口时会与接口**@PreAuthorize**注解所规定的权限进行比较

# 自定义失败处理

​	在SpringSecurity中，如果我们在认证或者授权的过程中出现了异常会被ExceptionTranslationFilter捕获到。在ExceptionTranslationFilter中会去判断是认证失败还是授权失败出现的异常。

​	如果是认证过程中出现的异常会被封装成AuthenticationExieption然后调用**AuthenticationEntryPoint**对象的方法去进行异常处理。	

​	如果是授权过程中出现的异常会被封装成AccessDeniedException然后调用AccessDeniedHandler对象的方法去进行异常处理。

所以如果我们需要自定义异常处理，我们只需要自定义AuthenticationEntryPoint和AcessDeniedHandler然后配置给SpringSecurity即可。

## 实现类

```java
@Component
public class AuthenticationEntryPointImp implements AuthenticationEntryPoint {
    @Override
    public void commence(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AuthenticationException e) throws IOException, ServletException {
          //处理异常
        try{
            httpServletResponse.setStatus(200);
            httpServletResponse.setContentType("application/json");
            httpServletResponse.setCharacterEncoding("utf-8");
            RestRult<String> restRult = new RestRult<>(HttpStatus.UNAUTHORIZED.value(), "认证失败","");
            httpServletResponse.getWriter().write(restRult.toString());
        }catch (Exception x){
            x.printStackTrace();
        }

    }
}
```

```java
@Component
public class AccessDeniedHandlerImp implements AccessDeniedHandler {
    @Override
    public void handle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, AccessDeniedException e) throws IOException, ServletException {
        //处理异常
        try{
            httpServletResponse.setStatus(200);
            httpServletResponse.setContentType("application/json");
            httpServletResponse.setCharacterEncoding("utf-8");
            RestRult<String> restRult = new RestRult<>(HttpStatus.UNAUTHORIZED.value(), "授权失败","");
            httpServletResponse.getWriter().write(restRult.toString());
        }catch (Exception x){
            x.printStackTrace();
        }
    }
}
```

## 配置，添加异常处理器

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class config extends WebSecurityConfigurerAdapter {

    @Autowired
    private JwtFilter jwtFilter;
    @Autowired
    private AuthenticationEntryPointImp authenticationEntryPointImp;
    @Autowired
    private AccessDeniedHandlerImp accessDeniedHandlerImp;

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //super.configure(http);
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                //对于登录接口允许匿名访问
                .antMatchers("/login").anonymous()
                //除上面的请求，其余全部需要鉴权认证
                .anyRequest().authenticated();
        //添加过滤器
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
        //添加异常处理器
        http
                .exceptionHandling()
                .authenticationEntryPoint(authenticationEntryPointImp)
                .accessDeniedHandler(accessDeniedHandlerImp);
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }


}
```

# 跨域

​	除了配置SpringBoot本身的跨域，还需要配置SpringSecurity的跨域

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
public class config extends WebSecurityConfigurerAdapter {

    @Autowired
    private JwtFilter jwtFilter;
    @Autowired
    private AuthenticationEntryPointImp authenticationEntryPointImp;
    @Autowired
    private AccessDeniedHandlerImp accessDeniedHandlerImp;

    @Bean
    public PasswordEncoder passwordEncoder(){
        return new BCryptPasswordEncoder();
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        //super.configure(http);
        http
                //关闭csrf
                .csrf().disable()
                //不通过Session获取SecurityContext
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                .and()
                .authorizeRequests()
                //对于登录接口允许匿名访问
                .antMatchers("/login").anonymous()
                //除上面的请求，其余全部需要鉴权认证
                .anyRequest().authenticated();
        //添加过滤器
        http.addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class);
        //添加异常处理器
        http
                .exceptionHandling()
                .authenticationEntryPoint(authenticationEntryPointImp)
                .accessDeniedHandler(accessDeniedHandlerImp);
        //允许跨域
        http
                .cors();
    }

    @Bean
    @Override
    public AuthenticationManager authenticationManagerBean() throws Exception {
        return super.authenticationManagerBean();
    }


}

```

















