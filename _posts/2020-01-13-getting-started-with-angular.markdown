---
layout: post
title:  "Getting Started with Angular"
date:   2020-01-13 19:00:00 +0100
categories: [angular]
---

# Introduction

Angular is a platform and framework for building client applications in HTML and TypeScript. Angular is written in TypeScript. It implements core and optional functionality as a set of TypeScript libraries that you import into your apps.

In Angular Website you can find an amazing documentation and example to understand better how the Framework is working. In this article we'll build a simple application just to have a quick overview of the different components used by Angular.

# Technologies used

* npm 6.13.4
* node 12.13.1
* Angular CLI 8.3.21

# Create new project

Thanks to Angular CLI we can create a new project with a simple command. If you don't have Angular CLI installed you can install it executing next command:

```
npm install -g @angular/cli
```

Once we have Angular CLI installed we can create a new project with this command.

```
ng new my-app
```

The ng new command prompts you for information about features to include in the initial app. Accept the defaults by pressing the Enter or Return key.

After a few minutes the project is created under a new folder `my-app`. To run the project we need to execute next commands:

```
cd my-app
ng serve --open
```

![Screenshot 01](/assets/20200113angular/screenshot01.png)


{% highlight xml %}

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.2.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

{% endhighlight %}

Spring Boot also provides an optional Maven plugin to create executable jars.


# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-angular)

```
git clone https://github.com/jocamav/tutorials.git
cd spring-boot-angular
mvn clean install
mvn spring-boot:run -pl spring-boot-module
```


# References

[Spring Boot Getting Started][spring-guide]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/spring-boot/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/
[github-logo]: /assets/logos/github-mark-32.png
