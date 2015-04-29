---
layout: post
title: "Introduccion a Ratpack"
date: 2014-08-19 00:34:28 -0500
author: Felipe Juárez Murillo
comments: true
categories:
- development
---

Es bueno estar de vuelta escribiendo, ya ha pasado bastante tiempo desde que hice un post así que vamos a ver algo que me ayudó en un curso. El día de hoy hablaremos de Ratpack y para ello primero vamos a dar una pequeña introducción de lo que es. Ratpack, como su página lo dice, es un conjunto de librerías de JAVA que facilita la rapidez, eficiencia, evolución y pruebas de aplicaciones HTTP, está construida sobre Netty y por ello posee muchos de los beneficios del motor del mismo. Ratpack se enfoca en permitir applicaciones HTTP para ser eficientes, modulares y adaptativas a los nuevos requerimientos, tecnologías y buenas pruebas sobre el tiempo. Bueno vamos a dejar de momento las definiciones y vamos a lo bonito, el código. Para este post vamos a hacer uso de gvm y de lazybones, para ello procederemos a instalar [gvm][1] Una vez listo procedemos a ejecutar el siguiente comando :

``` bash
gvm install lazybones
```

Y con ello tendremos lazybones instalado en nuestra máquina... bueno y ¿para que necesitamos lazybones si es un post sobre Ratpack? Lazybones es una herramienta de línea de comandos que nos permite generar una estructura de un proyecto para cualquier framework basado en plantillas predefinidas. Y Ratpack tiene varias plantillas para lazybones que pueden ser encontradas en

[Bintray][2] en el apartado de [ratpack/lazybones repository][3]. Si desean saber un poco más de Lazybones pueden encontrarlo en la [documentación][4] Para crear la estructura del proyecto ejecutaremos la siguiente sentencia :

``` bash
lazybones create ratpack contact-book
```

Al ejecutar la instrucción nos aparecerá en pantalla una serie de instrucciones y también lo que ganamos al ejecutarla. A parte nos da la estructura de directorios que se genera y nos dice como levantar nuestra aplicación.

``` bash
  <proj>
    |
    +- src
        |
        +- ratpack
        |     |
        |     +- ratpack.groovy
        |     +- ratpack.properties
        |     +- public          // Static assets in here
        |          |
        |          +- images
        |          +- lib
        |          +- scripts
        |          +- styles
        |
        +- main
        |   |
        |   +- groovy
                 |
                 +- // App classes in here!
        |
        +- test
            |
            +- groovy
                 |
                 +- // Spock tests in here!
```

Para levantar el ejemplo nos movemos a la carpeta con cd, ejecutamos lo siguiente y nos preparamos un café porque va a descargar dependencias XD:
`./gradlew run`

Una vez que terminó de ejecutarse podemos ver lo que se creó en la url http://localhost:5050 como podrán observar la página que se creó no es gran cosa pero ya tenemos lo necesario para trabajar en una aplicación un poco más compleja como iremos viendo a lo largo de este post. Como podremos ver en el **README.md** existen prácticamente tres archivos principales:

- **build.gradle**
- **Ratpack.groovy**
- **index.html**

