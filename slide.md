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

```java {.animated.fadeInUp}
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

<slide class="bg-black-blue">

## 四大注解

:::column
#### @Service

---
#### @Repository

---
#### @Component

---
#### @Controller
:::

<slide :class="size-40 aligncenter">

## 总结
---

* 通过注解`@EnalbleAutoConfiguration`的方式去查找、过滤和加载所需的`configuration`
* `@ComponentScan`扫描我们自定义的`Bean`
* `@SpringBootConfiguration`使得被`@SpringBootApplication`注解的类声明为注解类