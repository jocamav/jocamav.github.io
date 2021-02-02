---
layout: post
title:  "Creating a REST service with Spring Boot"
author:  "José Carlos Martínez"
lang: en
lang-ref: creating-rest-service-with-spring-boot
date:   2021-01-31 14:38:12 +0100
comments: true
categories: [spring-boot]
logo: spring
---

Learn how to create a REST service with Spring Boot in a few steps. Thanks to Spring you'll be able to create your first REST
service in a couple of minutes.

# Technologies used

* Spring Boot 2.4.1
* Maven 3

# Maven Installation

To create a REST API you'll need to include Spring MVC to your project. Let's start creating a Spring Boot project including 
the dependency `spring-boot-starter-web`in our `pom.xml`. 


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
    <artifactId>spring-boot-rest-start</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-boot-rest-start</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>1.8</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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

# Create Representation Class

The first step is to create a POJO class describing our resource. In our example we are going to create a simple object 
`Person` with these attributes: 

* `id`: A Long value as unique identifier.
* `name`: A String for the name of the person.
* `familyName`: A String for the family name of the person.

Create a new class `Person.java` as a plain old Java object including all the fields, constructor and getters. 
> It's a very good practice to define the fields as final and assign all the values in the Constructor.
> This way we ensure the object will be immutable. 

{% highlight java %}
package com.jocamav.springbootreststart;

public class Person {

    private final Long id;
    private final String name;
    private final String familyName;

    public Person(Long id, String name) {
        this.id = id;
        this.name = name;
        this.familyName = "Snow";
    }

    public Long getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getFamilyName() {
        return familyName;
    }
}

{% endhighlight %}

> In this case we are setting the family name always as `Snow`. It's just to reduce the number of parameters 
>of the constructor

Auto-configuration for Jackson is provided in Spring as Jackson is part of `spring-boot-starter-json`. When Jackson is on the 
classpath an `ObjectMapper` bean is automatically configured. This is very handy as we don't need to configure any additional
converter. Spring will map our POJO class into JSON automatically. 

# Create a Controller

The next step is to create a REST Controller. A REST Controller is a normal Controller class which is annotated with the annotation 
`@RestController`. `@RestController` is a composed annotation that is itself meta-annotated with `@Controller` 
and `@ResponseBody`. `@ResponseBody` tells Spring the response should be converted directly (using Jackson in our case) 
**instead looking for a view in order to render a HTML template**.


![Screenshot 01](/assets/img/20210131springbootrest/screenshot01.png)

> Annotate a class with `@RestController` has the same behaviour than including both annotations `@Controller` `@ResponseBody`
>in the Controller class.

Let's create a `PersonController.java` class as follows. 

{% highlight java %}
package com.jocamav.springbootreststart;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.concurrent.atomic.AtomicLong;

@RestController
public class PersonController {

    private final AtomicLong counter = new AtomicLong();

    @GetMapping("/person")
    public Person getPerson(@RequestParam(value = "name", defaultValue = "Jon") String name) {
        return new Person(counter.incrementAndGet(), name);
    }
}

{% endhighlight %}

In addition to `@RestController` we added the annotation `@GetMapping` to the method `getPerson()`. The `@GetMapping` annotation 
ensures that HTTP GET requests to `/person` are mapped to the `getPerson()` method.

> There are companion annotations for other HTTP verbs (e.g. `@PostMapping` for `POST`). There is also a `@RequestMapping` 
annotation that they all derive from, and can serve as a synonym (e.g. `@RequestMapping(method=GET)`).

`@RequestParam` binds the value of the query string parameter `name` into the `name` parameter of the `getPerson()` method. 
If the `name` parameter is absent in the request, the defaultValue of `Jon` is used.

As we annotated the class with `@RestController` we don't need to do any manual conversion to JSON. 
As `Jackson 2` is on the classpath, Spring will use `MappingJackson2HttpMessageConverter` as the default converter to
convert a Person object to JSON.

# Create an Application class

That's all regarding the REST service. Let's create a `SpringBootRestStartApplication.java` class to test our REST API.

{% highlight java %}
package com.jocamav.springbootreststart;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringBootRestStartApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootRestStartApplication.class, args);
    }

}

{% endhighlight %}

# Execute the application

Execute `mvn spring-boot:run`

As usual, you can build an executable jar and execute the application as follows:

```
mvn package && java -jar target/spring-boot-rest-start-0.0.1-SNAPSHOT.jar
```

To test it open in the browser the URL `http://localhost:8080/person` and you should see the response in the browser.

{% highlight json %}
{
  "id": 1,
  "name": "Jon",
  "familyName": "Snow"
}
{% endhighlight %}

We didn't add any parameter to our request, so the default value `Jon` is used as name. If we add some extra parameter to the request,
for example `http://localhost:8080/person?name=Jaime` the response should be something as the following one. 

{% highlight json %}
{
  "id": 2,
  "name": "Jaime",
  "familyName": "Snow"
}
{% endhighlight %}

That's all you need to create your first REST service! 

* Create a REST Controller
* Create a POJO class as a representation of your resource

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-rest-start)

```
git clone https://github.com/jocamav/tutorials.git
cd tutorials/spring-boot-rest-start 
mvn spring-boot:run
```


# References

[Building a RESTful Web Service][spring-guide]

[Spring MVC Reference Documentation][spring-doc]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/rest-service/
[spring-doc]: https://docs.spring.io/spring-framework/docs/5.3.3/reference/html/web.html#spring-web
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/index.html
[github-logo]: /assets/logos/github-mark-32.png
[spring-logo]: /assets/logos/spring.svg
