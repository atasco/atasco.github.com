---
layout: post
title: "Octopress y las Cookies"
date: 2014-06-08 20:20
comments: true
categories: cajondesastre
---
Vaya despiste, ha pasado casi un año de la apertura y puesta en largo del blog y hasta ahora no había hecho los cambios necesarios para cumplir con *La Ley de Cookies*[^1].

> Esa que nos amarga la vida cada vez que entramos en una página web haciéndonos aceptar si o sí la correspondiente política si queremos seguir consultando el contenido.

{% img left /images/posts/cookie.gif 'Galleta!' %}

Como con casi todo lo referente a [octopress](http://octopress.org/), no sólo hay mucha información en la web para documentarse y solucionar cualquier problema, si no que hay desarrollos libres que resuelven el problema directamente.

En esta entrada voy a documentar de forma sencilla la solución que se ha implementado para cumplir con la *Ley de Cookies*[^2] en esta web.

<!-- more -->

Vamos al grano, hay un plugin de [Carl Woodhouse](http://www.carlwoodhouse.com/) que se puede encontrar en su repositorio de GitHub [carlwoodhouse](https://github.com/carlwoodhouse/jquery.cookieBar) que, como el mismo indica, es perfecto para implementar *La Ley de Cookies Europea*.

Hacer uso del plugin es tan sencillo como [descargarlo](https://github.com/carlwoodhouse/jquery.cookieBar/tarball/master) y copiarlo al directorio de plugins:

``` sh
cp jquery.cookieBar.js $HOME_OCTOPRESS/source/js/
```

Si se quiere utilizar la hoja de estilo que viene con el plugin, hay que copiarla al directorio correspondiente:

``` sh
cp cookieBar.css $HOME_OCTOPRESS/source/stylesheets/
```

Ahora hay que incluir las referencias al script y la hoja de estilo, así como la llamada, después de la librería *jQuery* en el fichero *head*:

``` sh
vi $HOME_OCTOPRESS/source/_includes/head.html
```

introduciendo las líneas:

```
<script src="{{ root_url }}/js/jquery.cookieBar.js" type="text/javascript"></script>
<link href="{{ root_url }}/stylesheets/cookieBar.css" media="screen, projection" rel="stylesheet" type="text/css">
<script type="text/javascript">
    $(document).ready(function() {
      $.cookieBar();
    });
</script>
```

después de:

```
<script src="//ajax.googleapis.com/ajax/libs/jquery/1.9.1/jquery.min.js"></script>
```

Para cambiar el texto que aparece en el banner de aceptación se edita el script:

``` sh
vi $HOME_OCTOPRESS/source/js/jquery.cookieBar.js
```

cambiando el texto:

```
// $('body').prepend('<div class="ui-widget"><div style="display: none;" class="cookie-message ui-widget-header blue"><p>By using this website you allow us to place cookies on your computer. They are harmless and never personally identify you.</p></div></div>');
```

Sólo nos queda crear una página con la información relativa a la política de cookies, en **atas.co** tenemos [Política de Cookies](http://atas.co/politica-de-cookies).

Generando de nuevo el site tendremos una estupenda cabecera indicando la aceptación del uso de cookies en la página web.


[^1]: En Internet hay mucha información y mucho más detallada y precisa de la que yo pueda incluir aquí. Una sencilla búsqueda como [esta](https://duckduckgo.com/?q=ley+de+cookies+en+espa%C3%B1a) devuelve una cantidad impresionante de fuentes desde la que comenzar la  apasionante puesta al día en *la vida, las cookies y cómo he podido vivir si la aplicación de la ley de cookies hasta este momento*.

[^2]: [Aquí](http://www.agpd.es/portalwebAGPD/canaldocumentacion/publicaciones/index-ides-idphp.php) hay un documento *"Guía sobre el uso de las cookies"* donde se explica qué y cómo hacer uso de los dulces sin azúcar que se comen los navegadores. 


