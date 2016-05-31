

#1. Servidor de actualizaciones WSUS

El mantenimiento de las actualizaciones de software es una necesidad constante. 
Si no mantienen los sistemas actualizados:
* Un atacante puede aprovechar las vulnerabilidades.
* Aumenta el tiempo de inactividad de los PC's comprometidos.
* La información de la empresa puede verse comprometida.

El servidor WSUS almacena y distribuye las actualizaciones de software MS, 
consiguiendo:
* Reducción del consumo del ancho de banda WAN.
* Mejora de los tiempos de actualización global.
* Mayor control de las actualizaciones que se desean.

Los servidores de instalacion son la solución para ayudar a distribuir las actualizaciones 
Funcionamiento de WSUS:
* Servidor pone las actualizaciones disponibles a los clientes.
* El administrador del servidor aprueba y prioriza las actualizaciones.
* El cliente se conecta a WSUS e instala los paquetes usando Windows update.

Resumen:
* WSUS proporciona control a la hora de aprobar y distribuir actualizaciones 
de Microsoft entre sus ordenadores clientes.
* Un servidor WSUS puede copiar actualizaciones desde Microsoft y almacenarlas 
localmente. Entonces, los ordenadores clientes descargarán las actualizaciones 
desde su servidor WSUS en vez de descargarlas desde Microsoft a través de Internet.
* Para soportar organizaciones con muchas oficinas, los servidores WSUS de descarga 
pueden sincronizar actualizaciones, aprobaciones y opciones de configuración de 
los servidores WSUS de subida.
* WSUS también requiere IIS.

#2. Servidor WSUS

Tener en cuenta que:
* NO es necesario tener un Active Directory para montar el servicio WSUS.
* Necesitamos 1 servidor WSUS por cada 10 PC's.
* Servidor WSUS se conecta vía HTTP/HTTPS con el exterior.
* IIS en WSUS para que se conecten los clientes.
* Se requiere al menos 6GB de disco duro parfa almacenar las actualizaciones.
* Tendremos que hacer copias de seguridad de la BBDD de WSUS periódicamente.
* En caso de fallo disponemos de 1 semana para reemplazar el servidor.

Configurar servidor:
* Fuente: Microsoft u otros servidor WSUS.
* Base de datos en C:\WSUS\UpdateServicesDbFiles\SUSDB.mdf o MSQL-Server.
* Minimizar descargas: Selección de idiomas, selección de productos para actualizar.

##2.1 Instalación del servidor

