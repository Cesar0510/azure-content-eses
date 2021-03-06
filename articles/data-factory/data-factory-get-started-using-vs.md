<properties 
	pageTitle="Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio" 
	description="En este tutorial, creará una canalización de la factoría de datos de Azure con una actividad de copia mediante Visual Studio." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/16/2016" 
	ms.author="spelluru"/>

# Tutorial: Crear una canalización con la actividad de copia mediante Visual Studio
> [AZURE.SELECTOR]
- [Información general del tutorial](data-factory-get-started.md)
- [Uso del Editor de Data Factory](data-factory-get-started-using-editor.md).
- [Uso de Visual Studio](data-factory-get-started-using-vs.md)
- [Uso de PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Uso del Asistente para copia](data-factory-copy-data-wizard-tutorial.md)

En este tutorial hará lo siguiente mediante Visual Studio 2013:

1. Cree dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlinkedService1**. AzureStorageLinkedService1 vincula un almacenamiento de Azure y AzureSqlLinkedService1 vincula una Base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactoryVS**. Los datos de entrada para la canalización se encuentran en un contenedor de blob en el almacenamiento de blobs de Azure y los datos de salida se almacenarán en una tabla en la base de datos SQL de Azure. Por lo tanto, agregue estos almacenes de datos como servicios vinculados en la factoría de datos.
2. Creará dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida. También especificará otras propiedades como la estructura de los datos, la disponibilidad de los datos, etc.
3. Cree una canalización denominada **ADFTutorialPipeline** en ADFTutorialDataFactoryVS. La canalización dispondrá de una opción para la **actividad de copia** que copia datos de entrada del blob de Azure a la tabla de salida de Azure SQL. La actividad de copia realiza el movimiento de datos en Data Factory de Azure y la actividad funciona con un servicio disponible generalmente que puede copiar datos entre varios almacenes de datos de forma segura, confiable y escalable. Para más información acerca de la actividad de copia, consulte el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md). 
4. Cree una factoría de datos e implemente servicios vinculados, tablas y la canalización.    

## Requisitos previos

