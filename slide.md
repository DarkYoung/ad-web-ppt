title: 高级Web课堂演示-Part2
speaker: 张健
plugins:
    - echarts

<slide class="bg-black-blue aligncenter" image="https://source.unsplash.com/C1HhAQrbykQ/ .dark">

# Spring Boot 注解 {.text-shadow}
# Thymeleaf 模板引擎 {.text-shadow}

By 张健 {.text-intro}

[:fa-github: Github](https://github.com/DarkYoung/ad-web-ppt){.button.ghost.flipInX}


<slide class="bg-gradient-r aligncenter">

# Spring Boot 的核心
---

### 注解

:::note

springboot 的核心就是注解。springboot 通过各种组合注解，极大地简化了 spring 项目的搭建和开发

:::

<slide>

:::{.content-center}
## 核心注解
:::flexblock {.specs}

### @Configuration {.animated.fadeInUp.delay-400}
---
### @Conditional {.animated.fadeInUp}
---
### @SpringBootApplication 

:::
<slide :class="slide-top">
:::div {.content-left}
### 核心注解： @Configuration

---
:::shadowbox {.tobuild.fadeInUp}

用于定义配置类，可替换xml配置文件

---

被注解的类内部包含一个或多个被`@Bean`注解的方法，用于构建 Bean 定义

:::
:::
:::div {.content-left.tobuild.fadeInRight}
#### 要求 
---

* `@Configuration`不可以是`final`类型 {.tobuild.fadeInUp}
* `@Configuration`不可以是匿名类 {.tobuild.fadeInUp}
* 嵌套的`@Configuration`必须是静态类 {.tobuild.fadeInUp}
:::
:::note

`@Bean`注解的方法将会被`AnnotationConfigApplicationContext`或`AnnotatonConfigWebApplicationContext`类扫描，并用于构建 Bean 定义，初始化 Spring 容器 

:::

<slide class="size-60 aligncenter">

### 核心注解： @Conditional

---
根据是否满足某一特定条件来决定是否创建某个特定的**Bean** {.animated.pulse}

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

使用注解的方式配置文件，可以通过 Java 进行类型安全检查

* 通过注解`@EnalbleAutoConfiguration`的方式去查找、过滤和加载所需的`configuration`
* `@ComponentScan`扫描我们自定义的`Bean`
* `@SpringBootConfiguration`使得被`@SpringBootApplication`注解的类声明为注解类

:::

<slide class="aligncenter">

## Spring 四大注解

---

:::column
#### @Service
对应业务层的 Bean

---
#### @Repository
对应数据访问层的 Bean

---
#### @Component
定义受 Spring 管理的组件

---
#### @Controller
定义用户控制器类，对应表现层的 Bean

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
    // 映射路径为：/templates/login.html /templates
    @RequestMapping(value={"login", "index", ""}) // 映射为：前缀 + 一级路由 + 二级路由 + 后缀
    public String index(Model model,  @RequestParam(value = "error", required = false) String error) {
        if (error != null) {
            model.addAttribute("error" , "用户名或者密码错误！");
        }
        //会使用 login/index.html 或 login/index.ftl 模板进行渲染显示，这里使用的是 html 模板文件
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
        //……
        Ad ad = adService.selectByPrimaryKey(parseInt(id));
        //……
        // 使用 @ResponseBody 注解后，返回 json 数据，而不是解析成跳转路径
        return jsonObject.toString();   
    }
}
```
:::note

@ResponseBody 一般在异步获取数据时调用，用于构建 RESTful 的 api
在使用 @RequestMapping 后，返回值通常解析为跳转路径（直接或间接转换，最后都是转换成 ModelAndView）
加上 @ResponseBody 后返回结果不会被解析成跳转路径，而是直接写入 HTTP Response Body 中
比如异步获取 json 数据，使用该注解将返回 json 数据

:::


<slide class="bg-gradient-r aligncenter">

# Thymeleaf

---

模板引擎（Spring Boot 官方推荐）

:::note

模板引擎的作用都是将模板（页面）和数据进行整合然后输出显示

:::

<slide>
:::div{.text-cols}
**Why Thymeleaf?**  {.flipInX.animated}

**Thymeleaf** 是 Web 和独立环境的现代服务器端 Java 模板引擎，能够处理 HTML，XML，JavaScript，CSS 甚至是纯文本
:::

* 建立在自然模板的概念上，将其逻辑注入到模板文件中，不会影响模板被用作设计原型 {.tobuild.fadeInRight}
* 设计了 Web 标准（特别是 HTML5），可以创建**完全验证的模板** {.tobuild.fadeInRight}
* Spring Boot **官方推荐**使用 Thymeleaf 而不是 JSP {.tobuild.fadeInRight}
* Spring Boot 开发的 Web 项目采用打 Jar 包的方式，且使用内置的 Tomcat，因此**默认不支持 JSP** {.tobuild.fadeInRight}
* 使用**简单**，因为 Spring Boot 已经提供了默认的配置，比如解析的文件前缀、文件后缀、文件编码、缓存等等，只需要写 HTML {.tobuild.fadeInRight}

<slide :class="size-100">

### 使用步骤
---
:::div
* 在 pom.xml 中引入 thymeleaf
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```
* 配置 thymeleaf
```yaml
thymeleaf:
     cache: false                   # 开发过程中建议关闭缓存   
     prefix: classpath:/templates/  # 映射文件的前缀
     suffix: .html                  # 映射文件的后缀
     encoding: UTF-8                # 编码
     servlet:
      content-type: text/html
     mode: HTML5                    # 启用 HTML5

```
* 编写模板文件 */*.html
* 编写访问模板文件对应的 Controller
:::
<slide class="aligncenter">
### 基本表达式
---
:::flexblock {.animated.fadeInUp}
#### `${}`
变量表达式 

---
#### `*{}`
选择表达式

---
#### `#{}`
消息表达式

---
#### `@{}`
超链接表达式

:::

<slide class="slide-top">
:::{.content-left}
### 变量表达式：${}
变量表达式（美元表达式），用于访问上下文中的变量 {.text-data}
```java {.animated.fadeInUp}
// 初始页面，会返回所有的用户
@RequestMapping({"/", "/index", "/index.html", ""})
public String index(Model model) {
    // 传入需要显示的数据
    addModelBeforeReturnIndex(model);
    // 使用 partner/index.html 模板进行渲染显示
    return "partner/index";
}
private void addModelBeforeReturnIndex(Model model) {
    List<Partner> partnerList = partnerService.selectAll();
    // 传入对象
    model.addAttribute("partnerList", partnerList); 
    List<String> fields = getFields();
    model.addAttribute("fields", fields);
}
```
:::
:::{.content-right.tobuild.lightSpeedIn}
#### partner/index.html
```html
<!-- 使用变量表达式访问上下文中的变量（传入的对象） -->
<tr th:each="partner, status: ${partnerList}">
    <!-- …… -->
</tr>
```

:::

:::note
变量表达式（美元表达式），用于访问上下文中的变量，该变量可能来自于路由选择时设置的值

:::

<slide :class="size-60 aligncenter">
### 选择表达式：*{}
选择表达式（星号表达式），选择`th:object`对象属性绑定的对象中的属性 {.text-intro}

```html {.animated.fadeInUp}
<div th:object="${session.user}" ><!-- 指定对象 -->
    <!-- 访问指定对象的属性，这里 *{name} 相当于 ${session.user.name} -->
    <p>姓名: <span th:text="*{name}">张三</span></p>   
    <p>手机号: <span th: text="*{phoneNumber}">12345</span></p>
</div>
```

:::note
选择表达式（星号表达式）。

选择表达式与变量表达式有一个重要的区别：选择表达式计算的是选定的对象，而不是整个环境变量映射。
也就是：如果没有指定选择的对象，选择表达式与变量表达式的语法是完全一样的。

那什么是选择的对象呢？是一个`th:object`对象属性绑定的对象。
:::

<slide>
### 消息表达式：#{} 
消息表达式（资源表达式），通常与th:text属性一起使用 {.text-intro.animated.fadeInLeft}
```html {.animated.fadeInRight.delay-400}
    <p th:text="#{user.name}">张三</p>
```

- 消息表达式通常用于显示页面静态文本 {.animated.fadeInLeft.delay-800}
- 对应的静态文本通常维护在`properties`或`yml`文件中 {.animated.fadeInRight.delay-1200}

```yaml {.animated.fadeInLeft.delay-1600}
    # /WEB-INF/templates/user.yml
    user.name: 王五
```

:::note
与`th:text`属性一起使用，指明声明了`th:text`的标签的文本是`#{}`中的`key`所对应的`value`，而标签内的文本将不会显示。

将静态文本维护在`properties`或`yml`文件中有几个好处，比如：方便维护，做国际化等。

`yml`文件配置较`properties`文件友好，采用缩进式配置，避免了较长的引用名（spring.datasource.url等）
:::

<slide>
### 超链接表达式：@{}

```html {.animated.fadeInUp}
<link rel="stylesheet" type="text/css" th:href="@{/css/bootstrap.css}"/>
<link rel="stylesheet" type="text/css" th:href="@{/css/header.css}"/>
<link rel="stylesheet" type="text/css" th:href="@{/css/footer.css}"/>
<link rel="stylesheet" type="text/css" th:href="@{/css/main.css}"/>
```

<slide class="aligncenter">
### 常用属性
---
:::flexblock 
#### `th:action` {.animated.flipInX.delay-400}

---
#### `th:each` {.animated.flipInX.delay-500}

---
#### `th:field` {.animated.flipInX.delay-600}

---
#### `th:id` {.animated.flipInX.delay-700}

---
#### `th:text`

---
#### `th:value` 

---
#### `th:href` 

---
#### `th:src` 

---
#### `th:if` 

---
#### `th:fragment` 

---
#### `th:replace` 

---
#### `th:include` 
:::

:::note

使用前提，和springboot所需要的其他依赖包一样，可以在maven中添加对应的依赖
指定命名空间，这样就可以根据语法规则来使用thymeleaf了

:::
<slide class="aligncenter">
#### th:fragment
```java {.animated.fadeInUp}
        mav = new ModelAndView("index/index"); // 使用 index 目录下的 index.html 进行渲染显示
        page.setPageIndex(Page.Index.HOME); // 设置当前（首页）Tab 高亮
        mav.addObject("page", page);
        return mav;   
```
```html {.animated.fadeInUp.delay-400}
<!-- header.html -->
<ul class="nav nav-tabs" th:fragment="fragment-header"> <!-- 声明一个碎片，命名为 fragment-header -->
    <!-- 根据不同参数（当前活动页面）高亮不同 tab 选项 -->
    <li role="presentation" th:class="${page.getPageIndex()==0}?'active'"><a th:href="@{/index}">Home</a></li>
    <li role="presentation" th:class="${page.getPageIndex()==1}?'active'"><a th:href="@{/property/}">房屋住户管理</a></li>
    <!-- …… 其他 Tab 选项 …… -->
</ul>
```
```html {.animated.fadeInUp.delay-800}
<div id="header"> <!-- index.html -->
    <div th:replace="~{header :: fragment-header}"></div> <!-- 将当前元素替换为 header.html 文件下定义的 fragment-header 碎片 -->
</div>
```

:::flexblock {.animated.fadeInUp.delay-800}
[:fa-github: 源代码](https://github.com/DarkYoung/Property_Management_System/blob/master/src/main/resources/templates/header.html){.button.ghost.radius}

---
[:fa-link: 演示](http://47.102.98.109:9999/index){.button.radius}
:::

:::note
本次课程使用的 Demo 不方便演示，因此使用了上学期《数据库设计》的物业管理系统项目
:::
<slide class="aligncenter">

```html {.animated.fadeInUp}
<caption th:text="'用户数目: ' + ${userList.size()} + ''"></caption>
<thead class="thead-dark"><tr>
    <th scope="col" th:text="'#'+${userList.size()}"></th>
    <!-- switch语句，根据变量设置对应的中文字段名 -->
    <th scope="col" th:each="field: ${fields}" th:switch="${field.getName()}">
        <label th:case="userPhone">电话</label>
        <label th:case="name">姓名</label>
        <label th:case="gender">性别</label>
        <label th:case="signDate">注册日期</label>
    </th>
</tr></thead>
<tbody>
    <tr th:each="user, status: ${userList}"> <!-- 相当于 jctl 中的 forEach，status 是一个状态变量（包含count、first等属性） -->
        <th scope="row" th:text="|${status.count}|"></th>
        <td class="list" th:each="field: ${fields}">
            <span th:if="${field.getName()=='signDate'}" th:text="${#dates.format(user[field.getName()],'yyyy-MM-dd')}"></span>
            <span th:if="${field.getName()!='signDate'}" th:text="${user[field.getName()]}"></span>
        </td>
    </tr>
</tbody>
```
:::note

status 是状态变量，包含下列属性：

* index：当前迭代对象的迭代索引，从0开始，这是索引属性
* count：当前迭代对象的迭代索引，从1开始，这个是统计属性
* size：迭代变量元素的总量，这是被迭代对象的大小属性
* current：当前迭代变量
* even/odd：布尔值，当前循环是否是偶数/奇数（从0开始计算）
* first：布尔值，当前循环是否是第一个
* last：布尔值，当前循环是否是最后一个

:::
<slide class="aligncenter">
```html {.animated.fadeInUp}
<!-- residentceList 是从路由控制器传过来的待渲染显示的数据（对象链表） -->
<caption th:text="${title}+'：共' + ${residenceList.size()} + '条'"></caption>
<thead class="thead-dark">
<tr>
    <th scope="col" th:text="'#'+${residenceList.size()}"></th>
    <!-- fields 为对象声明的属性集合（通过反射得到） -->
    <!-- FOEMap 是一个 JSONObject，key 为 Bean 定义的属性名， value 为对应的中文/英文名（方便国际化处理）-->
    <th scope="col" th:each="field: ${fields}" th:text="${FOEMap[field.getName()]}"></th>
    <th scope="col">绑定户主</th>
</tr>
</thead>
<tbody>
    <tr th:each="residence, status: ${residenceList}"> 
        <th scope="row" th:text="|${status.count}|"></th>
        <td class="list" th:each="field: ${fields}" th:text="${residence[field.getName()]}"></td>
        <td class="list"><a th:href="'bind?id='+${residence[fields.get(0).getName()]}">绑定</a></td>
    </tr>
</tbody>
```
:::flexblock {.animated.fadeInUp.delay-800}
[:fa-github: 源代码](https://github.com/DarkYoung/Property_Management_System/blob/master/src/main/resources/templates/property/showIdleResidences.html){.button.ghost.radius}

---
[:fa-link: 演示](http://47.102.98.109:9999/property/idle){.button.radius}
:::

<slide :class="aligncenter">


# Thanks {.animated.flipInX.delay-400}