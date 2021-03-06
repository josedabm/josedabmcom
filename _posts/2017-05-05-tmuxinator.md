---
layout: post
title:  "Tmuxinator: plantillas para tmux"
date:   2017-05-05 13:27
cover:  "/img/portadas-blog/tmuxinator.svg"
cover_alt: tmuxinator
cover_title: tmuxinator
og_image: /img/og/tmuxinator.png
image_mime_type: image/png
permalink: "/blog/tmuxinator-plantillas-para-tmux/"
excerpt: "Tmux tiene sus limitaciones. Cada vez que quieres usarlo tienes que crear el layout desde cero. Con tmuxinator puedes crear plantillas para tmux y lanzarlas con un solo comando. Lee el artículo para saber cómo."
featured: true
---

## Introducción

¡Hola! Me alegro de verte por aquí, porque si no conoces **tmuxinator**, descubrir esta app te va a alegrar el día :D

Pero antes que nada, si no conoces la aplicación tmux te aconsejo que leas el artículo anterior a éste: [tmux: app para multiplexar la terminal]({{ site.baseurl }}/blog/tmux-multiplexa-la-terminal/).

Comencemos pues. A modo de resumen del artículo anterior, tmux es una app para multiplexar tu terminal en más *subterminales* y poder trabajar con cada una de ellas de forma independiente. Es una gran ventaja, ¿no te parece? Pero la principal desventaja que tiene es que no puedes guardar layouts de terminales. Tienes que construir de forma manual los layouts cada vez que quieras usarlos. Y exactamente para eso se desarrolló tmuxinator, para **crear templates de tmux y lanzarlas con un solo comando**. Pero no todo termina ahí, también puedes **ejecutar una orden en cada una de esas terminales** al lanzar el template. Si te digo la verdad, es una de las aplicaciones que más productivo me ha hecho desde que me pasé a Linux.

## Instalación

Tmuxinator es una app que está desarrolada en ruby. Si quieres instalarla lo primero es instalar ruby (si todavía no lo tienes instalado).

{% highlight shell %}
  sudo apt-get install ruby    # Ubuntu y derivados
  sudo pacman -S ruby          # Arch y derivados
  sudo yum install ruby        # Fedora y derivados
{% endhighlight %}

Una vez instalado ruby, se procede a instalar tmuxinator

{% highlight shell %}
  gem install tmuxinator
{% endhighlight %}

## Comandos básicos

Lo primero, para ver todas las acciones que te ofrece tmuxinator, sólo tienes que lanzar el comando

{% highlight shell %}
  tmuxinator
{% endhighlight %}

y te aparecerá este texto de ayuda a modo de chuleta

{% highlight shell %}
  tmuxinator commands:
    tmuxinator commands                          # Lists commands available in tmuxinator
    tmuxinator completions \[arg1 arg2]           # Used for shell completion
    tmuxinator copy \[EXISTING] \[NEW]             # Copy an existing project to a new project and open it in your editor
    tmuxinator debug \[PROJECT] \[ARGS]            # Output the shell commands that are generated by tmuxinator
    tmuxinator delete \[PROJECT1] \[PROJECT2] ...  # Deletes given project
    tmuxinator doctor                            # Look for problems in your configuration
    tmuxinator help \[COMMAND]                    # Describe available commands or one specific command
    tmuxinator implode                           # Deletes all tmuxinator projects
    tmuxinator list                              # Lists all tmuxinator projects
    tmuxinator local                             # Start a tmux session using ./.tmuxinator.yml
    tmuxinator new \[PROJECT]                     # Create a new project file and open it in your editor
    tmuxinator start \[PROJECT] \[ARGS]            # Start a tmux session using a project's tmuxinator config, with an optional \[ALIAS] for project reuse
    tmuxinator stop \[PROJECT]                    # Stop a tmux session using a project's tmuxinator config
    tmuxinator v
{% endhighlight %}

Como lo que quiero es que puedas iniciarte con tmuxinator y sacarle partido lo más pronto posible, sólo te voy a explicar estas 3 órdenes:

Crear un nuevo proyecto/template:

{% highlight shell %}
  tmuxinator new nombre_template
{% endhighlight %}

Lanzar un template ya creado:

{% highlight shell %}
  tmuxinator start nombre_template
{% endhighlight %}

Parar un template (sale de la vista de tmux):

{% highlight shell %}
  tmuxinator stop nombre_template
{% endhighlight %}

## Crear un nuevo template

Para crear un template, como dije antes, ejecuta

{% highlight shell %}
  tmuxinator new nombre_template
{% endhighlight %}

Para este tutorial voy a usar el nombre de template `my_first_template`, entonces para crear la plantilla:

{% highlight shell %}
  tmuxinator new my_first_template
{% endhighlight %}

Esta orden creará el fichero `~/.tmuxinator/my_first_template.yml` y abrirá dicho fichero en tu editor de texto por defecto. Este fichero es donde configuras la nueva plantilla.

Como mencioné anteriormente mi objetivo es que empieces a sacarle partido a tmux + tmuxinator lo antes posible. No voy a hacer un análisis exhaustivo de la app. Así que para  simplificar el tutorial, voy a quitar ciertas opciones, y ya si en algún momento quieres ampliar, basta con mirar la documentación. Limpiando un poco el documento, el fichero queda así:

