# Blog de MakingDevs

## Estructura del blog

La herramienta que usamos se llama [Octopress][1], y es la misma herramienta que usan en GitHub. El hosting del blog es un repositorio y la publicación final es formada con los archivos del branch _master_.

## Instrucciones para escribir un blog post

- Como somos parte de la organización solo tienen que clonar el repo
- Verán dos branches:
  - **master** : Aquí está el contenido generado para el blog
  - **source** : Aquí están los archivos para generar el contenido del blog
- Los requisitos previos para usarse son:
  - Ruby 2.+ (Les recomiendo que usen [rbenv][2])
  - [Bundler][3] instalado
  - Editor de textos
- Una vez con estas herramientas se tiene que instalar todas las gemas necesarias: `bundle install`
- Y para correr un preview del sitio puedene ejecutar: `rake preview`
  - Si por alguna razón no les corre por versiones de gemas, entonces forcen con ayuda de _bundler_: 'bundle exec rake preview'
  - Verán el sitio publicado en [http://localhost:4000][4]
- Para crear un nuevo blog post usen : `rake new_post['nueva entrada']`
  - Esto genera un archivo en el directorio **_posts** en donde encontrarán un archivo con extensión _.markdown_ del mismo nombre que mandaron por línea de comando
    - Al estar en la línea de comando y creo que con zsh tendrán que hacer algo como :  `rake new_post\['nueva entrada'\]` , es decir, escapar los corchetes cuadrados.
- En ese mismo instante pueden ver su artículo publicado en local para que vayan viendo como va quedando, sin reiniciar el server. Pero si van a mandar una nueva entrada y desean que no esté visible tienen que ocupar el atributo `published: false`.
  - Les recomiendo que vean o se ayuden de los otros blog post previamente publicados, pueden poner varias cosas en la información del blog post
  - Una vez que ya tienen lista su entrada, pueden manejarla con git como quieran subirla a un branch o tenerla en su local
- Para publicar tienen que ejecutar:
  - `rake generate` para crear todo el contenido estático
    - Si por alguna razón les manda error al no encontrar el directorio **_deploy**, sólo tienen que crearlo
  - `rake deploy` sube al master sus cambios y por ende lo publica
- No olviden de subir sus cambios de sus archivos markdown al branch **source** del repo

Nota: Les recomiendo que sus snippets de código los hagan en sus gist de github y los referencien, es muy sencillo hacerlo.

[1]: http://octopress.org/
[2]: https://github.com/sstephenson/rbenv
[3]: http://bundler.io/
[4]: http://localhost:4000
