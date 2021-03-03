---
layout: post
title:  "HTTP verbs for a REST service with Spring Boot"
author:  "José Carlos Martínez"
lang: en
lang-ref: http-verbs-rest-service-with-spring-boot
date:   2021-03-02 14:38:12 +0100
comments: true
categories: [spring-boot]
logo: spring
---

Learn how to create a complete RESTful service using GET, POST, PUT and DELETE HTTP verbs. Follow the best and most common
practices to create a complete REST service to load, create, update and delete resources. 


![Screenshot 01](/assets/img/20210302springresthttp/screenshot01.png)

# Technologies used

* Spring Boot 2.4.1
* Maven 3

# What are we going to build? 

There's plenty good documentation and books about how to implement a good RESTful service. We are going to try to keep it 
simple and short. A good RESTful service should provide access to a resource following some good practices. For this example
we are going to implement REST API to manage `Persons`. Our goal is to build a REST API which allows us:

* See the list of persons
* See the details of one person
* Create new persons
* Update persons
* Delete persons

## URI design

Our resource is a `Person`. The most suitable URI could be `/persons`. Let's take a look to the semantics of the HTTP verbs:

* `GET`: Get a representation of the target resource’s state.
* `POST`: Let the target resource process the representation enclosed in the request.
* `PUT`: Set the target resource’s state to the state defined by the representation enclosed in the request.
* `DELETE`: Delete the target resource’s state.

How this should be reflected in our REST API? 

* `GET` to `/persons` should fetch the list of persons.
* `GET` to `/persons/{id}` should fetch the details of the person with ID `id`.
* `POST` to `/persons` should create a new person.
* `PUT` to `/persons/{id}` should update the details of the person with ID `id`.
* `DELETE` to `/persons/{id}` should delete the person with ID `id`.

## Person resource

Our person will contain these attributes:

* `id`: A Long value as unique identifier.
* `name`: A String for the name of the person.
* `familyName`: A String for the family name of the person.

# Maven Installation

As usual you need to add the dependency `spring-boot-starter-web`in our `pom.xml` to implement REST controllers.

{% highlight xml %}
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    
{% endhighlight %}


# Create the representation POJO class

First step is to create the POJO class with all the attributes as a representation of our resource. Let's start creating 
a `Person.java` class as follows:


{% highlight java %}
package com.jocamav.springbootrest.http;

public class Person {
package com.jocamav.springbootrest.http;

public class Person {

    private Long id;
    private String name;
    private String familyName;

    public Person() {
    }

    public Person(Long id, String name, String familyName) {
        this.id = id;
        this.name = name;
        this.familyName = familyName;
    }

    public Person(String name, String familyName) {
        this.id = null;
        this.name = name;
        this.familyName = familyName;
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

    public void setName(String name) {
        this.name = name;
    }

    public void setFamilyName(String familyName) {
        this.familyName = familyName;
    }
}
{% endhighlight %}

# Create Service

It's a good practice to keep the business logic in a Service instead the Controller. Let's create an interface `PersonService`.

This service should provice operations to:

* Get list of persons
* Get a single person
* Create persons
* Update person
* Delete person

{% highlight java %}
package com.jocamav.springbootrest.http;

import java.util.List;

public interface PersonService {
    List<Person> getList();
    Person get(Long id);
    Person save(Person person);
    Person update(Long id, Person person);
    void delete(Long id);
}
{% endhighlight %}

Now let's create a service implementing this interface. In a real world application you should use a database to persist all
the changes. As this is an example to understand the concepts of REST and the HTTP verbs we are going to use a HashMap to 
store the users just to keep it as simple as possible. Create a `PersonServiceDefault.java` class implementing all the 
method of the interface:

 
{% highlight java %}
package com.jocamav.springbootrest.http;

import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.List;
import java.util.concurrent.atomic.AtomicLong;
import java.util.stream.Collectors;

@Service
public class PersonServiceDefault implements PersonService {

    private final AtomicLong idSequence = new AtomicLong();

    private HashMap<Long, Person> personStorage;

    public PersonServiceDefault() {
        this.personStorage = new HashMap<>();
    }

    @Override
    public List<Person> getList() {
        return personStorage.values()
                .stream()
                .collect(Collectors.toList());
    }

    @Override
    public Person get(Long id) {
        return personStorage.get(id);
    }

    @Override
    public Person save(Person person) {
        Long id = idSequence.incrementAndGet();
        Person personToSave = new Person(id, person.getName(), person.getFamilyName());
        personStorage.put(id, personToSave);
        return personToSave;
    }

    @Override
    public Person update(Long id, Person person) {
        Person personToUpdate = get(id);
        personToUpdate.setName(person.getName());
        personToUpdate.setFamilyName(person.getFamilyName());
        return personStorage.put(id, personToUpdate);
    }

    @Override
    public void delete(Long id) {
        personStorage.remove(id);
    }
}

{% endhighlight %}

> We add the annotation `@Service` to declare the class as a Service component. We could use `@Component` too, but for the 
> Service layer better to use `@Service` because this annotation can also carry additional semantics in future releases of the Spring Framework.

# Add Spring Boot application file

We are going to add to the Spring Boot Application file some code to create some users on startup.

{% highlight java %}
    package com.jocamav.springbootrest.http;
    
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.ConfigurableApplicationContext;
    