{% highlight yaml %}
  # ~/.tmuxinator/my_first_template.yml

  name: my_first_template
  root: ~/

  startup_window: tab1
  startup_pane: 0

  windows:
    - tab1:
        layout: main-vertical
        panes:
          - nano
          - man tmux
    - tab2: watch --color git status
{% endhighlight %}

Explico cada parte:

 {% highlight yaml %}
  # ~/.tmuxinator/my_first_template.yml
 {% endhighlight %}

es la localización del fichero.

{% highlight yaml %}
  name: my_irst_template
{% endhighlight %}

es el nombre de la plantilla por el cual la inicializas con el comando `tmuxinator start ...`

{% highlight yaml %}
  root: ~/
{% endhighlight %}

es el directorio donde quieres que se sitúen todas las terminales cuando se inicie el template.

{% highlight yaml %}
  startup_window: tab1
{% endhighlight %}

es la pestaña en la que te quieres situar cuando se inicie el template. Si no se establece este parámetro, toma como valor por defecto el nombre de la primera pestaña.

{% highlight yaml %}
  startup_pane: 1
{% endhighlight %}

es la terminal en la que te quieres situar (dentro de la pestaña anterior) cuando se inicie el template. Si no se establece este parámetro, toma como valor por defecto `0`.

{% highlight yaml %}
  windows:
    - tab1:
        layout: main-vertical
        panes:
          - nano
          - man tmux
    -tab2: watch --color git status
{% endhighlight %}

En el parámetro `windows` primero se establecen las pestañas. En este caso las pestañas son `tab1` y `tab2`. Yo les he puesto esos nombres, pero tú puedes establecer los que tu quieras.

En cada pestaña puedes hacer dos cosas:

-Establecer una serie de terminales, como en la pestaña `tab1`.
-Establecer un comando para esa pestaña, como en la pestaña `tab2`. En este caso en la pestaña solo aparecerá una única terminal.

Si decides añadir terminales a una pestaña debes tener en cuenta dos parámetros:

-`layout`, sería la estructura de terminales que tiene la pestaña. Puedes usar un valor por defecto o un valor personalizado. Los valores personalizados los vermos en el próximo apartado. Como valores por defecto puedes usar:
  -main-vertical
  -main-horizontal
  -even-vertical
  -even-horizontal
  -tiled
-`panes`, cada una de las terminales del layout definido arriba. A cada una se le puede asignar o no un comando a ejecutar para cuando se inicie la plantilla.

Bueno, pues todo ésto sería cómo configurar un template básico. En el próximo apartado veremos cómo configurar un template con un layout personalizado. ¡No te lo pierdas! Es muy fácil.

## Crear un layout personalizado

Antes de nada, para hacer ésto necesitas controlar las acciones básicas de tmux. Si no te sabes mover todavía con tmux, ve al artículo anterior que menciono al principio del artículo. Voy a ir poniendo capturas de pantalla para que te sea mucho más fácil de comprender el proceso. Verás que no es difícil.

Para crear un layout personalizado en tmuxinator, primero debes ejecutar el comando `tmux` y crear un layout manualmente.

![Template de tmux]({{ site.baseurl }}/img/capturas/tmuxinator-template-tmux.png  "Template de tmux")

Recuerda  que para dividir la terminal en dos terminales verticales se usa `ctrl+b` `%` y para dividirla en dos terminales horizontales se usa `ctrl+b` `"`

Una vez tengas el layout como quieres debes ejecutar

{% highlight shell %}
  tmux list-windows
{% endhighlight %}

y aparecerá algo así:

![Obtención del layout de tmux]({{ site.baseurl }}/img/capturas/tmuxinator-obtener-layout.png  "Obtención del layout de tmux")

Debes copiar lo que está dentro de los corchetes que contienen la palabra `layout`, excepto dicha palabra. Entonces de la captura anterior tienes que copiar lo siguiente:

{% highlight shell %}
  e082,150x39,0,0\[150x24,0,0,0,150x14,0,25{99x14,0,25,1,50x14,100,25,2}]
{% endhighlight %}

y debes pegarlo como valor del parámetro `layout` del fichero de configuración del template (`~/.tmuxinator/nombre_template.yml`).

El siguiente paso es establecer el resto de parámetros de ese mismo fichero, que ya vimos como se hace en el apartado anterior.

{% highlight yaml %}
  # ~/.tmuxinator/josedabm.yml

  name: josedabm

  root: ~/proyectos/josedabm.com/

  windows:
    - editor:
        layout: e082,150x39,0,0\[150x24,0,0,0,150x14,0,25{99x14,0,25,1,50x14,100,25,2}]
        panes:
          - clear
          - gitwatch
          - jekyll serve
{% endhighlight %}

Y para finalizar, guardamos el fichero, y a partir de ahora, solo tienes que ejecutar

{% highlight shell %}
  tmuxinator start nombre_template
{% endhighlight %}

para iniciar el template.

Y para salir del template:

{% highlight shell %}
  tmuxinator stop nombre_template
{% endhighlight %}

Aquí tenéis una pequeña demostración del template de las capturas que he ido haciendo

![Demostración de tmuxinator]({{ site.baseurl }}/img/gifs/demostracion-tmuxinator.gif  "Demostración de tmuxinator")

## Conclusión

¡Ésto es una maravilla!

Con un solo comando podrás poner en marcha una serie de scripts y visualizarlo todo en la misma pantalla, o en distintas pestañas. A mi me ha venido muy bien para comenzar a trabajar rápidamente en mis  proyectos de desarrollo.

Espero que te haya gustado tanto como a mí.

¡Hasta la próxima!
