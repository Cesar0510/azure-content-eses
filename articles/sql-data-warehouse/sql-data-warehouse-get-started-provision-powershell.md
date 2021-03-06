<properties
   pageTitle="Crear Almacenamiento de datos SQL mediante el uso de Powershell | Microsoft Azure"
   description="Creación de Almacenamiento de datos SQL con Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/07/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de Almacenamiento de datos SQL con Powershell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

## Requisitos previos
Antes de empezar, asegúrese de que cumple los siguientes requisitos previos:

- **Cuenta de Azure**: consulte [Evaluación gratuita de Azure][] o [Crédito mensual de Azure para suscriptores de Visual Studio][] para crear una cuenta.
- **SQL Server V12 de Azure**: consulte [Creación de un servidor lógico de Base de datos SQL de Azure][] o [Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall][].
- **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server V12 de Azure o consulte [Uso del Portal de Azure para implementar y administrar los recursos de Azure][] para crear uno.
- **PowerShell versión 1.0.3 o posterior**: para comprobar la versión, ejecute **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

> [AZURE.NOTE] La creación de una nueva instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios de Almacenamiento de datos SQL][] para más información sobre los precios.

## Creación de una base de datos de Almacenamiento de datos SQL.
1. Abra Windows PowerShell.
2. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Seleccione la suscripción que desea utilizar para la sesión actual.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Cree la base de datos. En este ejemplo se crea una nueva base de datos denominada "mynewsqldw", con el nivel de objetivo de servicio "DW400", en el servidor llamado "sqldwserver1" que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1".

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Los parámetros necesarios para este cmdlet son los siguientes:

- **RequestedServiceObjectiveName**: la cantidad de DWU solicitada, con formato "DWXXX". DWU representa una asignación de CPU y memoria. Cada valor de DWU representa un aumento lineal en estos recursos. Los valores admitidos son: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: el nombre del Almacenamiento de datos SQL que está creando.
- **ServerName**: el nombre del servidor que se usa para la creación (tiene que ser V12).
- **ResourceGroupName**: el grupo de recursos que está usando. Para obtener los grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
- **Edition**: tiene que establecer la edición como "DataWarehouse" para crear un Almacenamiento de datos SQL.

Para más información sobre las opciones de parámetros, consulte [Create Database (Azure SQL Data Warehouse)][] (Creación de base de datos [Almacenamiento de datos SQL de Azure]). Para la referencia de comandos, consulte [New-AzureRmSqlDatabase][].

## Pasos siguientes
Después de que Almacenamiento de datos SQL finalice el aprovisionamiento, puede intentar [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

Si está interesado en más información sobre cómo administrar Almacenamiento de datos SQL mediante programación, consulte nuestro artículo sobre cómo usar [las API de REST y los cmdlets de PowerShell][].

<!--Image references-->

<!--Article references-->

[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-load-with-bcp.md
[cargar datos de ejemplo]: sql-data-warehouse-get-started-load-sample-databases.md
[las API de REST y los cmdlets de PowerShell]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Cómo instalar y configurar Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Creación de un servidor lógico de Base de datos SQL de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Uso del Portal de Azure para implementar y administrar los recursos de Azure]: ../azure-portal/resource-group-portal.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps
[Precios de Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédito mensual de Azure para suscriptores de Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0608_2016-->