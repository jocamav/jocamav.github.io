---
layout: post
title:  "Package a Spring Boot and Angular Application together"
date:   2020-02-04 19:00:00 +0100
categories: [angular, spring-boot]
---

# Introduction

Developing a Web Application using Angular for the Front-End and Spring Boot for the Back-End can become 
a challenge when we are planing to deploy the Application.

In this article we'll see how to build both application together and package it as a single JAR file.

# Technologies used

* npm 6.13.4
* node 12.13.1
* Angular CLI 8.3.21

# Create a Multi Module Maven Project

Let's create a Multi Module Maven project with two modules (one for the Back-End and another for the Front-End):

* spring-boot-module
* angular-module

The main `pom.xml` file should look as this one.

{% highlight xml %}

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jocamav</groupId>
    <artifactId>spring-boot-angular</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>spring-boot-module</module>
        <module>angular-module</module>
    </modules>

</project>

{% endhighlight %}

# Create Angular module

Thanks to Angular CLI we can create a new project/module with a simple command. We execute next command

```
ng new angular-module
```

This will create a new folder `angular-module` inside our main project with the sample Angular Application inside. We can
test the application running these commands.


```
cd angular-module
ng serve --open
```

We should see the default app created by Angular if we open in the browser the URL `http://localhost:4200/`.

![Screenshot 01](/assets/20200204angularboot/screenshot01.png)

Right now the Angular application is running in the server launched by Angular CLI (which it's running in the port 4200). 

# Create Spring Boot Module

We can use [Spring Initializr][spring-initializr] to create our Spring Boot application. 

* As name we select `spring-boot-module`
* We include `Spring Web` as dependency

This will create a `spring-boot-module.zip` which we should unzip inside our project. The `pom.xml` file of the new module 
should look similar to:


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
	<artifactId>spring-boot-module</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>spring-boot-module</name>
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
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
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

Let's try to run the Spring Boot executing these commands.

```
cd spring-boot-module
mvn clean install
mvn spring-boot:run
```

We see the server started on port 8080.

```
Tomcat started on port(s): 8080 (http) with context path ''
```

But let's see what happens if we open `http://localhost:8080/` in the browser.

![Screenshot 02](/assets/20200204angularboot/screenshot02.png)

We are getting a 404 Not Found error because in Spring Boot we don't have any page or Controller created. We can create 
a `index.html` page under `src/main/resources/static/index.html` or also copy all the generated files of Angular under this
folder. For doing it we could:
1. Execute `ng build` in Angular module. This will create the static files
2. Copy these files in `src/main/resources/static/` folder.

This can become very cumbersome if we have to do it each time we change anything in our Angular Application.
Instead doing it manually let's see how we can put everything together using some Maven Plugins.

# Build Angular Application

For compile the Angular Application we'll use [Frontend Maven Plugin][frontend-maven-plugin]. This plugin
install Node and NPM locally for our project, runs `npm install` and any other combination.

Let's create a `pom.xml` file in our Angular project with this content.

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <artifactId>angular-module</artifactId>

  <parent>
    <groupId>com.jocamav</groupId>
    <artifactId>spring-boot-angular</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>


  <build>
    <plugins>

      <plugin>
        <groupId>com.github.eirslett</groupId>
        <artifactId>frontend-maven-plugin</artifactId>
        <version>1.8.0</version>
        <configuration>
          <nodeVersion>v12.13.1</nodeVersion>
          <npmVersion>6.13.4</npmVersion>
          <workingDirectory>src/main/web/</workingDirectory>
        </configuration>
        <executions>
          <execution>
            <id>install node and npm</id>
            <goals>
              <goal>install-node-and-npm</goal>
            </goals>
          </execution>
          <execution>
            <id>npm install</id>
            <goals>
              <goal>npm</goal>
            </goals>
          </execution>
          <execution>
            <id>npm run build</id>
            <goals>
              <goal>npm</goal>
            </goals>
            <configuration>
              <arguments>run build</arguments>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

</project>


{% endhighlight %}

This will:

* Install `node` and `npm` in our project (we declared the versions to use for each one)
* Run `npm install`
* Run `npm build`

By default Angular will build the application under `dist` folder.

# Copy the resources into Spring Boot application

To copy the generated files into Spring Boot we'll use [Apache Maven Resources Plugin][maven-resources-plugin]. 
The Resources Plugin handles the copying of project resources to the output directory. 

Let's include the plugin to the Spring Boot project:

{% highlight xml %}

    <plugin>
        <artifactId>maven-resources-plugin</artifactId>
        <executions>
            <execution>
                <id>copy-resources</id>
                <phase>validate</phase>
                <goals>
                    <goal>copy-resources</goal>
                </goals>
                <configuration>
                    <outputDirectory>${project.build.directory}/classes/resources/</outputDirectory>
                    <resources>
                        <resource>
                            <directory>../angular-module/dist/angular-module/</directory>
                        </resource>
                    </resources>
                </configuration>
            </execution>
        </executions>
    </plugin>

{% endhighlight %}

Let's take a look to the configuration of the plugin:
* `outputDirectory` indicate the target directory to copy the resources. In our case we want to copy 
under `${project.build.directory}/classes/resources/`
* `resources` the resources to copy. In this case we'll copy all the files of the folder `angular-module/dist/angular-module/`

# Run the Application

To install the Application we execute

```
mvn clean install
```

When the command is complete we can see Angular files have been copied under `resources` folder.


![Screenshot 03](/assets/20200204angularboot/screenshot03.png)

And if we run `mvn spring-boot:run -pl spring-boot-module` and open `http://localhost:8080/` in the browser
we can see the Angular Application running with Spring Boor.  

> The `-pl` or `--projects` option allows you to select a list of projects from a multimodule project.  

![Screenshot 04](/assets/20200204angularboot/screenshot04.png)

Also we have created a JAR which can be deployed in a server. We can run the application executing.

```
java -jar spring-boot-module\target\spring-boot-module-0.0.1-SNAPSHOT.jar
```

# Summary 

In this article we saw:

* How to create a Multi Module Maven project
* How to use frontend-maven-plugin to compile the Angular Application
* How to use maven-resources-plugin to copy the static files into our Spring Application
* Create a JAR artifact which can be deployed in any Web Server.


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

[Creating a Multi Module Project][spring-multi-module]

[Spring Boot Reference Documentation][spring-boot-doc]

[spring-initializr]: https://start.spring.io/
[spring-multi-module]: https://spring.io/guides/gs/multi-module/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.2.2.RELEASE/reference/html/
[frontend-maven-plugin]: https://github.com/eirslett/frontend-maven-plugin
[maven-resources-plugin]: http://maven.apache.org/plugins/maven-resources-plugin/
[github-logo]: /assets/logos/github-mark-32.png