    @SpringBootApplication
    public class SpringBootRestHttpMethodsApplication {
    
        public static void main(String[] args) {
            ConfigurableApplicationContext ctx = SpringApplication.run(SpringBootRestHttpMethodsApplication.class, args);
            PersonService personService = ctx.getBean(PersonService.class);
            personService.save(new Person("Jon", "Snow"));
            personService.save(new Person("Sansa", "Stark"));
            personService.save(new Person("Jaime", "Lannister"));
        }
    
    }

{% endhighlight %}

> This is not the best way to do it for a Production Environment. But it will help us to have some data to play with. 

# Create Controller

Let's create our REST Controller class. Create a class `PersonController.java` annotated with `@RestController`:

{% highlight java %}
    @RestController
    @RequestMapping("/api/persons")
    public class PersonController 
{% endhighlight %}

The annotation `@RestController` will declare the Controller as a REST Controller. Notice we added the annotation 
`@RequestMapping("/api/persons")`. This will make all the requests to `/api/persons` will be mapped to this controller.
This way we don't need to add the same URI to all the methods of the Controller. 

We are going to use `PersonService` to perform all the operations. To inject this dependency we should add this line
to the Controller class. 

{% highlight java %}
    @Autowired
    private PersonService personService;
{% endhighlight %}

## Get list of persons

Let's add a method to get the list of Persons. We want the GET requests to be mapped to `/api/persons`, so we could add
this method:

{% highlight java %}
    @RequestMapping
    public List<Person> getList() {
        return personService.getList();
    }
{% endhighlight %}

Let's start our application and test it.

```
mvn spring-boot:run
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/api/persons  
```

We see the response in the console:

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 16 Feb 2021 09:04:10 GMT

[
    {"id":1,"name":"Jon","familyName":"Snow"},
    {"id":2,"name":"Sansa","familyName":"Stark"},
    {"id":3,"name":"Jaime","familyName":"Lannister"}
]
```

Let's take a look to the response:
* We are getting a `200` HTTP response which is correct for GET requests.
* **Content-Type** is `application/json`
* The response is a JSON with a list of persons.

Everything looks fine! Isn't it? Not that fast...there's an issue implementing the service this way. 
Let's try a `POST` request to understand what it's going on:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST http://localhost:8080/api/persons  
```

We get the same response! 

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 16 Feb 2021 09:07:19 GMT

[
    {"id":1,"name":"Jon","familyName":"Snow"},
    {"id":2,"name":"Sansa","familyName":"Stark"},
    {"id":3,"name":"Jaime","familyName":"Lannister"}
]
```

From a semantic point of view this is not correct. A `POST` request should be used to created new resources not for fetching data.
`@RequestMapping` by default matches to all HTTP methods. We can use the parameter `method` to match the request only to 
`GET` requests as follows: 

{% highlight java %}
    @RequestMapping(method = RequestMethod.GET)
    public List<Person> getList() {
        return personService.getList();
    }
{% endhighlight %}

Let's try again both requests.  The `GET` request is still working as expected. But now we are getting a different response
with `POST` request:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST http://localhost:8080/api/persons 

HTTP/1.1 405 
Allow: GET
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 16 Feb 2021 09:16:12 GMT

{
    "timestamp":"2021-02-16T09:16:12.424+00:00",
    "status":405,
    "error":"Method Not Allowed",
    "message":"",
    "path":"/api/persons"
}
```

* We are getting an error `405` because the method `POST` is not allowed.
* Also we get a `JSON` response generated by Spring with some extra information as a timestamp, status, error, message and path. 

There are also HTTP method specific shortcut variants of `@RequestMapping` depending on the HTTP method we want to map:

| Shorcut annotation        | `@RequestMapping` equivalent |
| ------------- |-------------|
| `@GetMapping`    | `@RequestMapping(method = RequestMethod.GET)` | 
| `@PostMapping`   | `@RequestMapping(method = RequestMethod.POST)`      |
| `@PutMapping`    | `@RequestMapping(method = RequestMethod.PUT)`     |
| `@DeleteMapping` | `@RequestMapping(method = RequestMethod.DELETE)`   |
| `@PatchMapping`  | `@RequestMapping(method = RequestMethod.PATCH)` |

So let's change the current method to:

{% highlight java %}
    @GetMapping
    public List<Person> getList() {
        return personService.getList();
    }
{% endhighlight %}

We can run the project and see the request to get the list of persons is still working as expected.

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/api/persons  
```

And the response:

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Tue, 16 Feb 2021 09:04:10 GMT

[
    {"id":1,"name":"Jon","familyName":"Snow"},
    {"id":2,"name":"Sansa","familyName":"Stark"},
    {"id":3,"name":"Jaime","familyName":"Lannister"}
]
```

## Get single person

To get a specific person by ID we should provide a URL as `/api/persons/{id}` where `id` is the ID of the person to fetch. 
For example, if we want to fetch the details of the person with ID 1 we should send a `GET` request to `/api/persons/1`. 
Let's add this method.

{% highlight java %}
    @GetMapping("/{id}")
    public Person get(@PathVariable Long id) {
        return personService.get(id);
    }
{% endhighlight %}

We included a new annotation `@PathVariable` which allows us to include URI variables. URI variables are automatically 
converted to the appropriate type, or TypeMismatchException is raised. Simple types (int, long, Date, and so on) are supported 
by default and you can register support for any other data type. The URI variable `id` will be assigned to the parameter `id`.

Let's test it.

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/api/persons/1  
```

And the response:

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 12:52:09 GMT

{
  "id":1,
  "name":"Jon",
  "familyName":"Snow"
}
```

## Create a person

To create a person we should use a `POST` request to `/api/persons/`. In the body of the request we should send the
information of the new person as a JSON. Let's add a new method as follows.

{% highlight java %}
    @PostMapping
    public Person save(@RequestBody Person person) {
        return personService.save(person);
    }
{% endhighlight %}

As we indicated before we can use the annotation `@PostMapping` as a shortcut for `POST` requests. Now we are using a new 
annotation `@RequestBody` to have the request body read and deserialized into an `Object` through an `HttpMessageConverter`.
By default `MappingJackson2HttpMessageConverter` is used to convert the JSON into a `Person` object.

Let's test it.

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST -d '{"name":"Rob","familyName":"Stark"}' http://localhost:8080/api/persons 
```

And the response:

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 13:21:21 GMT

{
  "id":4,
  "name":"Rob",
  "familyName":"Stark"
}
```

The new Person has been created! But if we take a closer look to the response we see we are getting a response `200`. When 
writing a RESTful service and a new resource is created the most appropriate response it's a `201` which means `Created`.
To return a `201` code we can use the annotation `@ResponseStatus(HttpStatus.CREATED)` to indicate Spring the method should 
return a `CREATED` status.

{% highlight java %}
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Person save(@RequestBody Person person) {
        return personService.save(person);
    }
{% endhighlight %}

If we restart and try again we get a `201` response as expected. 

```
HTTP/1.1 201 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 13:26:26 GMT

{
  "id":4,
  "name":"Rob",
  "familyName":"Stark"
}
```

## Update a person

To update a person we should use a `PUT` request to `/api/persons/{id}`. The ID should be part of the URI and the 
content to update. As you can imagine we'll need to use these annotations:

* `@PutMapping` to map the request to `PUT`.
* `@PathVariable` to use an URI parameter. 
* `@RequestBody` to convert the body to a Person object.

In this case a response `200` is OK. The method should look as the following one:

{% highlight java %}
    @PutMapping("/{id}")
    public Person update(@PathVariable Long id, @RequestBody Person person) {
        return personService.update(id, person);
    }
{% endhighlight %}

We can try to update a Person as follows:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X PUT -d '{"name":"Jon","familyName":"Stark"}' http://localhost:8080/api/persons/1 
```

We get the response:

```
HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 13:32:59 GMT

{
  "id":1,
  "name":"Jon",
  "familyName":"Stark"
}
```

If we get the person we can verify the record was updated:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/api/persons/1 

HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 13:33:07 GMT

{
  "id":1,
  "name":"Jon",
  "familyName":"Stark"
}
```

## Delete a person

To delete a person we should use a `DELETE` request to `/api/persons/{id}`. For a delete request the normal scenario is to return an
empty body with a status `204` which means No Content. 

{% highlight java %}
    @DeleteMapping("/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void delete(@PathVariable Long id) {
        personService.delete(id);
    }
{% endhighlight %}

Let's try to delete a Person:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X DELETE http://localhost:8080/api/persons/3
```

We get a response with a `204` HTTP code:

```
HTTP/1.1 204 
Date: Wed, 03 Mar 2021 13:38:33 GMT

```

Let's fetch the list to verify the Person with ID 3 is not in the list anymore:

```
curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X GET http://localhost:8080/api/persons/

HTTP/1.1 200 
Content-Type: application/json
Transfer-Encoding: chunked
Date: Wed, 03 Mar 2021 13:38:55 GMT

[
  {"id":1,"name":"Jon","familyName":"Snow"},
  {"id":2,"name":"Sansa","familyName":"Stark"}
]
```

# Summary 

In this article we saw:

* How to create a RESTful Controller
* Inject a Service to the Controller
* Use the different mapping annotation to get a list, get a simple record, create a record, update values and delete records.
* Use the appropriate HTTP response for each operation.

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-rest-http-methods)

```
git clone https://github.com/jocamav/tutorials.git
cd spring-boot-rest-http-methods  
mvn spring-boot:run
```

# References

[Spring MVC Reference Documentation][spring-doc]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-doc]: https://docs.spring.io/spring-framework/docs/5.3.3/reference/html/web.html#spring-web
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/index.html
[github-logo]: /assets/logos/github-mark-32.png
[spring-logo]: /assets/logos/spring.svg
