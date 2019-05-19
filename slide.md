title: 高级Web课堂演示-Part1
speaker: 张健
plugins:
    - echarts
<slide class="bg-green aligncenter">
# Spring Boot

---

By 谢东方、张健、陈雷远、陈涛 {.text-intro}

<slide class="bg-green aligncenter" >

# Part 1 {.text-shadow.animated.flipInX}

---

#### Spring Boot 注解 & Thymeleaf 模板引擎 {.text-shadow}

By 张健 {.text-intro}

[:fa-github: Github](https://github.com/DarkYoung){.button.ghost}


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
* @EnableAutoConfiguration：启用自动配置，尝试根据添加的 jar 包一拉自动配置 Spring 项目
* @ComponentScan：组件扫描，可以自动发现和装配一些 Bean（包括@Component、@Controller、@Service等，并注册成 Bean），通常使用该注解搜索并注册 Beans，然后使用 @Autowired 注解导入

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

## Spring 四大注解(用于软件系统不同层次)
---

:::column
#### @Repository
作用在数据访问层

---
#### @Component
定义一个组件（任何层次）

---
#### @Service
作用在业务层

---
#### @Controller
作用在控制层

:::

:::note
@Repository 能够将所标注类中抛出的数据访问异常封装为 Spring 的数据访问异常类型（Spring 封装的不同持久化框架抛出的异常，使得异常独立于底层框架）

@Component 泛指组件，当定义的组件不好归类时，可以使用该注解进行标注

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
@RequestMapping("/")  // 表示该控制器处理所有 classpath:/templates/ 的 URL 请求
public class LoginController {
    // 全部基于 Thymeleaf 的默认配置，前缀为 classpath:/templates/ 后缀为.html
    // 映射路径为 classpath:/templates/login.html classpath:/templates/index.html
    @RequestMapping(value={"login", "index", ""})
    public String index(Model model,  @RequestParam(value = "error", required = false) String error) {
        if (error != null) {
            model.addAttribute("error" , "用户名或者密码错误！");
        }
        //会使用 login/index.html 模板进行渲染显示，这里使用的是 html 模板文件
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
        return "ad/index"; // 使用 ad/index.html 模板进行渲染显示
    }
    // 路径为 /detail/{adId} 的 URL 请求都使用此方法处理（{adId}表示变量）
    @RequestMapping("/detail/{adId}")
    public String detail(
        @PathVariable("adId") String adId, 
        Model model) {
        \\……
        return "ad/detail"; // 使用 ad/detail.html 模板进行渲染显示
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
    <!-- 相当于 jsp 中的 forEach， user 是要迭代的变量，userList 是要迭代的列表
    status 是一个状态变量（包含count、first等属性） -->
    <tr th:each="user, status: ${userList}"> 
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


<!-- Part 2 -->
<slide class="bg-green aligncenter" >

# Part 2 {.text-shadow.animated.flipInX}

---

#### Spring Security 机制{.text-shadow}

By 谢东方 {.text-intro}

[:fa-github: Github](https://github.com/zhaoyangyingmu){.button.ghost}

<slide image="http://47.102.98.109/img/page1.jpg">
<slide image="http://47.102.98.109/img/page2.jpg">
<slide image="http://47.102.98.109/img/page3.jpg">
<slide image="http://47.102.98.109/img/page4.jpg">
<slide image="http://47.102.98.109/img/page6.jpg">
<slide image="http://47.102.98.109/img/page7.jpg">
<slide image="http://47.102.98.109/img/page8.jpg">
<slide image="http://47.102.98.109/img/page9.jpg">
<slide image="http://47.102.98.109/img/page10.jpg">
<slide image="http://47.102.98.109/img/page11.jpg">

<!-- Part 3 -->
<slide class="bg-green aligncenter" >

# Part 3 {.text-shadow.animated.flipInX}

---

#### Mybatis {.text-shadow}

By Chen Tao {.text-intro}

[:fa-github: Github](https://github.com/ksky521/nodeppt){.button.ghost}


<slide>

# 什么是 *MyBatis*？
---
MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生类型、接口和 Java 的 POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。
<slide>

# MyBatis 流程
---

:::steps {.animated.fadeInUp}

SqlSessionFactoryBuilder读取配置文件

---

生成SqlSessionFactory

---

生成SqlSession

---

SqlSession 获取Mapper

--- 

Mapper执行语句

:::

:::note
没有整合spring boot前面，每次查询都要手动管理sqlSession等，步骤繁琐
:::
<slide>

# Spring Boot 结合 MyBatis 
---
- 在Spring Boot中不再需要手动管理SqlSessionFactory、SqlSession
- 在Service直接获取dao，调用dao的函数
- 该函数会自动映射到mapper文件中sql语句
- 执行sql语句之后会返回结果给service

<slide>

# Spring Boot 结合 MyBatis 配置
---
```xml {.animated.fadeInUp}
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.0.1</version>
        </dependency>
```
:::note
需要在pom.xml中添加依赖
:::
<slide>

# Spring Boot 结合 MyBatis 配置
---
```yaml {.animated.fadeInLeft}
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/demo?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT%2B8
    username: root
    password: 123456
    driver-class-name: com.mysql.cj.jdbc.Driver
```

```yaml {.animated.fadeInRight}
mybatis:
  type-aliases-package: com.estate.estate.entity # 实体类的存放路径
  mapper-locations: classpath*:mapper/*.xml # mapper的存放路径
```
:::note
在application.yml中配置spring标签下配置数据库基本配置</br>
在mybatis标签下配置实体类和映射文件的存放路径
:::

<slide>

# Spring Boot 结合 MyBatis 配置
---
```java {.animated.fadeInLeft}
@SpringBootApplication
@MapperScan("com.estate.estate.dao")//增加接口所在的包
public class BackgroundApplication {
    public static void main(String[] args) {
        SpringApplication.run(BackgroundApplication.class, args);
    }
}
```

```yaml {.animated.fadeInRight}
logging: #非必须，用来debug的时候查看输出
  level:
    com.estate.estate.dao: DEBUG
```
:::note
需要使用MapperScan注解指明dao层所在的包</br>
可以增加log输出，方便调试
:::

<slide>

## MyBatis 映射文件 Mapper
:::column {.animated.fadeInUp}
使用@Mapper注解的Java文件
```java
@Mapper
@Component("AdMapper")
public interface AdMapper {
    @Select(/*sql语句*/)
    selectMethod();
    @Insert(/*sql语句*/)
    insertMethod();
    //……
    
}
```
---
使用xml的映射文件
```xml
<mapper namespace="com.estate.estate.dao.AdMapper">
  <resultMap id="BaseResultMap" type="..."/>
  <select id="selectMethod" parameterType="." resultMap=".">
  <!--....-->
  </select>
  <insert id="insertMethod" parameterType="...">
  <!--....-->
  </insert>
  <!--....-->
</mapper>
```
:::

:::note
MyBatis 的映射文件是进行sql查询的基础</br>
sql语句都是在mapper中写好，定义好接口</br>
之后在需要查询的时候直接调用接口，程序就会自动执行对应的语句进行数据库操作</br>
两种方法都可以作为mapper，可能一看之下使用mapper注解的java文件更为简单</br>
但是实际上使用xml文件是一个更好的选择，因为xml文件可以复用一些内容</br>
可以更好的使用动态sql语句</br>
更主要的是，xml文件可以使用工具自动生成</br>
:::

<slide>

# better-mybatis-generator 的使用
---
一款很好用的自动生成dao、entity、mapper的Intellij插件

:::steps

File

---

Setting

---

Plugins

---

Browse Repositories
:::

:::note
这个插件会自动帮我们生成dao层接口、数据库表的实体类、mapper文件、以及操作辅助类example</br>
对于单表操作的增删改查的sql语句基本做到都可以覆盖</br>
很大程度上免除了编写sql语句的烦恼</br>
:::

<slide class="size-100">

## dao、Mapper xml文件

:::column
```java
public interface AdMapper {
    long countByExample(AdExample example);

    int deleteByExample(AdExample example);

    int insert(Ad record);

    List<Ad> selectByExample(AdExample example);

    Ad selectByPrimaryKey(Integer adId);

    int updateByPrimaryKeySelective(Ad record);
}
```
---
```xml
<mapper namespace="com.estate.estate.dao.AdMapper">
  <resultMap id="BaseResultMap" type="com.estate.estate.entity.Ad"/>
  
  <sql id="Base_Column_List"/>
  
  <select id="countByExample" parameterType="." resultType="."/>
  
  <delete id="deleteByExample" parameterType="."/>
  
  <insert id="insert" parameterType="."/>
  
  <update id="updateByExampleSelective" parameterType="map"/>
</mapper>
```
:::

:::note
mapper和dao层接口是对应的，接口中的函数在mapper中使用标签属性中的id来进行对应</br>
mapper中还要定义参数类型、返回类型等</br>
:::
<slide>

## 动态sql

:::flexblock 

### if

---

### choose、when

---

### where

---

### foreach
:::

:::note
动态sql让sql语句不再是以前的一个sql只能表示一个语句</br>
它让sql语句可以复用</br>
:::
<slide>

## 使用mapper进行增删改查
---
```java
    @Autowired //使用Autowired标签，自动装配
    private AdMapper adMapper;
    @Override
    public int insert(Ad record) {
        //....
        return adMapper.insertSelective(record);
    }
```
:::note
使用Autowired标签，会自动装配</br>
使用dao的函数的时候，会自动对应到mapper中的sql语句，进行操作</br>
:::
<slide>

## 使用Example类

```java
    @Override
    public List<Ad> selectInvalidAd() {
        AdExample adExample = new AdExample();
        adExample.createCriteria().andValidEqualTo(0);
        return adMapper.selectByExampleWithBLOBs(adExample);
    }

    @Override
    public List<Ad> selectByTitle(String title) {
        if (title == null || title.trim().length() == 0) {
            return new ArrayList<>();
        }
        AdExample adExample = new AdExample();
        adExample.createCriteria().andTitleLike("%" + title + "%");
        return adMapper.selectByExampleWithBLOBs(adExample);
    }
```
:::note
example类的是在动态sql的基础上实现的，</br>
使用起来非常方便，</br>
我们需要使用where语句时候，不需要一个条件写一条sql语句，只要使用example类，使用其中的函数即可</br>
example类还可以添加limit字句进行分页查询</br>
:::

<slide>

# MyBatis 高级查询

:::flexblock

#### 嵌套select查询

---

#### 嵌套结果映射

:::

:::note
高级查询是指多表联合查询，有外键连接时，经常使用到
:::
<slide>

# MyBatis 高级查询标签

:::flexblock

```xml
<association property="" column=""/>
```

---

```xml
<collection property="" column=""/>
```

:::

:::note
association用于一对一查询时</br>
collection用于一对多查询时</br>
:::
<slide>
## 示例数据库结构
:::column
```
class
    id
    name

```
---
```
student
    id
    name
    class_id
```
:::

:::note
两张表通过外键class_id相关联
:::
<slide>

## 对应实体类

:::column
```java
public class Clazz implements Serializable {
    private Integer id;

    private String name;

    private List<Student> studentList;//存储该班的学生
}

```
---
```java
public class Student implements Serializable {
    private Integer id;

    private String name;

    private Integer classId;

    private Clazz clazz;//该学生所在班级
}
```
:::
<slide>

## 嵌套select查询

```xml
    <resultMap id="BaseResultMap" type="com.example.demo.entity.Clazz">
        <id column="id" jdbcType="INTEGER" property="id"/>
        <result column="name" jdbcType="VARCHAR" property="name"/>
        <collection property="studentList" column="id" select="selectStudent"/>
    </resultMap>
    
    <select id="selectByPrimaryKey" parameterType="java.lang.Integer" resultMap="BaseResultMap">
            select
            <include refid="Base_Column_List"/>
            from class
            where id = #{id,jdbcType=INTEGER}
     </select>

    <select id="selectStudent" parameterType="java.lang.Integer" resultType="com.example.demo.entity.Student">
        select id , name , class_id as classId from student where class_id=#{id,jdbcType=INTEGER}
    </select>

```
:::note
嵌套select查询，在resultmap中使用再次使用select，select语句要另外再构建</br>
嵌套select查询实现的sql语句会简单一点，就是两次查询语句，不用join什么的</br>
但是这样会造成N+1问题</br>
N+1问题就是在查询有关联表结果时，一次查询有n条记录，要这n条记录关联的其他表的数据，需要再次进行N次查询</br>
:::
<slide>

## 嵌套结果映射查询

```xml
    <resultMap id="Collect" type="com.example.demo.entity.Clazz">
        <id property="id" column="class_id" jdbcType="INTEGER"/>
        <result property="name" column="class_name" jdbcType="VARCHAR"/>
        <collection property="studentList" ofType="com.example.demo.entity.Student">
            <id property="id" column="student_id" jdbcType="INTEGER"/>
            <result property="name" column="student_name" jdbcType="VARCHAR"/>
            <result property="classId" column="class_id" jdbcType="VARCHAR"/>
        </collection>
    </resultMap>
    <select id="selectByPrimaryKey2" parameterType="java.lang.Integer" resultMap="Collect">
            select
            class.id as class_id,
            class.name as class_name,
            student.id as student_id,
            student.name as student_name
            from class left join student on class.id = student.class_id
            where class.id = #{id,jdbcType=INTEGER}
    </select>

```
:::note
嵌套结果映射查询是将结果进行嵌套映射，首先映射查询对象，然后对对象中的某个变量再次进行映射</br>
这样做的sql语句写起来会麻烦点，为了避免查询出来的列名相同的情况，最好使用as关键字进行别名修改</br>
这样在resultmap也要进行相对于的设置column</br>
当然，也可以不使用以上association和collection两个标签，直接新建实体类对应自己要查询的字段，再构造新的sql语句</br>
不过这样比较麻烦，新的实体类也只能用在这个sql语句</br>
:::


<!-- Part 4 -->
<slide class="bg-green aligncenter" >

# Part 4 {.text-shadow.animated.flipInX}

---

#### redis和日志管理 {.text-shadow}

By 陈雷远 {.text-intro}


:::note

大家好，今天由我给大家介绍一下redis数据库以及spring日志管理的相关内容

:::


<slide :class="size-60 aligncenter">

## 什么是redis数据库
---

### Redis是一个开源的使用C语言编写、可基于内存亦可持久化的日志型、Key-Value型、并提供多种语言的API的非关系型数据库

:::note
首先我们来看一下什么是redis数据库，以及redis数据库相比我们大一学的mysql数据库有什么区别？
用官方介绍的话来讲，Redis是一个开源的使用C语言编写、可基于内存亦可持久化的日志型、Key-Value型、并提供多种语言的API的非关系型数据库。
从这段话中，我们可以了解到redis数据库中的数据既可以存在内存中，这样有着较快的访问速度，但关机后删除，同时也可以持久化， 将内存中数据保存在磁盘中，
重启时候再次加载使用，同时他不同于我们大一学习的mysql以表的方式存储数据，redis中的数据都是以键值对的方式进行存储
      

:::

<slide>


## redis的特点


### 性能极高
---
### 丰富的数据类型
---
### 原子性和管道技术


:::note
接下来我们再来看一下redis数据库的三大特点，第一个特点是 redis数据库有着极高的性能
Redis能读的速度是110000次/s,写的速度是81000次/s 
其次Redis支持丰富的数据类型 包括字符串 集合 哈希 列表 有序集合等，这也是我们接下来会详细介绍的部分
最后redis数据库所有操作都是满足原子性的，多个操作也支持事务 同时拥有管道技术，可以持续向服务器发出请求 
:::




<slide :class="size-100 aligncenter">

#### 字符串与哈希

---
:::column
``` {.animated.fadeInUp}
redis 127.0.0.1:6379> COMMAND KEY_NAME

redis 127.0.0.1:6379> SET localhost 127.0.0.1
OK
redis 127.0.0.1:6379> GET localhost
"127.0.0.1"
```
----

``` {.animated.fadeInUp.delay-400}
127.0.0.1:6379>  HMSET peter age 16 
height 160
weight 50
OK
127.0.0.1:6379>  HGETALL peter
1) "age"
2) "16"
3) "height"
4) "160"
5) "weight"
6) "50"
```
:::


:::note

字符串是redis中最基础的数据类型，而哈希则是一个string类型的field和value的映射表，hash特别适合用于存储对象。
左边的图通过redis命令行 set命令 设置了字符串类型的键值对 localhost 和127.0.0.1的映射，
get命令得到localhost在数据库中键所对应的值127.0.0.1
而右边的图通过HMSET 命令设置了 一个hash，这个hash其实相当于一个对象，描述peter这个人，他有三个属性，年龄身高 体重
同时在设置好键值对后我们可以通过getall命令得到键对应的hash遍历

:::



<slide :class="size-100 aligncenter">

#### 集合和有序集合

---
:::column
``` {.animated.fadeInUp}
redis 127.0.0.1:6379> SADD set1 redis
(integer) 1
redis 127.0.0.1:6379> SADD set1 mongodb
(integer) 1
redis 127.0.0.1:6379> SADD set1 mysql
(integer) 1
redis 127.0.0.1:6379> SADD set1 mysql
(integer) 0
redis 127.0.0.1:6379> SMEMBERS set1

1) "mysql"
2) "mongodb"
3) "redis" 
```
----

```{.animated.fadeInUp.delay-400}
redis 127.0.0.1:6379> ZADD set2 1 redis
(integer) 1
redis 127.0.0.1:6379> ZADD set2 2 mongodb
(integer) 1
redis 127.0.0.1:6379> ZADD set2 3 mysql
(integer) 1
redis 127.0.0.1:6379> ZADD set2 3 mysql
(integer) 0
redis 127.0.0.1:6379> ZADD set2 4 mysql
(integer) 0
redis 127.0.0.1:6379> ZRANGE set2 0 10 WITHSCORES

1) "redis"
2) "1"
3) "mongodb"
4) "2"
5) "mysql"
6) "4"
```
:::


:::note
接下来看集合和无序集合
在Redis 当中集合是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据。
Redis 中集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
在左图的例子中，我们构建了set1这个集合，往里面重复添加了mysql，服务器返回0，同时并未将重复数据存储
Redis 有序集合和集合一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。
有序集合的成员是唯一的,但分数(score)却可以重复。 和集合一样，有序集合也是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)。 
再右边的图中我们可以看到当我们重复往有序集合中添加 有着不同分数的mysql值时候，mysql的score进行了更新
:::


<slide :class="size-60 aligncenter">

#### 列表

---
```{.animated.fadeInUp}
redis 127.0.0.1:6379> LPUSH list redis
(integer) 1
redis 127.0.0.1:6379> LPUSH list mongodb
(integer) 2
redis 127.0.0.1:6379> LPUSH list mysql
(integer) 3
redis 127.0.0.1:6379> LRANGE list 0 10

1) "mysql"
2) "mongodb"
3) "redis"
```

:::note
我们再来看redis最后一个数据类型 列表，Redis列表是简单的字符串列表，按照插入顺序排序。
redis提供给用户非常多的方法，你可以想操作栈一样操作列表 即用pop 和push命令 也可以像数组一样 通过索引去操纵指定值
还可以像字符串一样，删除列表中间一段内容。在下图的例子中我们用push命令往list中添加值， redis会返回list的长度，同时通过range 命令我们就可以遍历list
:::

<slide :class="size-60 aligncenter">

#### 事务

---
``` {.animated.fadeInUp}
redis 127.0.0.1:6379> MULTI
OK
redis 127.0.0.1:6379> SET book-name "Mastering C++ in 21 days"
QUEUED
redis 127.0.0.1:6379> GET book-name
QUEUED
redis 127.0.0.1:6379> SADD tag "C++" "Programming" "Mastering Series"
QUEUED
redis 127.0.0.1:6379> SMEMBERS tag
QUEUED
redis 127.0.0.1:6379> EXEC
1) OK
2) "Mastering C++ in 21 days"
3) (integer) 3
4) 1) "Mastering Series"
   2) "C++"
   3) "Programming"
```

:::note
1


在讲完redis基本数据结构后我们再来看看redis的事务
以下是一个事务的例子， 它先以 MULTI 命令开始一个事务， 然后将多个命令入队到事务中， 最后由 EXEC 命令触发事务， 一并执行事务中的所有命令：
Redis会将一个事务中的所有命令序列化，然后按顺序执行。Redis保证了不在一个事务的执行过程中插入执行另一个客户端发出的请求。这样Redis便将这些命令作为一个单独的隔离操作执行。
在一个Redis事务中，Redis要么执行其中的所有命令，要么什么都不执行。因此，Redis事务能够保证原子性。
:::


<slide :class="size-60">

#### 管道技术

---
Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。这意味着通常情况下一个请求会遵循以下步骤：
- 客户端向服务端发送一个查询请求，并监听Socket返回，通常是以阻塞模式，等待服务端响应。
- 服务端处理命令，并将结果返回给客户端。

Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。

``` {.animated.fadeInUp}
$(echo -en "PING\r\n SET demokey redis\r\nGET demokey\r\nINCR 
visitor\r\nINCR visitor\r\nINCR visitor\r\n"; sleep 10) | nc localhost 6379

+PONG
+OK
redis
:1
:2
:3
```

:::note
Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。这意味着通常情况下一个请求会遵循以下步骤：
- 客户端向服务端发送一个查询请求，并监听Socket返回，通常是以阻塞模式，等待服务端响应。
- 服务端处理命令，并将结果返回给客户端。

Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。
在以上实例中我们通过使用 PING 命令查看redis服务是否可用， 之后我们设置了 demokey 的值为 redis，然后我们获取 demokey 的值并使得 visitor 自增 3 次。

在返回的结果中我们可以看到这些命令一次性向 redis 服务提交，并最终一次性读取所有服务端的响应
:::








<slide :class="size-120 aligncenter">
### spring连接redis数据库
```maven {.animated.fadeInLeft}
//1.配置maven依赖
<properties>
    <!-- redis 版本 -->
    <redis.version>2.9.0</redis.version>
    <spring.redis.version>1.8.4.RELEASE</spring.redis.version>
</properties>
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>${redis.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>${spring.redis.version}</version>
</dependency>
```
:::note
1


再介绍完redis数据库基本信息后，我们再来看看spring 是如何整合redis数据库的，首先是maven配置，要将redis对应版本，和依赖写进去
:::


<slide :class="size-120 aligncenter">
### spring连接redis数据库
```xml {.animated.fadeInLeft}
//2.设置redis数据库
 <!-- 加载配置文件 -->  
    <context:property-placeholder location="classpath:redis.properties" />  
    <!-- redis数据源 -->
    <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大空闲数 -->
        <property name="maxIdle" value="${redis.maxIdle}" />
        <!-- 最大空连接数 -->
        <property name="maxTotal" value="${redis.maxTotal}" />
        <!-- 最大等待时间 -->
        <property name="maxWaitMillis" value="${redis.maxWaitMillis}" />
        <!-- 连接超时时是否阻塞，false时报异常,ture阻塞直到超时, 默认true -->
         <property name="blockWhenExhausted" value="${redis.blockWhenExhausted}" /> 
        <!-- 返回连接时，检测连接是否成功 -->
        <property name="testOnBorrow" value="${redis.testOnBorrow}" />
    </bean>

    <!-- Spring-redis连接池管理工厂 -->
    <bean id="jedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory">
        <!-- IP地址 -->
        <property name="hostName" value="${redis.host}" />
        <!-- 端口号 -->
        <property name="port" value="${redis.port}" />
        <!-- 超时时间 默认2000-->
        <property name="timeout" value="${redis.timeout}" />
        <!-- 连接池配置引用 -->
        <property name="poolConfig" ref="poolConfig" />
        <!-- usePool：是否使用连接池 -->
        <property name="usePool" value="true"/>
    </bean>

```

:::note
其次我们利用property文件对redis数据库进行一些设置包括redis数据源，最大空闲数，

最大空连接数 ，最大等待时间，IP地址 端口号等等
:::


<slide :class="size-120 aligncenter">
### spring连接redis数据库
```java {.animated.fadeInLeft}
//3. redisUtil工具类
 public final class RedisUtil {  
    private Logger logger = Logger.getLogger(RedisUtil.class);  
    private RedisTemplate<Serializable, Object> redisTemplate;  
  
    //删除value
    public void remove(final String... keys) {  
        for (String key : keys) {  
            remove(key);  
        }  
    }  
  
    //删除key
    public void removePattern(final String pattern) {  
        Set<Serializable> keys = redisTemplate.keys(pattern);  
        if (keys.size() > 0)  
            redisTemplate.delete(keys);  
    }  
  
    //读取缓存
    public Object get(final String key) {  
        Object result = null;  
        ValueOperations<Serializable, Object> operations = redisTemplate  
                .opsForValue();  
        result = operations.get(key);  
        return result;  
    }  
  
    //写入缓存
    public boolean set(final String key, Object value) {  
        boolean result = false;  
        try {  
            ValueOperations<Serializable, Object> operations = redisTemplate  
                    .opsForValue();  
            operations.set(key, value);  
            result = true;  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
        return result;  
    }    
    
  
    public void setRedisTemplate(  
            RedisTemplate<Serializable, Object> redisTemplate) {  
        this.redisTemplate = redisTemplate;  
    }  
}  
```
:::note
接着我们可以构造一个redis util类，对一些基本操作进行封装

 方便我们使用 包括 插入 删除，对于缓存的控制也可以放在这里面
:::


<slide :class="size-120 aligncenter">
### spring连接redis数据库
```java {.animated.fadeInLeft}
//4.配置bean
public class User implements Serializable{

    private static final long serialVersionUID = 1L;
    private int uid;
    private String userName;
    private String passWord;
    private int salary;
    public int getUid() {
        return uid;
    }
    public void setUid(int uid) {
        this.uid = uid;
    }
    public String getUserName() {
        return userName;
    }
    public void setUserName(String userName) {
        this.userName = userName;
    }
    public String getPassWord() {
        return passWord;
    }
    public void setPassWord(String passWord) {
        this.passWord = passWord;
    }
    public int getSalary() {
        return salary;
    }
    public void setSalary(int salary) {
        this.salary = salary;
    }
    public User(int uid, String userName, String passWord, int salary) {
        super();
        this.uid = uid;
        this.userName = userName;
        this.passWord = passWord;
        this.salary = salary;
    }
   

}
```

:::note
第四步，则是构造我们需要的bean，注意因为redis

保存对象的时候要求对象是序列化的，所以我们的bean要是实现序列化的接口，
:::

<slide :class="size-120 aligncenter">
### spring连接redis数据库
```java {.animated.fadeInLeft}
//5. 在service中配置相应方法
    public User findUserById(int id) {
        String key = "user_" + id;

        ValueOperations<String, User> operations = redisTemplate.opsForValue();

        boolean hasKey = redisTemplate.hasKey(key);
        if (hasKey) {
            User user = operations.get(key);
            System.out.println("==========从缓存中获得数据=========");
            System.out.println(user.getUserName());
            System.out.println("==============================");
            return user;
        } else {
            User user = userDao.findUserById(id);
            System.out.println("==========从数据表中获得数据=========");
            System.out.println(user.getUserName());
            System.out.println("==============================");

            // 写入缓存
            operations.set(key, user, 5, TimeUnit.HOURS);
            return user;
        }

    }

```

:::note
最后一步就是在service中配置相应方法，比如这个示例代码的方法实现了根据id找user，他首先根据输入的id判断是不是在缓存中，
如果在则直接从缓存中取，不在则去数据表拿，同时将结果写入缓存之中。
:::

<slide>


## spring 日志管理概述
##### SpringBoot 内部日志系统使用的是 Commons Logging  
##### SpringBoot 给 JDKLogging , Log4j2(Log4j也是支持的) , Logback 都提供了默认配置
##### logging格式
!![](https://img-blog.csdn.net/20180828112411869?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVqYXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

日志时间：精确到毫秒
日志级别：ERROR， WARN， INFO， DEBUG or TRACE
进程 id
分割符：用于区分实际的日志记录
线程名：括在方括号中
日志名字：通常是源类名
日志信息




:::note
接下来我再给大家介绍下spring日志管理的相关内容
一般来说网页都会用到日志管理 好的日志管理可以快速定位问题出现的位置，也可以提高代码的阅读性。
SpringBoot 内部日志系统使用的是 Commons Logging  
SpringBoot 给 JDKLogging , Log4j2(Log4j也是支持的) , Logback 都提供了默认配置
我们首先看看spring logging的格式 他的logging包括七部分，第一部分是日志时间：精确到毫秒
第二部分是日志级别：有ERROR， WARN， INFO， DEBUG or TRACE五个级别
第三部分是进程 id
第四部分是分割符：用于区分实际的日志记录
第五部分是线程名：通常括在方括号中
第六部分是日志名字：通常是源类名
最后一部分就是日志信息

:::
<slide>


## 日志级别
##### 日志级别从低到高依次是：TRACE < DEBUG < INFO < WARN < ERROR < FATAL 
##### Spring Boot 只会输出比当前级别高的日志，默认的日志级别是 INFO，因此低于 INFO 级别的日志记录都不输出。
```java
@SpringBootTest
@RunWith(SpringRunner.class)
public class LoggerTest {

    private static final Logger logger = LoggerFactory.getLogger(LoggerTest.class);

    @Test
    public void test() {
        logger.trace("trace 级别的日志");
        logger.debug("debug 级别的日志");
        logger.info("info 级别的日志");
        logger.warn("warn 级别的日志");
        logger.error("error 级别的日志");
    }
}
```

!![](https://img-blog.csdn.net/20180828120828160?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVqYXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

:::note
在spring中日志级别从低到高依次是：TRACE < DEBUG < INFO < WARN < ERROR < FATAL 
Spring Boot 只会输出比当前级别高的日志，默认的日志级别是 INFO，因此低于 INFO 级别的日志记录都不输出。
比如下面这个代码运行后，并不能成功输出trace 和debug级别的日志

:::
<slide>
## 日志文件输出&通用配置
##### 默认情况下，Spring Boot 日志仅输出在控制台，不会写入日志文件。如果想要写入日志文件，需要在application.properties 中设置 logging.file 或 logging.path 属性。
- logging.file：设置文件，可以是绝对路径，也可以是相对路径。
- logging.path：设置目录，会在该目录下创建一个 spring.log 文件，写入日志内容
#####  日志文件在达到 10 MB 时会轮换，并且与控制台输出一样，默认情况下会记录 ERROR 级别，WARN 级别和 INFO 级别的消息。 可以使用 logging.file.max-size 属性更改大小限制。

## 日志通用配置
```
# 启用日志颜色
spring.output.ansi.enabled=always
logging.level.root=INFO
# mapper 接口所在的包设置为 debug
logging.level.com.×××.mapper=DEBUG
# 在当前项目下生成日志文件
logging.file=./logs/×××.log
logging.pattern.console=%d{yyyy/MM/dd-HH:mm:ss} [%thread] %-5level %clr(%logger){cyan} %clr(%msg%n){green}
logging.pattern.file=%d{yyyy/MM/dd-HH:mm} [%thread] %-5level %logger- %msg%n
```

:::note
默认情况下，Spring Boot 日志仅输出在控制台，不会写入日志文件。如果想要写入日志文件，需要在application.properties 中设置 logging.file 或 logging.path 属性
 logging.file比较好理解，就是直接设置文件，可以是绝对路径，也可以是相对路径。
 logging.path则是设置目录，会在该目录下创建一个 spring.log 文件，写入日志内容
  日志文件在达到 10 MB 时会轮换，并且与控制台输出一样，默认情况下会记录 ERROR 级别，WARN 级别和 INFO 级别的消息。 
  可以使用 logging.file.max-size 属性更改大小限制。
  一般日志的通用配置见下图 包括启动日志颜色，设置日志级别。输出格式等
:::

<slide class="aligncenter">

# [:fa-link: 完整的演示](http://47.100.106.153:18080){.button.radius}

<slide :class="aligncenter">


# Thanks {.animated.flipInX.delay-400}
:::note
我今天的介绍就到这里，谢谢大家
:::