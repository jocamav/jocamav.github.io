---
layout: post
title:  "Building an Application with Spring Boot"
date:   2020-01-03 20:38:12 +0100
categories: spring boot
---

# Introduction

Spring Boot makes it easy to create stand-alone, production-grade Spring-based Applications that you can run. We take an opinionated view of the Spring platform and third-party libraries, so that you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

## Technologies used

* Spring Boot 2.2.2.RELEASE
* Maven 3

## pom.xml

Our pom.xml should look as the following one:

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
    <artifactId>spring-boot-starting</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-boot-starting</name>
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

## Controller

We can create a `HelloController.java` class:

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

## Create an Application class

We can create a `HelloController.java` class:

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

## Execute the application

Execute `mvn spring-boot:run`

## References

[Spring Boot Getting Started][spring-guide]
[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/spring-boot/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/
