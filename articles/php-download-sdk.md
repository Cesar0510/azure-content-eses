<properties
	pageTitle="Descarga del SDK de Azure para PHP"
	description="Obtenga información acerca de cómo descargar e instalar el SDK de Azure para PHP."
	documentationCenter="php"
	services="app-service\web"
	authors="allclark"
	manager="douge"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="allclark;yaqiyang"/>

#Descarga del SDK de Azure para PHP

## Información general

El SDK de Azure para PHP incluye componentes que le permiten desarrollar, implementar y administrar aplicaciones PHP para Azure. Específicamente, el SDK de Azure para PHP incluye lo siguiente:

* **Las bibliotecas de clientes PHP para Azure**. Estas bibliotecas de clases proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube.  
* **La interfaz de la línea de comandos de Azure para Mac, Linux y Windows (CLIC de Azure).** Este es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Sitios web Azure y Red virtual de Azure. La interfaz de la línea de comandos de Azure funciona en cualquier plataforma, incluidas las plataformas Mac, Linux y Windows.
* **Azure PowerShell (solo Windows)**. Este es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure, como Servicios en la nube y Máquinas virtuales.
* **Los emuladores de Azure (solo Windows)**. Los emuladores de proceso y almacenamiento son emuladores locales de los servicios en la nube y los servicios de administración de datos que le permiten probar localmente una aplicación. Los emuladores de Azure solo se ejecutan en Windows.

Las secciones que vienen a continuación describen cómo descargar e instalar los componentes descritos.

En las instrucciones de este tema se asume que tiene instalado [PHP][install-php].

> [AZURE.NOTE] Debe tener instalado PHP 5.5 o superior con el fin de utilizar las bibliotecas de clientes PHP para Azure.

##Bibliotecas de clientes PHP para Azure

Las bibliotecas de clientes PHP para Azure proporcionan una interfaz para tener acceso a características de Azure, como los servicios de administración de datos y los servicios en la nube, desde cualquier sistema operativo. Estas bibliotecas se pueden instalar mediante el Compositor.

Si desea obtener información acerca del uso de las bibliotecas de clientes PHP para Azure, consulte [Uso del servicio BLOB][blob-service], [Uso del servicio Tabla][table-service] y [Uso del servicio Cola][queue-service].

###Instalación mediante el compositor

1. [Instalación de Git][install-git].


	> [AZURE.NOTE] En Windows, también tendrá que agregar el archivo ejecutable Git a la variable de entorno PATH.

2. Cree un archivo con el nombre **composer.json** en la raíz del proyecto y agréguele el código siguiente:

        {
			"require": {
				"microsoft/windowsazure": "^0.4"
			}
        }

3. Descargue **[composer.phar][composer-phar]** en la raíz del proyecto.

4. Abra un símbolo del sistema y ejecute esto en la raíz del proyecto

		php composer.phar install

##Azure PowerShell y emuladores de Azure

Azure PowerShell es un conjunto de cmdlets de PowerShell para implementar y administrar servicios de Azure (como Servicios en la nube y Máquinas virtuales). Los emuladores de Azure son emuladores de servicios en la nube y servicios de administración de datos que le permiten probar localmente una aplicación. Estos componentes solo son compatibles con Windows.

La manera recomendada de instalar Azure PowerShell y los emuladores de Azure es utilizar el [instalador de plataforma web de Microsoft][download-wpi]. Observe que también puede elegir instalar otros componentes de desarrollo, como PHP, SQL Server, los controladores de Microsoft para SQL Server para PHP y WebMatrix.

Para obtener más información acerca del uso de Azure PowerShell, consulte [Cómo instalar y configurar Azure PowerShell][powershell-tools].

##Azure CLI

La interfaz de la línea de comandos de Azure es un conjunto de herramientas que sirve para implementar y administrar servicios de Azure, como Sitios web Azure y Red virtual de Azure. Para obtener información acerca de cómo instalar la CLI de Azure, consulte [Instalar la CLI de Azure](xplat-cli-install.md).

## Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

<!---HONumber=AcomDC_0601_2016-->