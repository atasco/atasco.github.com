---
layout: post
title: "Parcheando ESXi 5.X sin VUM"
date: 2013-11-28 18:30
comments: true
categories: vmware
---
En ocasiones, aplicar parches a los servidores **ESXi 5.X** no es sencillo cuando no se dispone de un vCenter (con vSphere Update Manager, VUM), tal como ocurre en los entornos de laboratorio formados por ESXi independientes.

Vamos a ver como es posible aplicar los parches a los servidores ESXi de manera sencilla siguiendo los pasos siguientes.

<!-- more -->

##Requisito previo

Es necesario tener acceso vía SSH al servidor, para habilitarlo tenemos dos opciones:

###A través del vSphere Client

1. Abrimos sesión en el servidor ESXi con el cliente
2. Seleccionamos la pestaña *Configuration* y pinchamos en *Security Profile*
3. Pinchar en *Properties* en la esquina superior derecha y seleccionar el servicio *SSH* pulsando el botón *Options*
4. Desde aqui se puede arrancar el servicio y seleccionar las opciones de arranque del mismo

###A través de la consola del servidor ESXi

1. Pulsar *F2* para acceder al menu de configuración
2. Seleccionar *Troubleshooting Options*
3. Desde el menu de opciones seleccionar *Enable SSH*

##Dejar los parches en un Datastore accesible desde el servidor ESXi

Una vez que se descargan los parches desde la [web de vmware](http://www.vmware.com/patch/download), hay que dejar accesibles para el servidor ESXi al que se van a aplicar los mismos.

> Es conveniente crear una carpeta en el datastore para dejarlos y poder aplicarlos posteriormente.

##Aplicar los parches

Antes de aplicar los parches hay que apagar de forma ordenada todas las máquinas virtuales que están en el servidor.

Conectarse por SSH al servidor ESXi y aplicar los parches mediante el comando:

{% codeblock %}
esxcli software vib update /vmfs/volumes/parches/<your-upgrade-bundle.zip>
{% endcodeblock %}

Una vez que finaliza la aplicación del parche, aparecerá un mensaje indicándolo y solicitando el reinicio del servidor.


##Referencias
[Using ESXi Shell in ESXi 5.0 and 5.1](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2004746)

[How to patch ESXi 5](http://communities.vmware.com/thread/328758)