El primero es un archivo de gradle (para los que no lo han manejado o conocen de él pueden revisar el siguiente [link][5] pero a grandes rasgos gradle vendría siendo lo mismo que Maven pero con drogas XD. El segundo archivo es donde usualmente sucede la magia ya que es donde se resuelven las urls y se hace todo el show necesario. Y el tercer archivo no es más que una plantilla html que se visualiza a través del segundo archivo. Con los arvhivos que vamos a estar trabajando de momento son **index.html** y **Ratpack.groovy**. En el tag body del html dejamos lo siguiente:

``` html
<body>
  <section>
    <h1>${model.title}</h1>
    <p>This is the main page of your contacts</p>
  </section>

  <footer class="site-footer"></footer>
</body>
```

Y en el **Ratpack.groovy** dejamos lo siguiente:

``` groovy
import static ratpack.groovy.Groovy.groovyTemplate
import static ratpack.groovy.Groovy.ratpack

ratpack {
  handlers {
    get {
      render groovyTemplate("index.html", title: "Contact book")
    }

    assets "public"
  }
}
```

Recargamos la página y vemos el cambio. Ahora vamos a añadir un form para poder agregar a un elemento a la lista de contactos. Para ello agregamos un link a nuestro _index.html_

``` html
  <body>
    <section>
      <h1>${model.title}</h1>
      <p>This is the main page of your contacts</p>
    </section>

    <a href="create">Create contact</a>

    <footer class="site-footer"></footer>

    <!-- build:js scripts/jquery.js -->
    <script src="lib/jquery/jquery.min.js"></script>
    <!-- endbuild -->
  </body>
```

Ahora para visualizar la nueva página le agregamos al archivo

**Ratpack.groovy** lo siguiente :
<pre><code class="groovy">get('contacts/new') {
  render groovyTemplate("contacts/new.html", title: "New contact")
}
</code></pre> Y dentro de la carpeta de templates creamos una carpeta llamada contacts y dentro de ella agregamos un archivo llamado

**new.html**
<pre><code class="html">&lt;!doctype html&gt;
  &lt;!--[if lt IE 7]&gt;      &lt;html class="no-js lt-ie9 lt-ie8 lt-ie7"&gt; &lt;![endif]--&gt;
  &lt;!--[if IE 7]&gt;         &lt;html class="no-js lt-ie9 lt-ie8"&gt; &lt;![endif]--&gt;
  &lt;!--[if IE 8]&gt;         &lt;html class="no-js lt-ie9"&gt; &lt;![endif]--&gt;
  &lt;!--[if gt IE 8]&gt;&lt;!--&gt; &lt;html class="no-js"&gt; &lt;!--&lt;![endif]--&gt;
  &lt;head&gt;
    &lt;meta charset="utf-8"&gt;
    &lt;meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"&gt;
    &lt;title&gt;Ratpack: ${model.title}&lt;/title&gt;
    &lt;meta name="description" content=""&gt;
    &lt;meta name="viewport" content="width=device-width"&gt;
  &lt;/head&gt;

  &lt;body&gt;
    &lt;section&gt;
      &lt;h1&gt;${model.title}&lt;/h1&gt;
    &lt;/section&gt;

    &lt;a href="/"&gt;Home&lt;/a&gt;

    &lt;footer class="site-footer"&gt;&lt;/footer&gt;
  &lt;/body&gt;
&lt;/html&gt;
</code></pre> Así para cuando regresemos a la página y la recarguemos podemos ver el link y hasta el momento navegar la página sin problemas. Ahora vamos a agregar un pequeño formulario para poder capturar los datos del contacto, como son:

*   **Nombre**
*   **Apellidos**
*   **Correo**
*   **Alias**
*   **Teléfono** Para ello vamos a hacer un formulario común y silvestre en el html:

<pre><code class="html">  &lt;body&gt;
    &lt;section&gt;
      &lt;h1&gt;${model.title}&lt;/h1&gt;
    &lt;/section&gt;

    &lt;form action="/contacts" method="post"&gt;
      &lt;div&gt;
        &lt;label for="name"/&gt;Name&lt;/label&gt;
        &lt;input type="text" name="name" /&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="lastName"/&gt;Last name&lt;/label&gt;
        &lt;input type="text" name="lastName" /&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="email"/&gt;Email&lt;/label&gt;
        &lt;input type="text" name="email" /&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="nickname"/&gt;Nickname&lt;/label&gt;
        &lt;input type="text" name="nickname" /&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="phone"/&gt;Phone&lt;/label&gt;
        &lt;input type="text" name="phone" /&gt;
      &lt;/div&gt;

      &lt;button type="submit"&gt; Create &lt;/button&gt;
    &lt;/form&gt;

    &lt;a href="/"&gt;Home&lt;/a&gt;

    &lt;footer class="site-footer"&gt;&lt;/footer&gt;
  &lt;/body&gt;
</code></pre> Una vez que tenemos eso lo demás es cuestión de recibirlo y procesarlo para ello hacemos uso del handler post con el path al que va a responder y vamos a agregarlo a una variable global (esto es para efectos de ejemplo no intenten esto en casa o sus trabajos XD)

<pre><code class="groovy">ratpack {
  def contacts = []

  handlers {
    get {
      render groovyTemplate("index.html", title: "Contact book")
    }

    post('contacts') {
      Form form = context.parse(Form)
      def contact = [:]
      contact.id = contacts ? contacts.id.max() + 1 : 1
      contact.name = form.name
      contact.lastName = form.lastName
      contact.email = form.email
      contact.nickname = form.nickname
      contact.phone = form.phone
      contacts &lt;&lt; contact
      redirect "/contacts/${contact.id}"
    }

    get('contacts/new') {
      render groovyTemplate("contacts/new.html", title: "New contact")
    }

    assets "public"
  }
}
</code></pre> Como podrán observar mediante el

*context.parse(Form)* obtenemos los datos enviados en la petición y de esa manera podemos acceder a los datos enviados como si fuera un mapa de groovy. Al final de la petición hacemos un redirect para ver los datos que se han creado con esa petición, pero de momento esto no funciona así que vamos a agregar un nuevo handler con algo que se le llama *pathTokens* y para ello agregamos lo siguiente:
<pre><code class="groovy">  get('contacts/:id') {
    def id = pathTokens.asLong('id')
    def contact = contacts.find {
      it.id == id
    }
    render groovyTemplate("contacts/show.html", contact:contact)
  }
</code></pre> Así mismo agregamos la página que va a visualizar los datos del contacto dentro de la carpeta

*contacts*:
<pre><code class="html">&lt;html&gt;
  &lt;head&gt;
    &lt;title&gt;Ratpack: Show contact&lt;/title&gt;
  &lt;/head&gt;

  &lt;body&gt;
    &lt;section&gt;
      &lt;h1&gt;Show contact&lt;/h1&gt;
    &lt;/section&gt;

    &lt;div&gt;
      &lt;div&gt;
        &lt;label for="name"/&gt;Name : &lt;/label&gt;
        &lt;span&gt;&lt;strong&gt; ${model.contact.name} &lt;/strong&gt;&lt;/span&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="lastName"/&gt;Last name : &lt;/label&gt;
        &lt;span&gt;&lt;strong&gt; ${model.contact.lastName} &lt;/strong&gt;&lt;/span&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="email"/&gt;Email : &lt;/label&gt;
        &lt;span&gt;&lt;strong&gt; ${model.contact.email} &lt;/strong&gt;&lt;/span&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="nickname"/&gt;Nickname : &lt;/label&gt;
        &lt;span&gt;&lt;strong&gt; ${model.contact.nickname} &lt;/strong&gt;&lt;/span&gt;
      &lt;/div&gt;

      &lt;div&gt;
        &lt;label for="phone"/&gt;Phone : &lt;/label&gt;
        &lt;span&gt;&lt;strong&gt; ${model.contact.phone} &lt;/strong&gt;&lt;/span&gt;
      &lt;/div&gt;
    &lt;/div&gt;

    &lt;ul&gt;
      &lt;li&gt; &lt;a href="/"&gt;Home&lt;/a&gt; &lt;/li&gt;
      &lt;li&gt; &lt;a href="contacts/new"&gt;New contact&lt;/a&gt; &lt;/li&gt;
    &lt;/ul&gt;

  &lt;/body&gt;
&lt;/html&gt;
</code></pre> Ahora vamos a visualizar nuestra lista de contactos y para ello agregaremos un nuevo

*handler* y un nuevo elemento llamado *byMethod*:
<pre><code class="groovy">  handler("contacts") {
    byMethod {
      get {
        render groovyTemplate("contacts/list.html", contacts: contacts)
      }

      post {
        Form form = context.parse(Form)
        def contact = [:]
        contact.id = contacts ? contacts.id.max() + 1 : 1
        contact.name = form.name
        contact.lastName = form.lastName
        contact.email = form.email
        contact.nickname = form.nickname
        contact.phone = form.phone
        contacts &lt;&lt; contact
        redirect "/contacts/${contact.id}"
      }
    }
  }
</code></pre> Lo que hará el

*handler* es manejar la url "contacts" y delegarla por al método por el cual se haya realizado la petición en el caso de GET visualizará la lista de contactos pero si es por POST agregará los datos del contacto y después de agregarse se visualizará la información de dicho contacto. También agregaremos un link a la lista de contactos para poder visualizar cada uno de los datos de manera individual esto es por si se tiene información extra que no se muestre en el detalle general de la lista de contactos.
<pre><code class="html">&lt;html&gt;
  &lt;head&gt;
    &lt;title&gt;Ratpack: Contact list&lt;/title&gt;
  &lt;/head&gt;

  &lt;body&gt;
    &lt;section&gt;
      &lt;h1&gt;Contact list&lt;/h1&gt;
    &lt;/section&gt;

    &lt;div&gt;
      &lt;table border="1"&gt;
        &lt;thead&gt;
          &lt;tr&gt;
            &lt;th&gt;Name&lt;/th&gt;
            &lt;th&gt;Last name&lt;/th&gt;
            &lt;th&gt;Phone&lt;/th&gt;
            &lt;th&gt;Email&lt;/th&gt;
            &lt;th&gt;Nickname&lt;/th&gt;
            &lt;th&gt;Show&lt;/th&gt;
          &lt;/tr&gt;
        &lt;/thead&gt;
        &lt;tbody&gt;
          &lt;% model.contacts.each { %&gt;
            &lt;tr&gt;
              &lt;td&gt;${it.name}&lt;/td&gt;
              &lt;td&gt;${it.lastName}&lt;/td&gt;
              &lt;td&gt;${it.phone}&lt;/td&gt;
              &lt;td&gt;${it.email}&lt;/td&gt;
              &lt;td&gt;${it.nickname}&lt;/td&gt;
              &lt;td&gt; &lt;a href="/contacts/${it.id}"&gt;Show&lt;/a&gt; &lt;/td&gt;
            &lt;/tr&gt;
          &lt;% } %&gt;
        &lt;/tbody&gt;
      &lt;/table&gt;
    &lt;/div&gt;

    &lt;ul&gt;
      &lt;li&gt; &lt;a href="/"&gt;Home&lt;/a&gt; &lt;/li&gt;
      &lt;li&gt; &lt;a href="contacts/new"&gt;New contact&lt;/a&gt; &lt;/li&gt;
    &lt;/ul&gt;

  &lt;/body&gt;
&lt;/html&gt;
</code></pre> Ya nos falta poco para poder tener un

*CRUD* completo, solo nos falta eliminar y actualizar (casi nada XD). Así que para ello vamos a agregar un *handler* más y anidar un *byMethod* como lo hicimos con el POST de *contacts*. Para ello hacemos lo siguiente:
<pre><code class="groovy">  handler("contacts/:id") {
    byMethod {
      get {
        def id = pathTokens.asLong('id')
        def contact = contacts.find {
          it.id == id
        }
        render groovyTemplate("contacts/show.html", contact:contact)
      }

        delete {
          def id = pathTokens.asLong('id')
          contacts = contacts.findAll {
            it.id != id
          }
          render "ok"
        }
    }
  }
</code></pre> Ahora una pregunta, ¿Cómo hacemos para llamar a ese método desde la página? bueno para hacer eso vamos a requerir a jQuery y para ello ocuparemos

[Bower][6] y para instalar jquery usamos el siguiente comando
<pre><code class="sh">bower install jquery
</code></pre> Esto nos creará una carpeta llamada

*bower_components* que de momento moveremos a la carpeta de **src/ratpack/public/lib** para tenerla disponible en la aplicación.
<pre><code class="sh">mv bower_components src/ratpack/public/lib/
</code></pre> Una vez hecho procedemos a incluir el script en la app en la lista de contactos y agregamos un archivo más que es el que se encargará de eliminar el elemento.

<pre><code class="html">  &lt;script src="lib/bower_components/jquery/dist/jquery.js"&gt;&lt;/script&gt;
  &lt;script src="scripts/delete.js"&gt;&lt;/script&gt;
</code></pre> El código de eliminar es relativamente sencillo ya que lo que vamos a hacer es que cada elemento con la clase delete vamos a añadir el evento click y vamos a realizar la llamada al server con los datos del href.

<pre><code class="javascript">  $(function() {
    $(".delete").click(function(event) {
      event.preventDefault();
      var url = $(event.target).attr('href');
      $.ajax({
        type: "DELETE",
        url: url,
      }).success(function(data) {
        location.reload();
      });
    });
  });
</code></pre> Una vez realizado esto podremos ver como nuestros datos cambian al eliminar un elemento de la lista de contactos. Veamos ahora como editar un elemento. Para la edición vamos a hacerlo de la manera más fácil que podamos esto es agregaremos en el

*byMethod* anterior el método POST, agregamos otro GET más para visualizar la pantalla de edición y actualizaremos los datos de ese elemento, para ello requerimos crear una pantalla nueva y agregarla a la navegación:
<pre><code class="groovy">  get("contacts/:id/edit") {
    def id = pathTokens.asLong('id')
    def contact = contacts.find {
      it.id == id
    }
    render groovyTemplate("contacts/edit.html", title:"Editing contact", contact:contact)
  }

  handler("contacts/:id") {
    byMethod {
      post {
        def id = pathTokens.asLong('id')
        def contact = contacts.find {
          it.id == id
        }

        Form form = context.parse(Form)
        contact.name = form.name
        contact.lastName = form.lastName
        contact.email = form.email
        contact.nickname = form.nickname
        contact.phone = form.phone

        render groovyTemplate("contacts/show.html", contact:contact)
      }
    }
  }
</code></pre>

<pre><code class="html">  &lt;!doctype html&gt;
  &lt;html&gt;
    &lt;head&gt;
      &lt;meta charset="utf-8"&gt;
      &lt;meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1"&gt;
    &lt;/head&gt;

    &lt;body&gt;
      &lt;section&gt;
        &lt;h1&gt;${model.title}&lt;/h1&gt;
      &lt;/section&gt;

      &lt;form action="/contacts/${model.contact.id}" method="post"&gt;
        &lt;div&gt;
          &lt;label for="name"/&gt;Name&lt;/label&gt;
          &lt;input type="text" name="name" value="${model.contact.name}" /&gt;
        &lt;/div&gt;

        &lt;div&gt;
          &lt;label for="lastName"/&gt;Last name&lt;/label&gt;
          &lt;input type="text" name="lastName" value="${model.contact.lastName}"/&gt;
        &lt;/div&gt;

        &lt;div&gt;
          &lt;label for="email"/&gt;Email&lt;/label&gt;
          &lt;input type="text" name="email" value="${model.contact.email}"/&gt;
        &lt;/div&gt;

        &lt;div&gt;
          &lt;label for="nickname"/&gt;Nickname&lt;/label&gt;
          &lt;input type="text" name="nickname" value="${model.contact.nickname}"/&gt;
        &lt;/div&gt;

        &lt;div&gt;
          &lt;label for="phone"/&gt;Phone&lt;/label&gt;
          &lt;input type="text" name="phone" value="${model.contact.phone}"/&gt;
        &lt;/div&gt;

        &lt;button type="submit"&gt; Update &lt;/button&gt;
      &lt;/form&gt;

      &lt;ul&gt;
        &lt;li&gt; &lt;a href="/"&gt;Home&lt;/a&gt; &lt;/li&gt;
        &lt;li&gt; &lt;a href="/contacts"&gt;Contact list&lt;/a&gt; &lt;/li&gt;
        &lt;li&gt; &lt;a href="/contacts/new"&gt;New contact&lt;/a&gt; &lt;/li&gt;
      &lt;/ul&gt;

      &lt;footer class="site-footer"&gt;&lt;/footer&gt;
    &lt;/body&gt;
  &lt;/html&gt;
</code></pre> Y de esta manera tenemos un pequeño

*CRUD* con llamadas normales y un poco de *jquery*. En POST posteriores vamos a tomar este código y vamos a hacerle algunas mejoras. Para empezar haremos uso de los *groovyTemplates* como tal y ajustaremos esto a que se maneja más por *REST*. Espero les haya gustado y cualquier duda o comentario ya saben donde encontrarnos. Saludos. GL HF.

 [1]: http://gvmtool.net/ "gvm"
 [2]: https://bintray.com/
 [3]: https://bintray.com/ratpack/lazybones
 [4]: https://github.com/pledbrook/lazybones#running-it
 [5]: http://www.gradle.org/documentation
 [6]: http://bower.io/]
