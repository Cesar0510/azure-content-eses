<properties
	pageTitle="Generación de perfiles de datos de los orígenes de datos"
	description="Este artículo de procedimientos destaca cómo incluir perfiles de datos de nivel de tabla y de columna al registrar orígenes de datos en el Catálogo de datos de Azure y cómo utilizar perfiles de datos para entender los orígenes de datos."
	services="data-catalog"
	documentationCenter=""
	authors="spelluru"
	manager="NA"
	editor=""
	tags=""/>
<tags
	ms.service="data-catalog"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-catalog"
	ms.date="04/07/2016"
	ms.author="spelluru"/>

# Orígenes de datos de perfiles de datos

## Introducción

El **Catálogo de datos de Microsoft Azure** es un servicio en la nube totalmente administrado que actúa como sistema de registro y de detección de orígenes de datos empresariales. En otras palabras, el **Catálogo de datos de Azure** consiste en ayudar a las personas a detectar, comprender y usar orígenes de datos, y en ayudar a las organizaciones a obtener más valor de sus datos. Cuando un origen de datos se registra en el **Catálogo de datos de Azure**, el servicio copia e indexa sus metadatos, pero eso no es todo.

El **Catálogo de datos de Azure** examina los datos de orígenes de datos admitidos en el catálogo y recopila estadísticas e información sobre esos datos. Esto se denomina **Generación de perfiles de datos**. Es fácil incluir un perfil de sus recursos de datos. Al registrar un recurso de datos, elija **Incluir perfil de datos** en la herramienta de registro de orígenes de datos.

## ¿Qué es la generación de perfiles de datos?

La generación de perfiles de datos examina los datos del origen de datos que se registra y recopila estadísticas e información sobre esos datos. Durante la detección del origen de datos, estas estadísticas pueden ayudar a los usuarios a determinar la idoneidad de los datos para resolver sus problemas empresariales.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Los siguientes orígenes de datos admiten la generación de perfiles de datos:

- Vistas y tablas de SQL Server (incluidos Almacenamiento de datos SQL y Base de datos SQL de Azure)
- Vistas y tablas de Oracle
- Vistas y tablas de Teradata
- Tablas de Hive

Incluir los perfiles de datos al registrar lo recursos de datos ayuda a los usuarios a responder preguntas acerca de los orígenes de datos, incluidas:

-	¿Puede utilizarse para solucionar mi problema empresarial?
-	¿Los datos se ajustan a estándares o patrones específicos?
-	¿Cuáles son algunas de las anomalías del origen de datos?
-	¿Cuáles son los posibles retos de integración de estos datos en mi aplicación?

> [AZURE.NOTE] También puede agregar documentación a un recurso para describir cómo se pueden integrar los datos en una aplicación. Consulte [Documentación de los orígenes de datos](data-catalog-how-to-documentation.md).


<a name="howto"/>
## Cómo incluir un perfil de datos al registrar un origen de datos

Es fácil incluir un perfil de sus origen de datos. Al registrar un origen de datos, en el panel **Objetos que se registrarán** de la herramienta de registro de orígenes de datos, elija **Incluir perfil de datos**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Para aprender más acerca de cómo registrar orígenes de datos, consulte [Registro de orígenes de datos](data-catalog-how-to-register.md) e [Introducción al Catálogo de datos de Azure](data-catalog-get-started.md).


## Filtrado de recursos de datos que incluyen perfiles de datos
Para detectar los recursos de datos que incluyen un perfil de datos, puede incluir **has:tableDataProfiles** o **has:columnsDataProfiles** como uno de los términos de búsqueda.

> [AZURE.NOTE] Al seleccionar **Incluir perfil de datos** en la herramienta de registro de orígenes de datos, se incluirá información del perfil de nivel de tabla y columna, pero la API de Catálogo de datos permite registrar los recursos de datos solo con un conjunto de información del perfil incluido.

## Visualización de la información del perfil de datos

Una vez que encuentre un origen de datos adecuado con un perfil, puede ver los detalles del perfil de datos. Para ver el perfil de datos, seleccione un recurso de datos y elija **Perfil de datos** en la ventana del portal de Catálogo de datos.

![](media\data-catalog-data-profile\data-catalog-view.png)

Un perfil de datos del **Catálogo de datos de Azure** muestra la información del perfil de tabla y columna, incluido lo siguiente:

### Perfil de datos de objeto

-	Número de filas
-	Tamaño de la tabla
-	Cuándo se actualizó por última vez el objeto

### Perfil de datos de columna

- Tipo de datos de columna
- Número de valores distintivos
- Número de filas con valores NULL
- Mínimo, máximo, promedio y desviación estándar para los valores de las columnas

## Resumen
La generación de perfiles de datos proporciona estadísticas e información sobre los recursos de datos registrados para ayudar a los usuarios a determinar la idoneidad de los datos para resolver problemas empresariales. Junto con la anotación y documentación de los orígenes de datos, los perfiles de datos pueden dar a los usuarios una comprensión más profunda de los datos.


## Otras referencias
-	[Registro de orígenes de datos](data-catalog-how-to-register.md)
-	[Introducción al Catálogo de datos de Azure](data-catalog-get-started.md)

<!---HONumber=AcomDC_0511_2016-->