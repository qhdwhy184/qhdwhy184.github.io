---
layout: post
title: spring mvc 学习笔记
category: 技术
tags: 
- spring
keywords: 
- spring 
description:
---

### spring mvc 学习笔记
项目结构如图：
![项目结构](https://github.com/qhdwhy184/qhdwhy184.github.io/blob/master/_posts/pictures/%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84%E5%9B%BE.png?raw=true)

web.xml是所有配置的入口

##### web.xml

```
<servlet>
    <servlet-name>springmvc-app</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring-servlet-config.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
 
<servlet-mapping>
    <servlet-name>springmvc-app</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
 
<welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
</welcome-file-list>
```

- spring mvc 通过DispatcherServlet拦截web请求。
- &lt;init-param&gt;指定了spring mvc配置文件的位置。
- &lt;load-on-startup&gt;通常为正整数，指定了启动顺序，这里1表示第一个启动。

DispatcherServlet的初始化在spring-servlet-config.xml中进行。
##### spring-servlet-config.xml

```
<mvc:default-servlet-handler />
<mvc:annotation-driven/>
<context:component-scan base-package="wyhlearn.controller"/>
<bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

&lt;mvc:default-servlet-handler /&gt; 作用：

- 把对ip:port的web请求，映射到&lt;welcome-file-list&gt;&lt;welcome-file&gt;index.jsp&lt;/welcome-file&gt;&lt;/welcome-file-list&gt;，
- 将controller返回的string映射到/WEB-INF/{string}.jsp

##### pom依赖

```
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>4.2.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.6.4</version>
    </dependency>
</dependencies>
```

- spring-webmvc：依赖了spring mvc所有核心jar包
- jackson-databind：在spring-webmvc 4.1.x版本之后，通过依赖这个jackson-databind，结合@ResponseBody注解，可以将controller返回的对象自动转为json。

##### controller

```
@Controller
public class TestController {
    @RequestMapping(value = "/sayJson")
    @ResponseBody
    public Map<String, String> sayJson() {
        Map<String, String> map = new HashMap<String, String>();
        map.put("hello", "world");
        return map;
    }
 
    @RequestMapping("/sayString")
    @ResponseBody
    public String sayString() {
        return "helloworld";
    }
 
    @RequestMapping("/sayJsp")
    public String sayJsp() {
        return "helloworld";
    }
}
```

- sayJson 直接返回 json串 “{hello:world}”。
- sayString直接返回字符串“helloworld”
- sayJsp返回的helloworld会被映射为/WEB-INF/helloworld.jsp