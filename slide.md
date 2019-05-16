title: 高级Web课堂演示
speaker: JasonZhang
plugins:
    - echarts

<slide class="bg-black-blue aligncenter" image="https://source.unsplash.com/C1HhAQrbykQ/ .dark">

# 高级Web课堂演示 {.text-landing.text-shadow}

By JasonZhang {.text-intro}

[:fa-github: Github](https://github.com/DarkYoung/ad-web-ppt){.button.ghost}


<slide :class="size-60 aligncenter">

# Spring Boot 的核心
---

### `注解`

:::note

springboot 的核心就是注解。springboot 通过各种组合注解，极大地简化了 spring 项目的搭建和开发

:::

<slide>

:::{.content-center}
## 核心注解
:::flexblock {.specs}

### @Configuration
---
### @Conditional
---
### @SpringBootApplication
:::
<slide :class="slide-top">
:::div {.content-left}
### 核心注解： @Configuration

---
:::shadowbox

用于定义配置类，可替换xml配置文件

---

被注解的类内部包含一个或多个被`@Bean`注解的方法，用于构建 Bean 定义

:::
:::
:::div {.content-left}
#### 要求
---

* `@Configuration`不可以是`final`类型 {.animated.fadeInUp}
* `@Configuration`不可以是匿名类 {.animated.fadeInUp.delay-400}
* 嵌套的`@Configuration`必须是静态类 {.animated.fadeInUp.delay-800}
:::
:::note

`@Bean`注解的方法将会被`AnnotationConfigApplicationContext`或`AnnotatonConfigWebApplicationContext`类扫描，并用于构建 Bean 定义，初始化 Spring 容器 

:::

<slide class="size-60 aligncenter">

### 核心注解： @Conditional

---
根据是否满足某一特定条件来决定是否创建某个特定的`Bean`

`例如：当某个 Bean 已经被创建（存在）时才会创建另外一个 Bean`

:::note

可以依据这一特定条件来控制 Bean 的创建行为
利用这一特性可以实现一些自动的配置

:::

<slide :class="size-100 aligncenter">

#### 组合条件注解

---
:::column
```java {.animated.fadeInUp}
//当上下文中有指定 Bean 的条件下进行实例化
@ConditionalOnBean             
//当上下文中没有指定 Bean 的条件下进行实例化
@ConditionalOnMissingBean       
//当 classpath 类路径下有指定类的条件下进行实例化
@ConditionalOnClass             
//当类路径下没有指定类的条件下进行实例化
@ConditionalOnMissingClass      
//当项目本身是一个 Web 项目是进行实例化
@ConditionalOnWebApplication    
//当项目本身不是一个 Web 项目时进行实例化
@ConditionalOnNotWebApplication 
```
----

```java {.animated.fadeInUp.delay-400}
//当指定的属性有指定的值时进行实例化
@ConditionalOnProperty          
//基于 SpEL 表达式的条件判断
@ConditionalOnExpression        
//当 JVM 版本为指定的版本范围时进行实例化
@ConditionalOnJava              
//当类路径下有指定资源时进行实例化
@ConditionalOnResource          
//在 JNDI 存在时触发实例化
@ConditionalOnJndi              
//当指定的 Bean 在容器中只有一个
//或者有多个但是指定了首选的 Bean 时触发实例化 
@ConditionalOnSingleCandidate   
```
:::


:::note

`@Conditional` 是 springboot 实现自动配置的关键能力
在此基础上，springboot 又创建了多个适用于不同场景的组合条件注解

:::

<slide :class="size-60 aligncenter">

### 核心注解： @SpringBootApplication

---


:::note

这是`Springboot`最核心的注解，也是个组合注解
除此之外，最核心的是`@EnableAutoConfiguration`注解，开启自动配置

:::

<slide :class="size-100">
### @SpringBootApplication
---
让 springboot 自动给程序进行必要的配置
* @Configuration：等同于 spring 的 XML 配置文件
* @EnableAutoConfiguration：启用自动配置
* @ComponentScan：组件扫描，可以自动发现和装配一些 Bean

```java {.animated.fadeInUp.delay-400}
@SpringBootApplication
@MapperScan("com.estate.estate.dao")
public class BackgroundApplication {
    public static void main(String[] args) {
        SpringApplication.run(BackgroundApplication.class, args);
    }
}
```
:::note

配置文件使用注解的方式，可以通过 Java 进行类型安全检查

* 通过注解`@EnalbleAutoConfiguration`的方式去查找、过滤和加载所需的`configuration`
* `@ComponentScan`扫描我们自定义的`Bean`
* `@SpringBootConfiguration`使得被`@SpringBootApplication`注解的类声明为注解类

:::

<slide class="bg-black-blue aligncenter">

## 四大注解

---

:::column
#### @Service

---
#### @Repository

---
#### @Component

---
#### @Controller
定义控制器类

通常配合 @RequestMapping 注解方法
:::

:::note

@Controller 用于定义控制器类，在 springboot 项目中有控制器将用户发来的 URL 请求转发到对应的服务接口（Service 层），一般在这个注解的类中，通常配合 @RequestMapping 注解方法

@RestController 用于标注控制层组件，相当于 @ResponseBody 和 @Controller 的组合
:::

<slide :class="size-120 aligncenter">

```java {.animated.fadeInLeft}
@Service
public class UserServiceImpl implements UserService {
    @Autowired
    private UserMapper userMapper;
    public User selectByPrimaryKey(String userPhone) {
        // 数据库事务操作
        return userMapper.selectByPrimaryKey(userPhone);
    }
}
```

```java {.animated.fadeInRight}
@Controller
@RequestMapping("/")  // 一级路由
public class LoginController {
    @RequestMapping(value={"login", "index", ""}) // 二级路由（表示为：一级路由 + 二级路由）
    public String index(Model model,  @RequestParam(value = "error", required = false) String error) {
        if (error != null) {
            model.addAttribute("error" , "用户名或者密码错误！");
        }
        //会使用 login/index.html 或 login/index.ftl 模板进行渲染显示
        return "login/index";   
    }
}
```

<slide :class="size-60 aligncenter">
:::{.content-center}
## RESTful
:::flexblock {.specs}
### @RequestMapping
---
### @ResponseBody
:::

<slide :class="size-100 aligncenter">
### @RequestMapping
提供路由信息，负责 URL 到 Controller 中具体函数的映射
```java {.animated.fadeInUp.delay-400}
    // 路径为 /、/index、/index.html的 URL 都使用此方法处理
    @RequestMapping({"/", "/index", "index.html", ""})
    public String index() {
        return "ad/index"; // 使用 ad/index.html 或 ad/index.ftl 模板进行渲染显示
    }
    // 路径为 /detail/{adId} 的 URL 请求都使用此方法处理（{adId}表示变量）
    @RequestMapping("/detail/{adId}")
    public String detail(
        @PathVariable("adId") String adId, 
        Model model) {
        \\……
        return "ad/detail"; // 使用 ad/detail.html 或 ad/detail.ftl 模板进行渲染显示
    }
```

<slide :class="size-100 aligncenter">

### @ResponseBody
将返回的结果直接写入 HTTP Response Body 中
```java {.animated.fadeInUp.delay-400}
@RestController // 相当于 @Controller 和 @ResponseBody 的组合
@RequestMapping("/api/ad")
public class AdController {
    @Autowired  // 自动装配
    private AdService adService;
    // 此方法接受一个 GET 请求，路径为 /detail/{id}， {id} 表示变量
    // produces 指定 Content-Type 为 json 数据，编码格式为 UTF-8
    @GetMapping(value = "/detail/{id}", produces = "application/json;charset=UTF-8")
    public String getDetailByAdId(@PathVariable("id") String id) {
        JSONObject jsonObject = new DuoDuoJson();
        AdInfoItem adInfoItem = null;
        //……
        Ad ad = adService.selectByPrimaryKey(parseInt(id));
        //……
        // 使用 @ResponseBody 注解后，返回 json 数据，而不是解析成跳转路径
        return jsonObject.toString();   
    }
```
:::note

@ResponseBody 一般在异步获取数据时调用，用于构建 RESTful 的 api
在使用 @RequestMapping 后，返回值通常解析为跳转路径（直接或间接转换，最后都是转换成 ModelAndView）
加上 @ResponseBody 后返回结果不会被解析成跳转路径，而是直接写入 HTTP Response Body 中
比如异步获取 json 数据，使用该注解将返回 json 数据

:::


<slide :class="size-60 aligncenter">

# Thymeleaf

---

模板引擎（Spring Boot 官方推荐）

<slide class="aligncenter">
:::card
### 使用
---
* 引入依赖包
* 指定命名空间

<slide class="aligncenter">

```html {.animated.fadeInUp}
<table class="table table-bordered table-striped">
<caption th:text="'用户数目: ' + ${userList.size()} + ''"></caption>
<thead class="thead-dark"><tr>
    <th scope="col" th:text="'#'+${userList.size()}"></th>
    <!--switch语句，根据变量设置对应的中文字段名-->
    <th scope="col" th:each="field: ${fields}" th:switch="${field.getName()}">
        <label th:case="userPhone">电话</label>
        <label th:case="name">姓名</label>
        <label th:case="gender">性别</label>
        <label th:case="signDate">注册日期</label>
    </th>
</tr></thead>
<tbody>
    <tr th:each="user, status: ${userList}">
        <th scope="row" th:text="|${status.count}|"></th>
        <td class="list" th:each="field: ${fields}">
            <span th:if="${field.getName()=='signDate'}"th:text="${#dates.format(user[field.getName()],'yyyy-MM-dd')}"></span>
            <span th:if="${field.getName()!='signDate'}" th:text="${user[field.getName()]}"></span>
        </td>
    </tr>
</tbody>
</table>
```