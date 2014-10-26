---
layout: post
title: "Enlaces troncales Cisco Catalyst - ESX(i)"
date: 2014-09-20 20:40
comments: true
categories: network vmware
---
Antes de realizar la instalación de un servidor ESX(i) en la infraestructura virtual es preciso hacer un análisis previo para determinar las necesidades. Dentro de estás necesidades la conexión del nuevo servidor en la red es una de las más críticas e importantes. 

Existe mucha documentación al respecto en la [base de datos de conocimiento de VMWare](http://kb.vmware.com/selfservice/microsites/) así como en sus [comunidades](https://communities.vmware.com/community), pero en este artículo vamos a centrarnos en la conexión del servidor ESX(i) con los switches (Cisco) desplegados en la red.

<!-- more -->

Antes de comenzar con los comandos para la configuración de los switches físico (Cisco Catalyst) y virtual (VMWare vSwitch0), vamos a ver en el siguiente diagrama el diseño final que queremos alcanzar.

![Diagrama de conexión Cisco - ESX](/images/posts/trunk_cisco_esx_1.png)

VLAN | Descripción
:--- | -----------
60   | Red de Gestión (**management network**)
70   | Red de VMotion (**VMotion network**)

# Configuración Cisco Catalyst

Configuramos los puertos que intervienen en el EtherChannel. 

```
enable
configure terminal
interface GigabitEthernet1/1
description ESX_LAB-NIC0
channel-group 1 mode on
interface GigabitEthernet1/2
description ESX_LAB-NIC1
channel-group 1 mode on
```

* Comando `enable`: pasamos al modo privilegiado.
* Comando `configure terminal`: entramos en modo configuración.
* Comando `interface GigabitEthernet1/1`: puerto del switch que vamos a configurar.
* Comando `description ESX_LAB-NIC0`: descripción del puerto.
* Comando `channel-group 1 mode on`: asignamos el puerto al grupo 1 y configuramos el modo incondicional.

Configuramos la interfaz lógica, Port-Channel, asociada a la agrupación de puertos del EtherChannel:

```
enable
configure terminal
interface port-channel 1
witchport trunk encapsulation dot1q
switchport trunk allowed vlan 60,70
switchport mode trunk
switchport nonegotiate
spanning-tree portfast trunk
```

* Comando `interface port-channel 1`: Port-Channel que vamos a configurar.
* Comando `switchport trunk encapsulation dot1q`: configuramos como protocolo de encapsulación el estándard del IEEE 802.1q (dot1q) ya que los ESX(i) sólo soportan este protocolo.
* Comando `switchport trunk allowed vlan 60,70`: configuramos las VLANs permitidas en el enlace troncal.
* Comando `switchport mode trunk`: habilitamos el enlace troncal. 
* Comando `switchport nonegotiate`: como ESX(i) no soporta DTP (Dynamic Trunking Protocol) lo deshabilitamos.
* Comando `spanning-tree portfast trunk`: habilitamos PortFast en el interface cuando se encuentra en modo troncal.

Verificamos la configuración y grabamos los cambios:

```
show interfaces trunk
show etherchannel summary
copy running-config startup-config
```

> En un primer momento, cuando el vSwitch del servidor no está configurado, si tenemos varios puertos en el EtherChannel activos no es posible llegar en remoto a la red de gestión (**management network**). Para lograrlo lo más sencillo es dejar sólo un puerto del EtherChannel activo.

# Configuración vSwitch0 ESX(i)

Si el nuevo ESX(i) va a formar parte de una infraestructura virtual previa donde ya hay desplegado un vCenter, podemos realizar la configuración del virtual switch a través del **web client**:

![vSwitch - ESX](/images/posts/vswitch_vcenter.png)

1. Cambiamos la configuración de balanceo a `Route based on IP hash`.
2. Ponemos activos los adaptadores (vmnic) que estan en **standby**.

En caso contrario (o si nos encontramos más cómodos con el cli), podemos configurar el switch virtual a través de la línea de comandos, accediendo vía SSH al servidor ESX(i):

```
esxcfg-vswitch -v 60 -p "Management Network" vSwitch0
```

# Referencias

[Sample switch port configuration for VLAN and TRUNK MODE](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1006628)
