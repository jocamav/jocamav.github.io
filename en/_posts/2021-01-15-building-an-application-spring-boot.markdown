---
layout: post
title:  "Building an Application with Spring Boot"
author:  "José Carlos Martínez"
lang: en
lang-ref: building-an-application-with-spring-boot
date:   2021-01-15 14:38:12 +0100
categories: [spring-boot]
---

# Introduction

Spring Boot can help you a lot to create Spring based application in a very simple way with almost no configuration. According 
the official documentation of Spring Boot:

>Spring Boot makes it easy to create stand-alone, production-grade Spring-based Applications that you can run. 
We take an opinionated view of the Spring platform and third-party libraries, so that you can get started with minimum fuss. 
Most Spring Boot applications need very little Spring configuration.

# Technologies used

* Spring Boot 2.4.1
* Maven 3

# Maven Installation

Spring Boot is compatible with Apache Maven 3.3 or above.

Spring Boot dependencies use the `org.springframework.boot groupId`. Typically, your Maven POM file inherits from 
the `spring-boot-starter-parent` project and declares dependencies to one or more [Starters](https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/using-spring-boot.html#using-boot-starter). 
So the first thing is to include `spring-boot-starter-parent` to our project.

{% highlight xml %}

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

{% endhighlight %}

Spring Boot also provides an optional Maven plugin to create executable jars.

{% highlight xml %}

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    
{% endhighlight %}

This plugin provides many convenient features:

* It collects all the jars on the classpath and builds a single, runnable `jar` file , which makes it more convenient to execute and transport your service.
* It searches for the `public static void main()` method to flag as a runnable class.
* It provides a built-in dependency resolver that sets the version number to match Spring Boot dependencies. You can override any version you wish, but it will default to Boot’s chosen set of versions.

For this example we are going to build a basic REST controller. We need to and an extra dependency to include another `Starter`
to build RESTful applications based on Spring MVC.

{% highlight xml %}

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    
{% endhighlight %}

> Spring Boot uses Tomcat as the default embedded container

Our final `pom.xml` file should look similar to this one.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.jocamav</groupId>
    <artifactId>spring-boot-start</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-boot-start</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
    
{% endhighlight %}

# Create a Controller

We can create a `HelloController.java` to return a simple `"Greetings from Spring Boot!"` message:

{% highlight java %}
package com.jocamav.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {
    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }
}

{% endhighlight %}

Let's take a deeper look to all the annotations we have included into this Controller:

* The class is flagged as a `@RestController`, meaning it’s ready for use by Spring MVC to handle web requests.
* `@RequestMapping` maps `/` requests to the `index()` method

When invoked from a browser or using curl on the command line, the method returns pure text. That’s because `@RestController` combines `@Controller` and `@ResponseBody`, two annotations that results in web requests returning data rather than a view.

# Create an Application class

First we will create a a `SpringBootStartApplication.java` class

The final part of our application is to create a class `SpringBootStartApplication.java` with a `main` method. This is just a standard method that follows the Java convention 
for an application entry point. 


{% highlight java %}
package com.jocamav;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringBootStartingApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootStartingApplication.class, args);
    }

}

{% endhighlight %}

It's recommended that you locate your main application class in a root package above other classes. The `@SpringBootApplication`
annotation is often placed on your main class, and it implicitly adds all of the following:

* `@Configuration`: Tags the class as a source of bean definitions for the application context.
* `@EnableAutoConfiguration`: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. 
For example, if spring-webmvc is on the classpath, this annotation flags the application as a web application and activates key behaviors, 
such as setting up a DispatcherServlet.
* `@ComponentScan`: Tells Spring to look for other components, configurations, and services in the `com.jocamav` package, 
letting it find the controllers.

Our `main` method delegates to Spring Boot’s `SpringApplication` class by calling `run` to launch an application. SpringApplication bootstraps 
our application, starting Spring, which, in turn, starts the auto-configured Tomcat web server. We need to pass the 
class (`SpringBootStartingApplication.class` in our example) as an argument to the run method to tell `SpringApplication` which 
is the primary Spring component.

Did you notice that there was not a single line of XML? There is no `web.xml` file, either. This web application **is 100% pure 
Java** and you did not have to deal with configuring any plumbing or infrastructure.


# Execute the application

Execute `mvn spring-boot:run`

Also you can build an executable jar and execute the application as follows:

```
mvn package && java -jar target/spring-boot-start-0.0.1-SNAPSHOT.jar
```

You should see in the logst the application is up and running in the port 8080.

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.4.1)

2021-01-18 20:42:44.971  INFO 37544 --- [           main] com.jocamav.SpringBootStartApplication   : Starting SpringBootStartApplication using Java 1.8.0_231 on Joses-MacBook-Pro.local with PID 37544 (/Users/carlos/Workspace/tutorials/spring-boot-start/target/classes started by carlos in /Users/carlos/Workspace/tutorials/spring-boot-start)
2021-01-18 20:42:44.975  INFO 37544 --- [           main] com.jocamav.SpringBootStartApplication   : No active profile set, falling back to default profiles: default
2021-01-18 20:42:45.851  INFO 37544 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2021-01-18 20:42:45.860  INFO 37544 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2021-01-18 20:42:45.860  INFO 37544 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.41]
2021-01-18 20:42:45.915  INFO 37544 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2021-01-18 20:42:45.915  INFO 37544 --- [           main] w.s.c.ServletWebServerApplicationContext : Root WebApplicationContext: initialization completed in 890 ms
2021-01-18 20:42:46.091  INFO 37544 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-01-18 20:42:46.266  INFO 37544 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2021-01-18 20:42:46.277  INFO 37544 --- [           main] com.jocamav.SpringBootStartApplication   : Started SpringBootStartApplication in 1.662 seconds (JVM running for 2.319)
```

To test it open in the browser the URL `http://localhost:8080/` and you should see the greeting message in the browser.

```
Greetings from Spring Boot!
```

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-start)

```
git clone https://github.com/jocamav/tutorials.git
cd tutorials/spring-boot-start
mvn spring-boot:run
```


# References

[Spring Boot Getting Started][spring-guide]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/spring-boot/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/
[github-logo]: /assets/logos/github-mark-32.png