Enlace de interés sobre [Instalación y puesta en marcha Servidor de actualizaciones (WSUS I)](http://cerowarnings.blogspot.com.es/2011/11/servidor-de-actualizaciones-wsus.html) 

* La forma más sencilla para instalar el software WSUS es usar la propia 
herramienta de Administrar del servidor Windows Server.
    * `Administrar el servidor -> Roles -> Agregar roles -> WSUS` 

> Resumen de los pasos para la instalación del servidor WSUS.
> 
> * Descarga desde www.microsoft.com/WSUS.
> * Inicio > Herr. Admin. > MS Win Server Update Services- 
> * Consola Up. Serv.
> * Detalles > sincronizar hora (Esto puede tardar minutos u horas).

##2.2 Configuración del servidor

Consideraciones a tener en cuanta a la hora de configurar servidor:
* Podremos usar como fuente origen de las actualizaciones a los servidor
 de Microsoft u otros servidores WSUS.
* La base de datos que guarda la información, la podemos tener en 
C:\WSUS\UpdateServicesDbFiles\SUSDB.mdf o montar un MSQL-Server.
* Para minimizar descargas es conveniente hacer una selección de idiomas y de productos para actualizar.

Pasos para la configuración del servidor WSUS:
* Editar opciones: `Updates Services > Opciones`. 
    * Definir proxy, productos a actualizar, idiomas, etc.
* Aprobar/descartar actualizaciones en el servidor.
* Ver informes.

#3. Cliente WSUS

Tener en cuenta que:
* Sistemas operativos clientes son: W2K, WXP, WVista, WS2K3 y WS2K8.
* Los clientes se conectan vía HTTP con el servidor WSUS.
* Cliente Windows Update usa la firma digital y HASH (SHA1) para comprobar 
la autenticidad. En WXP y W2K se usa "cliente de actualizaciones automáticas".
* Se pueden usar directivas de grupo para distribuir la configuración de 
la organización ( `Conf Eq. > Direct > Plant. Admin. > Componen > Win Update`)

Parámetros de configuración en el cliente:
* Máquina Servidor WSUS
* Frecuencia, notificaciones, grupo asignado.
* Reinicio automático / confirmación / retrasar
* Administración de energía: Iniciar PC si está apagado para ejecutar actualización.


Dos modos de configurar equipos clientes.
* Sin PDC: Configurar Windows Update de cada cliente de forma local.
* Con PDC: Asignar servidor WSUS a los equipos del dominio mediante directivas de grupo.

Para configurar los clientes WSUS tenemos los siguientes métodos:
* Política de Grupo (AD DS). Enlace de interés [Configuración de clientes y aprobación de actualizaciones WSUS](http://cerowarnings.blogspot.com.es/2011/11/servidor-de-actualizaciones-wsus-ii.html)  
* Política de Grupo (Local). Enlace de interés [Configuración de cliente WSUS con o sin Active Directory](http://cosiis.com/blog/archives/69) 
* Registro de Windows. Enlace de interés [Script para modificar el registro de Windows](http://servidorespararedes.blogspot.com.es/2008/10/configuracion-de-cliente-wsus-por.html)

> Comandos para ejecutar en el cliente como administrador:
>
> * `gpupdate /force`: Esto fuerza a que se apliquen los cambios realizados en las directivas
> * `wuauclt.exe /detectnow`: Este trata de conectar con el servidor y registra el equipo cliente en WSUS.

#4. Auditar los procesos

##4.1 Auditar desde el servidor

Para realizar el diagnóstico de WSUS tenemos 3 archivos de registro:
* Registro de eventos de Aplicación (http://support.microsoft.com)
* `C:\Program Files\ Update Services\Log Files\ Change.txt`
* `C:\Program Files\ Update Services\Log Files\ SoftwareDistribution.txt`

¿Se instala bien el software en los clientes? Herramientas para auditar 
las actualizaciones desde el servidor:
* (A) Consola Windows Update > Nodo Equipos e Informes.
* (B) MS System Center Conf. Manager 2007. Se ajusta a AD (www.microsoft.com/smserver)
* (C) MS Baseline Security Analyzer (MBSA). Escaneo de la red completa para auditar (www.microsoft.com/mbsa).

##4.2 Diagnosticar desde el cliente

Diagnosticar el cliente Windows Update.
* Examinar el archivo %SystemRoot%\WindowsUpdate.log (http://support.microsoft.com/kb/902093).
* Navegador URL http://<WSUSServerName>/iuident.cab
* Herramienta Rsop.msc > Conf. Equipo > Plantillas Admin. > Comp. Wind > Win Update.
* Más información en Reg. de Apps > MS > Win > WinUpdateClient > Operational.log.
* Para ver las actualizaciones instaladas en un PC WinVista o W2k8: Inicio > Panel de Control > Sistema y Mantenimiento > Windows Update > Ver historial de actualizaciones.

Si se cambia la configuración, reiniciar el servicio Windows Update en el cliente:
* `net stop wuauserv`, Para el servicio de Windows Update.
* `net start wuauserv`, Inicia el servicio de Windows Update.

Esperar 10 minutos y Win Update intentará conectar con el servidor. 
Para que el cliente consulte al servidor hacemos: `wuauclt /a`

##4.3 Eliminar actualizaciones

Cuando una actualización ocasiona problemas de compatibilidad, éstas se
pueden desinstalar. Para ellos hacemos:
* Usar Windows Update para ver el historial de actualizaciones. 
Consultar los detalles de cada una para identificar dónde puede estar el problema. Anotar el número KB.
* Inicio -> Panel de control -> Programas -> desinstalar programas.
* Tareas -> ver actualizaciones instaladas.
* Selecionar por el número KB y desinstalarla.
* Siga las instrucciones que aparecen y reinicie si fuese necesario.

> NOTA: 
>
> Si eliminar la actualización NO resuelve el problema, debe volver a aplicar esa actualización. 
> A continuación, CONTACTE con el desarrollador de la aplicación 
(en caso de incompatibilidad con el programa) o con su representante de soporte 
de Microsoft para informarle de la incompatibilidad.
