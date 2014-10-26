---
layout: post
title: "Análisis: Air Console"
date: 2014-10-26 21:30
comments: true
categories: cajondesastre network
---
{% img right /images/posts/airconsole.png 'airconsole' %}

Una tarea tan común y aparentemente sencilla, que todo ingeniero de redes realiza de forma continua, como es conectarse vía consola a la electrónica de red (router, switch, firewall...) puede complicarse si el acceso al dispositivo no resulta sencillo.

Para esos casos el dispositivo que analizamos en esta entrada puede ser la solución ideal.

<!-- more -->

En ocasiones trabajar en un [CPD](http://es.wikipedia.org/wiki/Centro_de_procesamiento_de_datos) puede resultar una experiencia incomoda por el espacio de trabajo que disponemos y/o por las condiciones de temperatura. En otras situaciones, el acceso a los armarios de comunicaciones resulta casi una misión imposible por el lugar donde están ubicados y conectarse a la consola de los switches, routers... *colgado* no es lo más adecuado.

Si a todo esto le añadimos que cada vez utilizamos más dispositivos móviles (smartphones, tablets...), que pueden llegar a sustituir a los ordenadores portátiles, resulta muy práctico disponer de un dispositivo que nos facilite el acceso a la consola haciendo uso de los mismos. [Air Console](http://www.get-console.com/airconsole/) es la solución a todos estos inconvenientes.

Se trata de un producto originario de Nueva Zelanda, pero la compra y el envío ha resultado de lo más rápido y sencillo. Realizando el pedido a través de la [tienda online](http://www.get-console.com/shop/) un domingo, el miércoles ya estaba el producto en su destino.

Una vez desembalado, y cargada la batería, lo primero es su configuración. En la caja del producto viene una [guía rápida](http://www.get-console.com/airconsole/files/Airconsole-QuickStart-Wifi-Bluetooth-2.5.pdf) que resulta suficiente para los primeros pasos.

**Air Console** se conecta a la consola del dispositivo y a su vez se comporta como un [AP](http://es.wikipedia.org/wiki/Punto_de_acceso_inal%C3%A1mbrico) al que nos podemos conectar, se trata de un adaptador [RS232](http://es.wikipedia.org/wiki/RS-232) sobre wifi y bluetooth.

Para acceder a la consola desde dispositivos móviles, **Get Console** dispone de aplicaciones para [Android](http://es.wikipedia.org/wiki/Android) ([SerialBot](https://play.google.com/store/apps/details?id=nz.co.cloudstore.serialbot)) y para [iOS](http://es.wikipedia.org/wiki/IOS) ([RapidSSH](https://itunes.apple.com/us/app/rapidssh/id546150309?mt=8), [Get Console](https://itunes.apple.com/us/app/get-console/id412067943?mt=8&ls=1)).

Para los sistemas operativos Windows y Mac OS X, en la propia web de **Get Console** están disponibles los drivers correspondientes.

En caso de utilizar otro sistema operativo, tenemos las siguientes opciones:

1. Se puede usar el navegador web del dispositivo para conectarse al emulador de terminal ([VT100](http://es.wikipedia.org/wiki/VT100)) que facilita como puente **Air Console**. Sólo hay que conectarse a `http://<IP_AIRCONSOLE>/terminal.asp`.

2. Se puede usar un [emulador de terminal](http://es.wikipedia.org/wiki/Emulador_de_terminal) en el dispositivo para conectarse vía [telnet](http://es.wikipedia.org/wiki/Telnet) al puerto 3696 (2167 para serial raw) de **Air Console**. 

3. También se puede usar un cliente [websockets](http://es.wikipedia.org/wiki/WebSocket) para conectarse a **Airconsole**.

4. Finalmente, si la programación es lo tuyo, puedes escribir una aplicación para interactuar con **Air Console** utilizando los [SDKs](http://es.wikipedia.org/wiki/Kit_de_desarrollo_de_software) disponibles en [Get Console](http://www.get-console.com).

Si usas LiNUX, lo mas sencillo es crear un nuevo dispositivo con **socat**:

``` sh
socat PTY,link=/dev/ttyVS0,user=root,group=uucp,mode=660 TCP:<IP_AIRCONSOLE>:3696
```

Ahora solo hemos de usar [screen](http://atas.co/blog/2014/06/18/screen-la-pantalla-indiscreta/) para conectarnos a través del nuevo dispositivo serie virtual creado con **socat**:

``` sh
screen /dev/ttyVS0
```


# Referencias

[Air Console](http://www.get-console.com/airconsole/)

[Socat](http://www.dest-unreach.org/socat/)
