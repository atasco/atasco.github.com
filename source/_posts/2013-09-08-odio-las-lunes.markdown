---
layout: post
title: "Odio las LUNes!"
date: 2013-09-08 20:30
comments: true
categories: vmware
---
Julio y agosto, meses de vacaciones, momento ideal para *romper* la infraestructura virtual con las actualizaciones anuales. Este año toca **vSpehere 5.1** (si esperamos un poco caemos en la 5.5).

Tras la pertinente revisión de compatibilidad de hardware y la toma de decisiones previas (que implican cambio del vCenter, nos pasamos al nuevo appliance bajo LiNUX [VCSA](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2007619), asignación de nuevas LUN…) se procede a la instalación del nuevo vCenter y la migración de los ESX 4.1 U2.
<!-- more -->

**¡Sorpresa!** Al disponer de diferente hardware no es posible actualizar a 5.1 parte de los blades HP BL460c (los G1) que forman parte de la infraestructura, por lo que se decide mantener un entorno mixto con servidores ESXi 5.0 y 5.1.

Esto supone reconsiderar la planificación inicial y separar los ESXi por versión en dos cluster (planificado inicialmente para mantener dos entornos independientes, uno para servidores de desarrollo y otro para los de producción) en vez de mantener un único cluster con un pool de recursos dedicado a las VM de desarrollo.

Una vez realizada la migración, se observa que el rendimiento en acceso a disco de las VM en los ESXi 5.0 no se corresponde con las VM en los ESXi 5.1. 

Revisando la configuración se observa que la politica de selección de caminos en las LUNes de la cabina (EMC VNX 5500 que soporta el protocolo ALUA) para los ESXi 5.0 no es la que tenemos configurada habitualmente. Aparece como FIXED cuando debería estar como ROUND ROBIN.

Con el comando:

{% codeblock %}
esxcli storage nmp satp list
{% endcodeblock %}

Obtenemos la política de selección de caminos (PSP) actual. 

Por ejemplo, en un ESXi 5.0:

{% codeblock %}
Name                 Default PSP    Description
-------------------  -------------  -------------------------------------------------------   
VMW_SATP_ALUA_CX     VMW_PSP_FIXED     Supports EMC CX that use the ALUA protocol             
VMW_SATP_ALUA        VMW_PSP_MRU    Supports non-specific arrays that use the ALUA protocol
VMW_SATP_CX          VMW_PSP_MRU    Supports EMC CX that do not use the ALUA protocol      
VMW_SATP_MSA         VMW_PSP_MRU    Placeholder (plugin not loaded)                        
VMW_SATP_DEFAULT_AP  VMW_PSP_MRU    Placeholder (plugin not loaded)                        
VMW_SATP_SVC         VMW_PSP_FIXED  Placeholder (plugin not loaded)                        
VMW_SATP_EQL         VMW_PSP_FIXED  Placeholder (plugin not loaded)                        
VMW_SATP_INV         VMW_PSP_FIXED  Placeholder (plugin not loaded)                        
VMW_SATP_EVA         VMW_PSP_FIXED  Placeholder (plugin not loaded)                        
VMW_SATP_SYMM        VMW_PSP_RR     Placeholder (plugin not loaded)                        
VMW_SATP_LSI         VMW_PSP_MRU    Placeholder (plugin not loaded)                        
VMW_SATP_DEFAULT_AA  VMW_PSP_FIXED  Supports non-specific active/active arrays             
VMW_SATP_LOCAL       VMW_PSP_FIXED  Supports direct attached devices
{% endcodeblock %}

Cambiar la PSP por defecto es sencillo, solo hay que ejecutar el comando:

{% codeblock %}
esxcli storage nmp satp set --default-psp=policy --satp=your_SATP_name
{% endcodeblock %}

Para cambiar el modo FIXED por ROUND ROBIN en la cabina VNX 3000 que soporta ALUA, el comando a ejecutar es:

{% codeblock %}
esxcli storage nmp satp set --default-psp=VMW_PSP_RR --satp=VMW_SATP_ALUA_CX
{% endcodeblock %}

A partir de ahora las nuevas LUN de la cabina que se presenten a los ESXi 5.0 tendrán como PSP Round Robin, pero las que se encuentran ya presentadas mantendrán la política Fixed.

Se puede cambiar la politica selección de camino reiniciando el ESXi para hacerlo de forma global para todas las LUN que se presebtan al mismo, o bien de forma individual para cada LUN.

Para conseguir que una LUN pase a utilizar un PSP diferente, ejecutamos el comando siguiente en el ESXi afectado:

{% codeblock %}
esxcli storage nmp device set -d NAAID -psp=policy
{% endcodeblock %}

Es necesario identificar previamente el NAAID de la LUN afectada. Para ello se puede hacer uso del cliente gráfico (viclient o web) o con el comando:

{% codeblock %}
esxcli storage nmp device list
{% endcodeblock %}

Una vez realizado el cambio se verifica el mismo con:

{% codeblock %}
esxcli storage nmp device list -d NAAID
{% endcodeblock %}

Ejemplo:

{% codeblock %}
esxcli storage nmp device set -d naa.6006016055711d00cff95e65664ee011 --psp=VMW_PSP_MRU
{% endcodeblock %}

Una vez realizados los cambios, con todos los ESXi utilizando la misma política de selección de caminos para las LUNes, el rendimiento de los hosts con versión 5.0 se estabiliza y adquiere valores similares a los que tienen la versión 5.1.

## Resumiendo

### Cambio de la PSP por defecto

[Referencia](http://kb.vmware.com/selfservice/search.do?cmd=displayKC&docType=kc&docTypeID=DT_KB_1_1&externalId=1017760)

** 1. Mirar la PSP (Path Selection Policy)actual: **

> esxcli storage nmp satp list

** 2. Cambiar la PSP por defecto: **

> esxcli storage nmp satp set --default-psp=policy --satp=your_SATP_name

** 3. Verificar el cambio: **

> esxcli storage nmp satp list

### Cambio de la PSP por LUN

[Referencia](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=1036189)

** 1. Identificar LUN ID (NAA ID) **

1.1. En VI Client (o web).

1.2. Vía comando:

> esxcli storage nmp device list

** 2. Cambiar la PSP: ** 

> esxcli storage nmp device set -d NAAID -psp=policy

** 3. Verificar el cambio: **

> esxcli storage nmp device list -d NAAID