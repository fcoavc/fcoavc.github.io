# Blog

En este repositorio está el código fuente para generar [mi blog](https://fcoavc.github.io/blog).

En sitio se usa [Jekyll](https://jekyllrb.com/) para construir nuevos posts en el sitio.

Simplemente se crea un nuevo archivo `yyyy-mm-dd-nombre.md` en la carpeta `docs/_posts` y automáticamente se incluirá en el blog.

Para poder trabajar con los archivos markdown, se deben incluir los siguientes metadatos en formato yaml:

- layout: post
- title:  "Título del post"
- date:   yyyy-mm-dd hh:mm:ss -0600
- tags: etiqueta1 etiqueta2 etiquetan
- author: Nombre o username

Es posible incluir fragmentos de código, por ejemplo:

````
{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}
````

Para incluir código Latex en algún post, se debe incluir `{% include footer.html %}` debajo de los metadatos, con esto se podrá visualizar las ecuaciones escritas en Latex.

Las imágenes o material descargable se pueden colocar en la carpeta `assets` ubicado en la raíz del proyecto.