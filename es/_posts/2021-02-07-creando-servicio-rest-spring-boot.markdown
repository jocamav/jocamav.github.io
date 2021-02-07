---
layout: post
title:  "Creando un servicio REST con Spring Boot"
author:  "José Carlos Martínez"
lang: es
lang-ref: creating-rest-service-with-spring-boot
date:   2021-02-07 12:38:12 +0100
comments: true
categories: [spring-boot]
logo: spring
---

Aprende como crar un servicio REST con Spring Boot siguiendo estos sencillos pasos. Gracias a Spring, podrás crear tu primer servicio 
REST en un par de minutos. 

# Tecnologías utilizadas

* Spring Boot 2.4.1
* Maven 3

# Crear un nuevo proyecto

Lo primero que vas a necesitar para crear tu primer API REST es incluir Spring MVC en tu proyecto. Empecemos por crear
un proyecto Spring Boot incluyendo la dependencia `spring-boot-starter-web` en nuestro fichero `pom.xml`. 


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

# Crear Clase del recurso/representación

El primer paso es crear una clase POJO describiendo nuestro recurso. En este ejemplo vamos a crear una sencilla clase 
`Person` con estos atributos: 

* `id`: Valor Long que representará el identificador unívoco del objeto.
* `name`: Un String para el nombre de la persona.
* `familyName`: Un String para el apellido de la persona.

La clase `Person.java` es una clase POJO (Pain old Java object) que inclute todos los campos, y constructor y los métodos
get. 
> Por norma general es buena práctica definir todos los atributos como `final` y asignar los valores en el constructor.
>De esta manera nos aseguramos que el objecto será inmutable.

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

> En este case ejemplo, asignamos el apellido com `Snow`. No es un ejemplo muy práctico, la finalidad es simplemente 
>reducir el número de parámetros del consctructor.

Jackson es parte de `spring-boot-starter-json`, por lo que será configurado de manera automática por Spring. Cuando Jackson
está en el classpath, un bean `ObjectMapper` es configurado automáticamente. Esto es muy útil al no tener que configurar 
manualmente ningún converter adicional. Spring mapeará nuestra clase POJO como JSON automáticamente. 

# Crear un Controlaor

El siguiente paso es crear un Controlador REST. Un controlador (Controller en inglés) es un controlador de Spring que está
anotado con `@RestController`. `@RestController` es una anotación compuesta que anota la clase con `@Controller` y
 `@ResponseBody`. `@ResponseBody` indica que la respuesta ha de convertirse directamente (utilizando Jackson en nuestro caso)
 tells Spring the response should be converted directly (using Jackson in our case) 
**en vez de buscar una vista para renderizar el resultado utilizando una plantilla HTML**.


![Screenshot 01](/assets/img/20210131springbootrest/screenshot01.png)

> Anotar una clase con `@RestController` tiene el mismo efecto que incluir ambas anotaciones `@Controller` `@ResponseBody`
>en la clase.

Creemos una clase `PersonController.java` como la siguiente. 

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

Además de `@RestController` hemos incluído la anotación `@GetMapping` al método `getPerson()`. La anotación `@GetMapping` 
asegura que todas las peticiones HTTP GET a `/person` serán mapeadas al método `getPerson()`.

> En Spring podemos encontrar otras anotaciones referente a otros verbos HTTP (por ejemplo `@PostMapping` para `POST`). 
>También disponemos de la anotación `@RequestMapping` de la que todas estas anotaciones derivan y que se puede utilizar
>igualmente (por ejemplo `@RequestMapping(method=GET)`).

`@RequestParam` enlaza el parámetro `name` de la petición HTTP al parámetro `name` del método `getPerson()`. En el caso 
de que no haya ningún parámetro `name` en la petición, se utilizará el valor por defecto `Jon`.

Como hemos anotado la clase con `@RestController` no necesitamos realizar ninguna conversión manual a JSON. Como hemos 
indicado antes, al estar `Jackson 2` presente en classpath, Spring utilizará `MappingJackson2HttpMessageConverter` 
cono el convertidor por defecto para convertir el objecto Person a JSON.

# Crear una clase Application

Eso es todo en lo que respecta a un servicio REST. Creemos ahora una clase `SpringBootRestStartApplication.java` 
para probar nuestro API REST.

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

# Ejecutar la aplicación

Ejecuta `mvn spring-boot:run`

Como es habitual, puedes crear un fichero JAR con la aplicación y ejecutar el siguiente comando:

```
mvn package && java -jar target/spring-boot-rest-start-0.0.1-SNAPSHOT.jar
```

Para probar la aplicación abre la URL `http://localhost:8080/person` en el navegador, deberías de ver una respuesta como la siguiente.

{% highlight json %}
{
  "id": 1,
  "name": "Jon",
  "familyName": "Snow"
}
{% endhighlight %}

Como no hemos añadido ningún parámetro a nuestra petición, de utiliza el valor por defecto `Jon` como nombre. Si añadimos
un parámetro adicional a la petición, por ejemplo`http://localhost:8080/person?name=Jaime` la respuesta debería de ser similar
a la siguinte.

{% highlight json %}
{
  "id": 2,
  "name": "Jaime",
  "familyName": "Snow"
}
{% endhighlight %}

¡Y eso es todo lo que necesitar para crear un servicio REST! 

* Crear un Controlador REST
* Crear una clase POJO como representación de tu recurso

# Código fuente

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-rest-start)

```
git clone https://github.com/jocamav/tutorials.git
cd tutorials/spring-boot-rest-start 
mvn spring-boot:run
```


# Referencias

[Building a RESTful Web Service][spring-guide]

[Spring MVC Reference Documentation][spring-doc]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/rest-service/
[spring-doc]: https://docs.spring.io/spring-framework/docs/5.3.3/reference/html/web.html#spring-web
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/index.html
[github-logo]: /assets/logos/github-mark-32.png
[spring-logo]: /assets/logos/spring.svg
