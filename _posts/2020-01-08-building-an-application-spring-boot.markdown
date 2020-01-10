---
layout: post
title:  "Building an Application with Spring Boot"
date:   2020-01-08 14:38:12 +0100
categories: [spring-boot]
---

# Introduction

Spring Boot makes it easy to create stand-alone, production-grade Spring-based Applications that you can run. We take an opinionated view of the Spring platform and third-party libraries, so that you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

# Technologies used

* Spring Boot 2.2.2.RELEASE
* Maven 3

# Maven Installation

Spring Boot is compatible with Apache Maven 3.3 or above.

Spring Boot dependencies use the `org.springframework.boot groupId`. Typically, your Maven POM file inherits from 
the `spring-boot-starter-parent` project and declares dependencies to one or more [Starters](https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/using-spring-boot.html#using-boot-starter). 
So the first thing is to include `spring-boot-starter-parent` to our project.

{% highlight xml %}

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
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

Our final pom.xml file should look similar to this one.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
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

Did you notice that there was not a single line of XML? There is no web.xml file, either. This web application is 100% pure 
Java and you did not have to deal with configuring any plumbing or infrastructure.


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
 :: Spring Boot ::        (v2.2.2.RELEASE)

2020-01-10 17:05:13.255  INFO 13324 --- [           main] com.jocamav.SpringBootStartApplication   : Starting SpringBootStartApplication on **** with PID 13324
2020-01-10 17:05:13.258  INFO 13324 --- [           main] com.jocamav.SpringBootStartApplication   : No active profile set, falling back to default profiles: default
2020-01-10 17:05:14.039  INFO 13324 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8080 (http)
2020-01-10 17:05:14.045  INFO 13324 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2020-01-10 17:05:14.045  INFO 13324 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.29]
2020-01-10 17:05:14.128  INFO 13324 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
2020-01-10 17:05:14.128  INFO 13324 --- [           main] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 835 ms
2020-01-10 17:05:14.252  INFO 13324 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2020-01-10 17:05:14.390  INFO 13324 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http) with context path ''
2020-01-10 17:05:14.392  INFO 13324 --- [           main] com.jocamav.SpringBootStartApplication   : Started SpringBootStartApplication in 1.479 seconds (JVM running for 2.258)
```

To test it open in the browser the URL `http://localhost:8080/` and you should see the greeting message in the browser.

```
Greetings from Spring Boot!
```

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/itext-pdf)

```
git clone https://github.com/jocamav/tutorials.git
cd spring-boot-start
mvn spring-boot:run
```


# References

[Spring Boot Getting Started][spring-guide]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/spring-boot/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/
