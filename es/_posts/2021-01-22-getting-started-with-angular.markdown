---
layout: post
title:  "Empezando a trabajar con Angular"
author:  "José Carlos Martínez"
lang: es
lang-ref: getting-started-with-angular
date:   2021-01-22 14:38:12 +0100
comments: true
categories: [angular]
logo: angular
---

Angular es una plataforma y framework para desarrollas aplicaciones en cliente en HTML y TypeScript. Angular está escrito
en TypeScript. Además del "core", implementa una serie de funcionalidades opcionales como un conjunto de librerías TypeScript
que puedes importar a tus aplicaciones.

En la página de Angular puedes encontrar documentación muy completa y ejemplos que te ayudarán a comprender mejor
como funciona el Framework. En este post vamos a desarrollar una aplicación muy sencilla para tener una ligera idea 
de los distintos componentes utilizados en Angular. Si quieres saber más, te recomiento que eches un vistazo al 
tutorial [Empezando con Angular](https://angular.io/tutorial).

# Tecnologías utilizadas

* npm 6.13.4
* node 12.13.1
* Angular CLI 10.0.8
* Angular 10.0.9

# Crear un nuevo proyecto

Gracias a Angular CLI podemos crear un nuevo proyect, generar código, realizar tareas como ejecutar test, compilar el proyecto
y preparar el despliegue. Todo ello con un simple comando. Si no tienes Angular CLI instalado, puedes instalarlo de manera
global en tu ordenador ejecutando el siguiente comando desde un terminal o consola de Windows::

```
npm install -g @angular/cli
```

Una vez que tenemos Angular CLI instalado, podemos crear un nuevo proyecto con el comando `ng new`.

```
ng new angular-getting-started
```

El comando `ng new` te llevará por una asistente para seleccionar las distintas funcionalidades a incluir inicialmente
en la aplicación. Acepta todas las opciones por defecto pulsando Intro o Return.

Tras unos cuantos minutos, el proyecto se creará en un nuevo directorio `angular-getting-started`. Echemos un vistazo a 
los distintos ficheros y directorios creados por Angular CLI.

* La aplicación se crea en un nuevo directorio `angular-getting-started`.
* El esquéleto de la applicación se crea en el subdirectorio `src`.
* Un projecto end-to-end test (en el subdirectorio `e2e`).
* Ficheros de configuración.

Para compilar y ejecutar la aplicación localmente, ejecuta los siguientes comandos:

```
cd angular-getting-started
ng serve --open
```

El comando `ng serve` arranca el servidor, observa los cambios de los ficheros y recompila la aplicación según realicemos
cambios en cualquiera de estos ficheros.

La opción `--open` (o simplemente -o) se utiliza para abrir automáticamente la URL `http://localhost:4200/` directamente 
en el navegador.

Podemos ver la aplicación creada por defecto con Angular CLI.

![Screenshot 01](/assets/img/20210122angular/screenshot01.png)


# Componentes de Angular

Los componentes son los bloques fundamentales de cualquier aplicación en Angular. Muestran la información por pantalla,
detectan los eventos del usuario y realizan acciones en funcion de dichos eventos. El principal componente de una aplicación
Angular es el el componente `AppComponent`. La implementación del componente `AppComponent` (así como cualquier otro componente) 
se distribuye en tres dicheros:

* `app.component.ts` la clase del componente, escrita en **TypeScript**.
* `app.component.html` la plantilla del componente, escrita en **HTML**.
* `app.component.css` los estilos **CSS** privados del componente.

Empecemos por limpiar la plantilla para mostrar únicamente el título de la aplicación. Empcemos por borrar el contenido
del fichero `app.component.html` e incluir el siguiente trozo de código:

{% highlight html %}
{% raw %}
<h1>{{ title }} app is running!</h1>
{% endraw %}
{% endhighlight %}

Una vez que salvemos los cambios, veremos que en el navegador se actualizará la aplicación sin necesidad de reiniciar el 
servidor. Deberíamos de ver únicamente el título de la aplicación en el navegador.

![Screenshot 02](/assets/img/20210122angular/screenshot02.png)

Las llaves dobles son parte de la **sintaxis de interpolación de enlace** en Angular. Esta interplación 
permite enlazar el título declarado en la clase en la cabecera HTML de la plantilla. ¿Cómo podemos cambiar entonces 
el título de la aplicación? Echemos un vistazo al fichero `app.component.ts`:

{% highlight typescript %}

title = 'angular-getting-started';

{% endhighlight %}

En la clase del componente hay un atributo llamado `title`. Si queremos cambiar el título por cualquier otro, nos basta
com cambiar directamente el valor de dicho atributo en la clase..

{% highlight typescript %}

title = 'Cool Angular App';

{% endhighlight %}

Una vez realizado el cambio el navegado actualizará la aplicación con el nuevo título.

![Screenshot 03](/assets/img/20210122angular/screenshot03.png)

Vale...no es que sea la leche. Afortunamente Angular nos permite definir estilos para el componente. Vamos a abrir 
el fichero `app.component.css` para añadir los siguientes estilos:

{% highlight css %}

h1 {
  color: #369;
  font-family: Arial, Helvetica, sans-serif;
  font-size: 250%;
}

{% endhighlight %}

Bueno, con un poco de color pinta mejor la cosa. 


![Screenshot 04](/assets/img/20210122angular/screenshot04.png)

# Creando un nuevo componente

Con Angular CLI es muy fácil crear nuevos componentes y añadir componentes anidados dentro de otros componentes. Para
crear nuevos componentes con Angular CLI, podemos utilizar el comando `ng gnerate`. Para crear un componente llamado
`StudentList` podemos ejecutar:

```
ng generate component student-list
```

El comando generará automaticamente todos los ficheros del componente.

``` 
CREATE src/app/student-list/student-list.component.html (27 bytes)
CREATE src/app/student-list/student-list.component.spec.ts (664 bytes)
CREATE src/app/student-list/student-list.component.ts (292 bytes)
CREATE src/app/student-list/student-list.component.css (0 bytes)
UPDATE src/app/app.module.ts (418 bytes)
```

Los tres ficheros principales se han creado automáticamente:
* Un fichero **HTML** con la plantilla.
* La clase **TypeScript** para declarar el componente.
* Un fichero **CSS** para los estilos.

> Podemos ver que hay un cuarto fichero `spec.ts` que se ha generado. Los ficheros `spect.ts` son ficheros que incluyen
los tests del componente. Por defecto, estos ficheros se generan en el mismo directorio que el componente. 

Echemos un vistazo al componente `StudentListComponent` declarado `student-list.component.ts`. 

```typescript
@Component({
  selector: 'app-student-list',
  templateUrl: './student-list.component.html',
  styleUrls: ['./student-list.component.css']
})
```

Podemos ver que tanto `templateUrl` como `styleUrls` hacen referencia a los otros ficheros creados. El atributo `selector` 
es el nombre del selector que podremos utilizar para incluir nuestro componente en otros componentes. Por ejemplo, 
para incluir este componente al componente `AppComponent` debemos añadir `<app-student-list></app-student-list>` 
a la plantilla de `AppComponent`.

```html
{% raw %}
<h1>{{ title }} app is running!</h1>
<app-student-list></app-student-list>
{% endraw %}
```

Al hacer esto, deberíamos de ver un mensaje `student-list works!` en la página principal.

![Screenshot 05](/assets/img/20210122angular/screenshot05.png)

Vamos a modificar la plantilla para mostrar algo más interesante. Vamos a añadir una lista de estudiantes por ejemplo.

```html
<ul>
  <li>Harry Potter</li>
  <li>Ron Weasley</li>
  <li>Hermione Granger</li>
  <li>Draco Malfoy</li>
  <li>Cedric Diggory</li>
</ul>
```

Ahora deberíamos de ver la lista en el navegador.

![Screenshot 06](/assets/img/20210122angular/screenshot06.png)

# Usando directivas

Esta no es la mejor manera para mostrar una lista. Es más elegante definir una lista de estudiantes en la clase y mostrar
la lista dependiendo del contenido de esta variable. Para ello, podemos añadir un listado de estudiantes en 
`student-list.component.ts` con el nombre de los estudiantes.

```typescript
  students = [
    'Harry Potter',
    'Ron Weasley',
    'Hermione Granger',
    'Draco Malfoy',
    'Cedric Diggory'
  ];
```

> Lo normal sería que esta información viniera de un servidor. Pero para este ejemplo vamos a definir directamente el listado en la clase.

Ahora necesitamos cambiar la plantilla para enlazar los elementos `li` a la lista. Podríamos hacer algo como lo siguiente:

{% raw %}
```html
<ul>
  <li>{{ students }}</li>
</ul>
``` 
{% endraw %}

Vemos que el listado se muestra como un único elemento de la lista. Seguramente esto no es lo que estábamos esperando.

![Screenshot 07](/assets/img/20210122angular/screenshot07.png)

Para arreglar esto, podemos utilizar una de las directivas más utilizadas de Angular. Con `ngFor` podemos iterar sobre la lista
y añadir un elemento `li` para cada uno de los items de la lista.

{% raw %}
```html
<ul>
  <li *ngFor="let student of students">{{ student }}</li>
</ul>

``` 
{% endraw %}

> No olvides añadir el asterisco (*) antes de ngFor. Es una parte fundamental de la sintaxis de Angular.

# Resumen

En este artículo hemos visto:

* Como instalar Angular CLI.
* Como crear un nuevo proyecto Angular.
* Comprender la estructura de un proyecto Angular.
* Como aádir y crear Componentes.
* Comprender como enlazar datos del componente a la plantilla.
* Usar directivas de Angular como `ngFor`.

Todavía hay mucho más que saber sobre  Angular: Modulos, Componentes, Servicios, Formularios, Routing, Testing...en la 
[Documentación oficial de Angular][angular-doc] puedes encontrar toda la información que necesitas. El mejor punto de partida 
es completar el [Tutorial de Angular Tour of Heroes][angular-tutorial].

# Source Code

![Github Logo][github-logo]
[GitHub Project](https://github.com/jocamav/tutorials/tree/master/spring-boot-angular)

```
git clone https://github.com/jocamav/tutorials.git
cd tutorials/angular-getting-started
npm install
ng serve -o
```

# References

[Angular Setup][angular-setup]

[Angular Tour of Heroes Tutorial][angular-tutorial]


[angular-doc]: https://angular.io/docs
[angular-setup]: https://angular.io/guide/setup-local
[angular-tutorial]: https://angular.io/tutorial
[github-logo]: /assets/logos/github-mark-32.png
