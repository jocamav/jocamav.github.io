---
layout: post
title:  Creando una aplicación con Spring Boot
author:  "José Carlos Martínez"
lang: es
lang-ref: building-an-application-with-spring-boot
date:   2021-01-15 14:38:12 +0100
comments: true
categories: [spring-boot]
---

# Introducción
Spring Boot facilita mucho el trabajo para crear una aplicación basada en Spring de una manera muy sencilla sin apenas configuración.
Esta es la introducción en la documentación oficial de Spring Boot:

>Spring Boot hace fácil crear aplicaciones stand-alone listas para ejecutar en Producción. Tomamos una visión obstinada de la plataforma Spring 
y las bibliotecas de terceros para que puedas empezar a trabajar sin muchos problemas. La mayoría de las aplicaciones en Spring Boot
necesitan muy poca configuración.

# Tecnologías usadas

* Spring Boot 2.4.1
* Maven 3

# Instalación con Maven

Spring Boot es complatible con Apache Maven 3.3 or versiones superiores.

Las dependencias de Spring Boot utilizan el groupId `org.springframework.boot`. Tradicionalmente, tu fichero POM de maven 
hereda de el proyecto `spring-boot-starter-parent` y declara las dependencias necesarias de uno o más [Starters](https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/using-spring-boot.html#using-boot-starter). 
Así que lo primero es incluir la dependencia `spring-boot-starter-parent` a nuestro proyecto.

{% highlight xml %}

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

{% endhighlight %}

Spring Boot también ofrece un plugin opcional de Maven para crear jars ejecutables.

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

Este plugin ofrece funcionalidades muy interesantes:

* Recopila todos los ficheros jars del classpath y los recopila en un único fichero `jar` ejecutable. Esto hace que tu servicio sea sencillo de exportar y ejectuar.
* Busca el método `public static void main()` para establecer la clase como ejecutable.
* Resuelve las dependencias para establecer el número de versión correcta que se coresponda con las dependencias de Spring Boot. Puedes sobreescribir cualquiera de estas dependencias, pero deberás de tener cuidado con las versiones por defecto.

Para este ejemplo vamos a implementar un controlador REST muy básico. Para ello, necesitamos incluir un `Starter` adicional
para desarrollas aplicaciones RESTful basadas en Spring MVC..

{% highlight xml %}

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    
{% endhighlight %}

> Spring Boot utilza Tomcat como contentedor embebido por defecto

El fichero `pom.xml` completo deberá de ser similar a este.

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

# Crear un Controlador

Podemos crear una clase `HelloController.java` para devolver un simple mensaje `"Greetings from Spring Boot!"`:

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

Echemos un vistazo más detenidamente a todas las anotaciones que hemos incluído en este Controlador:

* La clase está anotada `@RestController`. Esto significa que la clase está lista para ser utilizada por Spring MVC para recibir peticiones web.
* `@RequestMapping` mapea peticiones a `/` al método `index()`

Cuando se invoca a la URL desde el navegador o mediante curl desde la línea de comandos, el método devuelve texto puro. Esto
es debido a que `@RestController` combina `@Controller` y `@ResponseBody`, dos anotaciones que combinadas hacen que
las peticiones web devuelvan datos serializados en vez de una vista..

# Crear una clase Aplicación

Primero vamos a crear una clase `SpringBootStartApplication.java`

Para nuestra aplicación, nuestra meta es crear una clase `SpringBootStartApplication.java` con un método `main`. 
Esto es simplemente el modo estándar acorde a la convención de Java como "punto de entrada" para una aplicación. 


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

Se recomienda poner la principal clase de la aplicación en el directorio raíz por encima del resto de las clases. La anotación 
`@SpringBootApplication` se incluye habitualmente en la clase principal y añade de manera implicita::

* `@Configuration`: Marca la clase como fuente de declaración de beans para el contexto de Spring.
* `@EnableAutoConfiguration`: Indica a Spring Boot que debe añadir beans según la configuración del classpath settings, otros beans y distintas propiedades y configuraciones. 
Por ejemplo, si spring-webmvc esta en el classpath, esta anotación marca la aplicación como una aplicación web y activa ciertos aspectos clave,
 como la configuración de un DispatcherServlet.
* `@ComponentScan`: Indica a Spring que debe buscar otros componentes, clases de configuración u servicios en el paquete `com.jocamav`, 
de manera que puede encontrar los distintos controladores que implementemos.

Nuestro método `main` delega a la clase `SpringApplication`de Spring Boot ejecutando el método `run` para iniciar la aplicación. 
SpringApplication se encarga del resto, arrancando Spring, quien a su vez inicia un servidor Tomcat ya configurao. 
necesitamos pasar la clase (`SpringBootStartingApplication.class` en nuestro ejemplo) como argumento al método `run` 
para indicar a `SpringApplication` cual es el componente principal de Spring..

Te has dado cuenta que no hay ningún fichero XML? Tampoco el clásico fichero `web.xml`. Esta aplicación **es 100% puro 
Java** sin necesidad de configurar practicamente nada ni ninguna infraestructura.


# Ejecutar la aplicación

Ejecuta desde la línea de comandos `mvn spring-boot:run`

También puedes crear un jar ejecutable y ejecutar la aplicación de la siguiente manera:

```
mvn package && java -jar target/spring-boot-start-0.0.1-SNAPSHOT.jar
```

Si todo va bien, deberías de ver que la aplicación se está ejecutando correctamente en el puerto 8080.


![Screenshot 01](/assets/img/20210115springboot/screenshot01.png)


Para probar, abre en el navegador la URL `http://localhost:8080/` y deberías de ver el mensaje en el navegador.

```
Greetings from Spring Boot!
```

# Código fuente

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-start)

```
git clone https://github.com/jocamav/tutorials.git
cd tutorials/spring-boot-start
mvn spring-boot:run
```


# Referencias

[Spring Boot Getting Started][spring-guide]

[Spring Boot Reference Documentation][spring-boot-doc]


[spring-guide]: https://spring.io/guides/gs/spring-boot/
[spring-boot-doc]: https://docs.spring.io/spring-boot/docs/2.4.1/reference/html/
[github-logo]: /assets/logos/github-mark-32.png
[spring-logo]: /assets/logos/spring.svg
