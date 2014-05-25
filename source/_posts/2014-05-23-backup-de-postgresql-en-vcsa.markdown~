---
layout: post
title: "Backup de postgresql en VCSA"
date: 2014-05-23 21:13:51 +0200
comments: true
categories: vmware
---
Cuando se realiza el despliegue de un vCenter con el appliance de VMWare (vCenter Server Appliance 5.X) es interesante (cuando no imprescindible) realizar backups periódicos de la base de datos incorporada (postgresql), si en la instalación se decide hacer uso de la misma, con el fin de poder hacer una recuperación rápida en caso de *accidente*.

En esta entrada se recoge la forma de hacer esta copia de seguridad así como la recuperación correspondiente si llega a ser necesario.

<!-- more -->

## Requisito previo

Es preciso disponer de acceso vía SSH al vCenter, para habilitarlo:

1. Acceder a la consola web del VCSA (https://VCSA:5480)
2. En la pestaña *Admin*, pulsar *Toggle SSH login* para permitir acceder al servidor a través de SSH

## Copia

**1.** Acceder al vCenter e ir al directorio donde se encuentran los binarios de postgresql:

``` sh
cd /opt/vmware/vpostgres/1.0/bin
```

**2.** Para ver la configuración de la base de datos ejecutar el comando:

``` sh
cat /etc/vmware-vpx/embedded_db.cfg
```

**3.** Parar el servicio del vCenter:

``` sh
service vmware-vpxd stop
```

**4.** Realizar la copia:

``` sh
./pg_dump EMB_DB_INSTANCE -U EMB_DB_USER -Fp -c > VCDBBackupFile
```

Donde *EMB_DB_INSTANCE* y *EMB_DB_USER* se sustituyen por los valores obtenidos en el punto **[2]**. *VCDBBackupFile* es el nombre del fichero donde se guardará el backup.

> ./pg_dump VCDB -U postgres -Fp -c > /tmp/VCDBackUp

**5.** Arrancar el servicio del vCenter:

``` sh
service vmware-vpxd start
```

## Recuperación

**1.** Acceder al vCenter e ir al directorio donde se encuentran los binarios de postgresql:

``` sh
cd /opt/vmware/vpostgres/1.0/bin
```

**2.** Para ver la configuración de la base de datos ejecutar el comando:

``` sh
cat /etc/vmware-vpx/embedded_db.cfg
```

**3.** Parar el servicio del vCenter:

``` sh
service vmware-vpxd stop
```

**4.** Restaurar la copia:

``` sh
PGPASSWORD='EMB_DB_PASSWORD' ./psql -d EMB_DB_INSTANCE -Upostgres -f VCDBBackupFile
```

Donde *PGPASSWORD* y *EMB_DB_INSTANCE* se sustituyen por los valores obtenidos en el punto **[2]**. *VCDBBackupFile* es el nombre del fichero donde se ha guardado previamente el backup.

Utilizar comillas simples **[']** con la password tal como aparecece en el fichero de configuración del punto **[2]**.

> PGPASSWORD=`'g<T4EuybGsA=kG$G'` ./psql -d VCDB -U postgres -f /tmp/VCDBackUp

**5.** Arrancar el servicio del vCenter:

``` sh
service vmware-vpxd start
```

## Referencias

[Backing up and restoring the vCenter Server Appliance (vPostgres) database (2034505)](http://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2034505)

[Enable or Disable SSH Administrator Login on the VMware vCenter Server Appliance](http://pubs.vmware.com/vsphere-55/index.jsp?topic=%2Fcom.vmware.vsphere.vcenterhost.doc%2FGUID-8DC793FF-1E00-43A1-B85C-070414B9F9B0.html&resultof=%22Enable%22%20%22enabl%22%20%22Disable%22%20%22disabl%22%20%22SSH%22%20%22ssh%22)