1. **Debe** leer la sección [Tutorial Overview](data-factory-get-started.md) del artículo y completar los pasos de los requisitos previos antes de continuar.
2. Debe ser **administrador de la suscripción de Azure** para poder publicar entidades de Data Factory en Data Factory de Azure. Esta es una limitación en este momento. Se le informará tan pronto como cambie este requisito. 
3. Debe tener lo siguiente instalado en el equipo: 
	- Visual Studio 2013 o Visual Studio 2015.
	- Descargue el SDK de Azure para Visual Studio 2013 o Visual Studio 2015. Vaya a la [página Descargas de Azure](https://azure.microsoft.com/downloads/) y haga clic en **VS 2013** o **VS 2015** en la sección **.NET**.
	- Descargue el último complemento de Data Factory de Azure para Visual Studio : [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Si usa Visual Studio 2013, también puede actualizar el complemento mediante el proceso siguiente: haga clic en el menú **Herramientas** -> **Extensiones y actualizaciones** -> **En línea** -> **Galería de Visual Studio** -> **Herramientas de Factoría de datos de Microsoft Azure para Visual Studio** -> **Actualizar**. 
 


## Creación de un proyecto de Visual Studio 
1. Inicie **Visual Studio 2013**. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Debería ver el cuadro de diálogo **Nuevo proyecto**.  
2. En el cuadro de diálogo **Nuevo proyecto**, seleccione la plantilla **DataFactory** y haga clic en **Proyecto vacío de factoría de datos**. Si no ve la plantilla DataFactory, cierre Visual Studio, instale el SDK de Azure para Visual Studio 2013 y vuelva a abrir Visual Studio.  

	![Cuadro de diálogo Nuevo proyecto](./media/data-factory-get-started-using-vs/new-project-dialog.png)

3. Escriba un **nombre** para el proyecto, la **ubicación** y un nombre para la **solución**; a continuación, haga clic en **Aceptar**.

	![Explorador de soluciones](./media/data-factory-get-started-using-vs/solution-explorer.png)

## Crear servicios vinculados
Los servicios vinculados vinculan almacenes de datos o servicios de proceso con una factoría de datos de Azure. Un almacén de datos puede ser un almacenamiento de Azure, una base de datos SQL de Azure o una base de datos SQL Server local.

En este paso, creará dos servicios vinculados: **AzureStorageLinkedService1** y **AzureSqlLinkedService1**. El servicio vinculado AzureStorageLinkedService1 vincula una cuenta de almacenamiento de Azure y AzureSqlLinkedService vincula una base de datos SQL de Azure a la factoría de datos: **ADFTutorialDataFactory**.

### Creación del servicio vinculado de Almacenamiento de Azure

4. Haga clic con el botón derecho en **Servicios vinculados**en el Explorador de soluciones, seleccione **Agregar** y haga clic en **Nuevo elemento**.      
5. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Servicio vinculado de Almacenamiento de Azure** en la lista y haga clic en **Agregar**. 

	![Nuevo servicio vinculado](./media/data-factory-get-started-using-vs/new-linked-service-dialog.png)
 
3. Reemplace **accountname** y **accountkey** por el nombre de la cuenta de almacenamiento de Azure y su clave.

	![Servicio vinculado de Almacenamiento de Azure](./media/data-factory-get-started-using-vs/azure-storage-linked-service.png)

4. Guarde el archivo **AzureStorageLinkedService1.json**.

### Cree el servicio vinculado SQL de Azure.

5. Haga doble clic con el botón derecho en el nodo **Servicios vinculados** en el **Explorador de soluciones** de nuevo, apunte a **Agregar** y haga clic en **Nuevo elemento**. 
6. Esta vez, seleccione **Servicios vinculados de SQL Azure** y haga clic en **Agregar**. 
7. En el archivo **AzureSqlLinkedService1.json** reemplace **servername**, **databasename**, ****username@servername** y **password** por los nombres del servidor de SQL Azure, la base de datos, la cuenta de usuario y la contraseña.
8.  Guarde el archivo **AzureSqlLinkedService1.json**. 


## Creación de conjuntos de datos
En el paso anterior, creó los servicios vinculados **AzureStorageLinkedService1** y **AzureSqlLinkedService1** para vincular una cuenta de Almacenamiento de Azure y una base de datos SQL de Azure con la factoría de datos: **ADFTutorialDataFactory**. En este paso, definirá dos tablas de factoría de datos: **EmpTableFromBlob** y **EmpSQLTable**, que representan los datos de entrada y salida que se almacenan en los almacenes de datos a los que hacen referencia AzureStorageLinkedService1 y AzureSqlLinkedService1 respectivamente. Para EmpTableFromBlob, especificará el contenedor de blob que contiene un blob con los datos de origen y para EmpSQLTable, especificará la tabla SQL que se almacenará los datos de salida.

### Creación de un conjunto de datos de entrada

9. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.
10. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **Blob de Azure** y haga clic en **Agregar**.   
10. Reemplace el texto JSON con el texto siguiente y guarde el archivo **AzureBlobLocation1.json**. 

		{
		  "name": "EmpTableFromBlob",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureBlob",
		    "linkedServiceName": "AzureStorageLinkedService1",
		    "typeProperties": {
		      "folderPath": "adftutorial/",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

### Creación del conjunto de datos de salida

11. Haga clic con el botón derecho en **Tablas** en el **Explorador de soluciones** de nuevo, seleccione **Agregar** y haga clic en **Nuevo elemento**.
12. En el cuadro de diálogo **Agregar nuevo elemento**, seleccione **SQL de Azure** y haga clic en **Agregar**. 
13. Reemplace el texto JSON con el JSON siguiente y guarde el archivo **AzureSqlTableLocation1.json**.

		{
		  "name": "EmpSQLTable",
		  "properties": {
		    "structure": [
		      {
		        "name": "FirstName",
		        "type": "String"
		      },
		      {
		        "name": "LastName",
		        "type": "String"
		      }
		    ],
		    "type": "AzureSqlTable",
		    "linkedServiceName": "AzureSqlLinkedService1",
		    "typeProperties": {
		      "tableName": "emp"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

## Creación de una canalización 
Ya ha creado las tablas y los servicios vinculados de entrada/salida. Ahora, va a crear una canalización con una **actividad de copia** para copiar datos del blob de Azure a Base de datos SQL de Azure.


1. Haga clic con el botón derecho en **Canalizaciones** en el **Explorador de soluciones**, seleccione **Agregar** y haga clic en **Nuevo elemento**.  
15. Seleccione **Copiar canalización de datos** en el cuadro de diálogo **Agregar nuevo elemento** y haga clic en **Agregar**. 
16. Reemplace el JSON por el siguiente JSON y guarde el archivo **CopyActivity1.json**.
			
		{
		  "name": "ADFTutorialPipeline",
		  "properties": {
		    "description": "Copy data from a blob to Azure SQL table",
		    "activities": [
		      {
		        "name": "CopyFromBlobToSQL",
		        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "EmpTableFromBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "EmpSQLTable"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "BlobSource"
		          },
		          "sink": {
		            "type": "SqlSink",
		            "writeBatchSize": 10000,
		            "writeBatchTimeout": "60:00:00"
		          }
		        },
		        "Policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "NewestFirst",
		          "style": "StartOfInterval",
		          "retry": 0,
		          "timeout": "01:00:00"
		        }
		      }
		    ],
		    "start": "2015-07-12T00:00:00Z",
		    "end": "2015-07-13T00:00:00Z",
		    "isPaused": false
		  }
		}

## Publicación e implementación de las entidades de Factoría de datos
  
18. Haga clic con el botón derecho en el proyecto en el Explorador de soluciones y haga clic en **Publicar**. 
19. Si ve el cuadro de diálogo **Iniciar sesión en tu cuenta Microsoft**, escriba sus credenciales para la cuenta que tiene la suscripción de Azure y haga clic en **Iniciar sesión**.
20. Debería ver el siguiente cuadro de diálogo:

	![Cuadro de diálogo Publicar](./media/data-factory-get-started-using-vs/publish.png)

21. En la página Configurar Data Factory, haga lo siguiente:
	1. Seleccione la opción **Crear la nueva factoría de datos**.
	2. Escriba **VSTutorialFactory** para **Nombre**.  
	
		> [AZURE.NOTE]  
		El nombre del generador de datos de Azure debe ser único global. Si recibe un error sobre el nombre de la factoría de datos cuando se publica, cámbielo (por ejemplo, sunombreVSTutorialFactory) e intente publicar de nuevo. Consulte el tema [Factoría de datos: reglas de nomenclatura](data-factory-naming-rules.md) para las reglas de nomenclatura para los artefactos de Factoría de datos.
		
	3. Seleccione la suscripción correcta en el campo **Suscripción**.
	4. Seleccione el **grupo de recursos** para la factoría de datos que se va a crear. 
	5. Seleccione la **Región** de la factoría de datos. 
	6. Haga clic en **Siguiente** para cambiar a la página **Publicar elementos**. 
23. En la página **Publicar elementos**, asegúrese de que todas las factorías de datos están seleccionadas y haga clic en **Siguiente** para cambiar a la página **Resumen**.     
24. Revise el resumen y haga clic en **Siguiente** para iniciar el proceso de implementación y ver el **Estado de implementación**.
25. En la página **Estado de implementación**, debería ver el estado del proceso de implementación. Cuando se haya completado la implementación, haga clic en Finalizar. 

Tenga en cuenta lo siguiente:

- Si recibe el error: "**La suscripción no está registrada para usar el espacio de nombres Microsoft.DataFactory**", realice una de las acciones siguientes e intente publicarla de nuevo: 

	- En Azure PowerShell, ejecute el siguiente comando para registrar el proveedor de Data Factory. 
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Puede ejecutar el comando siguiente para confirmar que se ha registrado el proveedor de Data Factory.
	
			Get-AzureRmResourceProvider
	- Inicie sesión mediante la suscripción de Azure en el [Portal de Azure](https://portal.azure.com) y vaya a una hoja de Data Factory (o) cree una factoría de datos en el Portal de Azure. Este procedimiento registra automáticamente el proveedor.
- 	El nombre de la factoría de datos se puede registrar como un nombre DNS en el futuro y, por lo tanto, hacerse públicamente visible.
- 	Para crear instancias de Data Factory, debe ser administrador o colaborador en la suscripción de Azure.

## Resumen
En este tutorial, ha creado una factoría de datos de Azure para copiar datos de un blob de Azure en una base de datos SQL de Azure. Ha usado Visual Studio para crear la factoría de datos, los servicios vinculados, los conjuntos de datos y una canalización. Estos son los pasos de alto nivel que realizó en este tutorial:

1.	Ha creado una **factoría de datos** de Azure.
2.	Ha creado **servicios vinculados**:
	1. Un servicio vinculado **Almacenamiento de Azure** para vincular la cuenta de almacenamiento de Azure que contiene datos de entrada. 	
	2. Un servicio vinculado **SQL Azure** para vincular la base de datos SQL de Azure que contiene los datos de salida. 
3.	Ha creado **conjuntos de datos** que describen los datos de entrada y salida para las canalizaciones.
4.	Ha creado una **canalización** con una **actividad de copia** con un origen **BlobSource** y un receptor **SqlSink**. 


## Uso del Explorador de servidores para ver las entidades de Data Factory

1. En **Visual Studio** haga clic en **Vista** en el menú y haga clic en **Explorador de servidores**.
2. En la ventana Explorador de servidores, expanda **Azure** y **Factoría de datos**. Si aparece **Iniciar sesión en Visual Studio**, escriba la **cuenta** asociada a su suscripción de Azure y haga clic en **Continuar**. Escriba la **contraseña** y haga clic en **Iniciar sesión**. Visual Studio intenta obtener información acerca de todas las factorías de datos de Azure en su suscripción. Verá el estado de esta operación en la ventana **Lista de tareas de Factoría de datos**.  
	![Explorador de servidores](./media/data-factory-get-started-using-vs/server-explorer.png)
3. Puede hacer clic con el botón derecho en una factoría de datos y seleccionar Exportar Factoría de datos al nuevo proyecto para crear un proyecto de Visual Studio basado en una factoría de datos existente.  
	![Exportar factoría de datos a un proyecto de VS](./media/data-factory-get-started-using-vs/export-data-factory-menu.png)  

## Actualización de herramientas de Factoría de datos para Visual Studio
Para actualizar las herramientas de Factoría de datos de Azure para Visual Studio, haga lo siguiente:

1. Haga clic en el menú **Herramientas** y seleccione **Extensiones y actualizaciones**. 
2. Seleccione **Actualizaciones** en el panel izquierdo y, luego, seleccione **Galería de Visual Studio**.
4. Seleccione **Herramientas de Factoría de datos de Azure para Visual Studio** y haga clic en **Actualizar**. Si no ve esta entrada, ya tiene la versión más reciente de las herramientas. 

Consulte [Supervisión y administración de canalizaciones de la Factoría de datos de Azure](data-factory-get-started-using-editor.md#monitor-pipeline) para obtener instrucciones sobre cómo usar el Portal de Azure para supervisar la canalización y los conjuntos de datos creados en este tutorial.

## Otras referencias
| Tema. | Descripción |
| :---- | :---- |
| [Actividades de movimiento de datos](data-factory-data-movement-activities.md) | En este artículo se proporciona información detallada sobre la actividad de copia que se usa en el tutorial. |
| [Programación y ejecución con Data Factory](data-factory-scheduling-and-execution.md) | En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure. |
| [Procesos](data-factory-create-pipelines.md) | Este artículo ayuda a comprender las canalizaciones y actividades en Factoría de datos de Azure y cómo aprovecharlas para construir flujos de trabajo de extremo a extremo controlados por datos para su escenario o empresa. |
| [Conjuntos de datos](data-factory-create-datasets.md) | Este artículo le ayudará a comprender los conjuntos de datos en Factoría de datos de Azure.
| [Supervisión y administración de canalizaciones de Data Factory de Azure mediante la nueva Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) | En este artículo se describe cómo supervisar, administrar y depurar las canalizaciones mediante la aplicación de supervisión y administración. 

<!---HONumber=AcomDC_0615_2016-->