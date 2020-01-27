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


Thanks to Angular CLI we can create a new project, generate code, perform tasks such as testing, bundling and deployment. 
All of it with a simple command. If you don't have Angular CLI installed you can 
install it globally executing next command in a terminal/console window:

```
npm install -g @angular/cli
```

Once we have Angular CLI installed we can create a new project with `ng new` command.

```
ng new angular-getting-started
```

The `ng new `command prompts you for information about features to include in the initial app. Accept the defaults by pressing 
the Enter or Return key.

After a few minutes the project is created under a new folder `angular-getting-started`.  Let's take a quick overview to the different 
files and folders created by Angular CLI.

* The application is generated in a new folder `angular-getting-started`.
* The skeleton app project is created in the `src` subfolder.
* An end-to-end test project (in the `e2e` subfolder).
* Related configuration files.

You can build and serve your app locally as follows:

```
cd angular-getting-started
ng serve --open
```

The ng serve command launches the server, watches your files, and rebuilds the app as you make changes to those files.

The `--open` (or just -o) option automatically opens your browser to `http://localhost:4200/`.

You can see the default app created by Angular.

![Screenshot 01](/assets/20200113angular/screenshot01.png)


# Angular component

Components are the fundamental building blocks of Angular applications. 
They display data on the screen, listen for user input, and take action based on that input. The main component of an 
Angular application is the component `AppComponent`. The implementation of the `AppComponent`(as any other component) 
is distributed over three files:

* `app.component.ts` the component class code, written in **TypeScript**.
* `app.component.html` the component template, written in **HTML**.
* `app.component.css` the component's private **CSS** styles.

Let's start cleaning up the template to start showing only the title of the Application. Let's change the file `app.component.html`
to:

{% highlight html %}

<h1>{{ title }} app is running!</h1>

{% endhighlight %}

Once we save it the changes should be displayed with no need of restarting the server. We should see only the title of 
the application displayed in the browser now.

![Screenshot 02](/assets/20200113angular/screenshot02.png)

The double curly braces are Angular's **interpolation binding syntax**. This interpolation binding presents the component's 
title property value inside the HTML header tag. But how can we change the title of the application? Let's take a look to `app.component.ts`:

{% highlight typescript %}

title = 'angular-getting-started';

{% endhighlight %}

There's an attribute inside the component called `title`. If we can to change the title to any other we just need to change
the value of the attribute.

{% highlight typescript %}

title = 'Cool Angular App';

{% endhighlight %}

The browser should refresh and show the new title.

![Screenshot 03](/assets/20200113angular/screenshot03.png)

Ok...it's not very fancy. But Angular allows as to define the styles for the components. Let's open `app.component.css`
file and include these lines:

{% highlight css %}

h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}

{% endhighlight %}

Now it looks a bit better with a neat blue color. 


![Screenshot 04](/assets/20200113angular/screenshot04.png)

# Creating a new component

With Angular Cli is very easy to create a new component and add nested components inside other components. 
To generate a new component we can use Angular CLI with `ng gnerate` command. To Create a new `StudentList` component we
can execute:

```
ng generate component student-list
```

This will generate automatically all the files of the component.

``` 
CREATE src/app/student-list/student-list.component.html (27 bytes)
CREATE src/app/student-list/student-list.component.spec.ts (664 bytes)
CREATE src/app/student-list/student-list.component.ts (292 bytes)
CREATE src/app/student-list/student-list.component.css (0 bytes)
UPDATE src/app/app.module.ts (418 bytes)
```

The three main files of the component are created automatically:
* A **HTML** file for the component template.
* A **TypeScript** class to declare the component.
* A **CSS** style file.

> We can see another file of type `spec.ts` has been generated. Files `spect.ts` are the files which we should use to 
write the tests in Angular. By default these file are generated in the same folder than the component. 

Let's take a look to the class `StudentListComponent` declared in `student-list.component.ts`. 

```typescript
@Component({
  selector: 'app-student-list',
  templateUrl: './student-list.component.html',
  styleUrls: ['./student-list.component.css']
})
```

We can see `templateUrl` and `styleUrls` refer to the other files. The `selector` attribute is the name of the selector we 
should use if we want to add our new component to other components. For example to add this new component to `AppComponent`
we add `<app-student-list></app-student-list>` to the template of `AppComponent`.

```html
<h1>{{ title }} app is running!</h1>
<app-student-list></app-student-list>
```

We should see some message like `student-list works!` in the main page.

![Screenshot 05](/assets/20200113angular/screenshot05.png)

Let's modify the template of the component to show something more interesting. Let's add a list of students for example.

```html
<ul>
  <li>Harry Potter</li>
  <li>Ron Weasley</li>
  <li>Hermione Granger</li>
  <li>Draco Malfoy</li>
  <li>Cedric Diggory</li>
</ul>
```

Now we should see the list displayed in the browser.

![Screenshot 06](/assets/20200113angular/screenshot06.png)

# Using Directives

Actually this is not very useful just to display a list. It would be much better if we could define the 
students as a variable in the component and show the list depending on the content of this variable. We can add to 
`student-list.component.ts` an array of students with the names of the students.

```typescript
  students = [
    'Harry Potter',
    'Ron Weasley',
    'Hermione Granger',
    'Draco Malfoy',
    'Cedric Diggory'
  ];
```

> Usually this information should come from the server. But for the example we can mock the data directly in the component.

No we need to change the template to bind the `li` elements to the array. We could do something like:

```html
<ul>
  <li>{{ students }}</li>
</ul>
``` 

But now all the items are shown as one item of the list. Probably this is not the expected behaviour.

![Screenshot 07](/assets/20200113angular/screenshot07.png)

To fix this issue  we can use one of the most common Angular directive. With `ngFor` we can iterate through the list of 
students and add one `li` element per item.

```html
<ul>
  <li *ngFor="let student of students">{{ student }}</li>
</ul>

``` 

> Don't forget the asterisk (*) in front of ngFor. It's a critical part of the syntax.

# Summary 

In this article we saw:

* How to install Angular CLI.
* How to create a new Angular project.
* Understand the structure of an Angular project.
* How to add and create new Components.
* Understand how to bind data from the component to the template.
* Use Angular directives as `ngFor`.

There are so much more to know about Angular: Modules, Components, Services, Forms, Routing, Testing...in 
the [Official Documentation][angular-doc] you can find everything you need. probably the best starter point is to complete
the [Angular Tour of Heroes Tutorial][angular-tutorial].

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-angular)

```
git clone https://github.com/jocamav/tutorials.git
cd angular-getting-started
npm install
ng serve -o
```

# References

[Angular Setup][angular-setup]

[Angular Tour of Heroes Tutorial][angular-tutorial]


[angular-doc]: https://angular.io/docs
[angular-setup]: https://angular.io/guide/setup-local
[angular-tutorial]: https://angular.io/tutorial
