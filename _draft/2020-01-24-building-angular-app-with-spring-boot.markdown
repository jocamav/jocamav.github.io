---
layout: post
title:  "Getting Started with Angular"
date:   2020-01-13 19:00:00 +0100
categories: [angular]
---

# Introduction

Angular is a platform and framework for building client applications in HTML and TypeScript. Angular is written in TypeScript. 
It implements core and optional functionality as a set of TypeScript libraries that you import into your apps.

In Angular Website you can find an amazing documentation and example to understand better how the Framework is working. 
In this article we'll build a simple application just to have a quick overview of the different components used by Angular.
If you want to know more about it I totally recommend to follow the [Getting Started Tutorial](https://angular.io/tutorial) of Angular

# Technologies used

* npm 6.13.4
* node 12.13.1
* Angular CLI 8.3.21

# Create new project

Thanks to Angular CLI we can create a new project with a simple command. If you don't have Angular CLI installed you can 
install it executing next command:

```
npm install -g @angular/cli
```

Once we have Angular CLI installed we can create a new project with this command.

```
ng new angular-getting-started
```

The ng new command prompts you for information about features to include in the initial app. Accept the defaults by pressing 
the Enter or Return key.

After a few minutes the project is created under a new folder `angular-getting-started`.  Let's take a quick overview to the different 
files and folders created by Angular CLI.

* The application is generated in a new folder `angular-getting-started`.
* The skeleton app project is created in the `src` subfolder.
* An end-to-end test project (in the e2e subfolder).
* Related configuration files.

You can build and server your app locally as follows:

```
cd angular-getting-started
ng serve --open
```

The ng serve command launches the server, watches your files, and rebuilds the app as you make changes to those files.

The --open (or just -o) option automatically opens your browser to http://localhost:4200/.

You can see the default app created by Angular.

![Screenshot 01](/assets/20200113angular/screenshot01.png)


# Angular component

Components are the fundamental building blocks of Angular applications. 
They display data on the screen, listen for user input, and take action based on that input. The main component of an 
Angular application is the component `AppComponent`. The implementation of the AppComponent is distributed over three files:

* `app.component.ts` the component class code, written in TypeScript.
* `app.component.html` the component template, written in HTML.
* `app.component.css` the component's private CSS styles.

Let's start cleaning up the template to start showing only the title of the Application. Let's change the file `app.component.html`
to:

{% highlight html %}

<h1>{{ title }} app is running!</h1>

{% endhighlight %}

We should see only the title of the application displayed in the browser with no need of restarting.

![Screenshot 02](/assets/20200113angular/screenshot02.png)

The double curly braces are Angular's interpolation binding syntax. This interpolation binding presents the component's 
title property value inside the HTML header tag. But how can we change the title of the application? Let's take a look to `app.component.ts`:

{% highlight typescript %}

title = 'angular-getting-started';

{% endhighlight %}

We can change the title to any other.

{% highlight typescript %}

title = 'Cool Angular App';

{% endhighlight %}

The browser should refresh and show the new title.

![Screenshot 03](/assets/20200113angular/screenshot03.png)

Ok...it's not very fancy. But Angular allows as to define the styles for our component. Let's open the file `app.component.css`
and include these lines:

{% highlight css %}

h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}

{% endhighlight %}

Now it's looking a bit better with a neat blue color. 


![Screenshot 04](/assets/20200113angular/screenshot04.png)

# Spring project



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
