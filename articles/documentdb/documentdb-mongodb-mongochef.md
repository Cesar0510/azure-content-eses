<properties 
	pageTitle="Uso de MongoChef con una cuenta de DocumentDB con soporte de protocolo para MongoDB | Microsoft Azure" 
	description="Obtenga información acerca de cómo usar MongoChef con una cuenta de DocumentDB con soporte de protocolo para MongoDB, ahora disponible para versión preliminar." 
	keywords="mongochef"
	services="documentdb" 
	authors="stephbaron" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/31/2016" 
	ms.author="stbaro"/>

# Uso de MongoChef con una cuenta de DocumentDB con soporte de protocolo para MongoDB

Para conectarse a una cuenta de DocumentDB con soporte de protocolo para MongoDB mediante MongoChef, debe hacer lo siguiente:

- Descargar e instalar [MongoChef](http://3t.io/mongochef)
- Disponer de la cuenta de DocumentDB con soporte de protocolo para la información de la [cadena de conexión](documentdb-connect-mongodb-account.md) de MongoDB

## Crear la conexión en MongoChef  

Para agregar su cuenta de DocumentDB con soporte de protocolo para MongoDB en el administrador de conexiones de MongoChef, realice los pasos siguientes.

1. Haga clic en **Connect** (Conectar) para abrir el Connection Manager (Administrador de conexiones) y, después, haga clic en **New Connection** (Nueva conexión).

	![Captura de pantalla del administrador de conexiones de MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManager.png)
	
2. En la ventana **New Connection** (Nueva conexión), en la pestaña **Server** (Servidor), escriba el puerto y el FQDN de la cuenta de DocumentDB con soporte de protocolo para MongoDB.
	
	![Captura de pantalla de la pestaña Server (Servidor) del administrador de conexiones de MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerServerTab.png)

3. En la ventana **New Connection** (Nueva conexión), en la pestaña **Authentication** (Autenticación), elija el modo de autenticación **Standard (MONGODB-CR or SCARM-SHA-1)** (Estándar (MONGODB-CR o SCARM-SHA-1) y escriba la información de nombre de usuario y contraseña. Acepte la base de datos de autenticación predeterminada (admin) o proporcione su propio valor.

	![Captura de pantalla de la pestaña Authentication (Autenticación) del administrador de conexiones de MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerAuthenticationTab.png)

4. En la ventana **New Connection** (Nueva conexión), en la pestaña **SSL**, active la casilla **Use SSL protocol to connect** (Usar protocolo SSL para conectar) y el botón de radio **Accept self-signed SSL certificates** (Aceptar certificados SSL autofirmados).

	![Captura de pantalla de la pestaña SSL del administrador de conexiones de MongoChef](./media/documentdb-mongodb-mongochef/ConnectionManagerSSLTab.png)

5. Haga clic en el botón **Test Connection** (Probar conexión) para validar la información de conexión, haga clic en **OK** (Aceptar) para volver a la ventana de la nueva conexión y, finalmente, haga clic en **Save** (Guardar).

	![Captura de pantalla de la ventana de conexión de prueba de MongoChef](./media/documentdb-mongodb-mongochef/TestConnectionResults.png)

## Uso de MongoChef para crear una base de datos, una colección y unos documentos  

Para crear una base de datos, una colección y unos documentos mediante MongoChef, realice los pasos siguientes.

1. En **Connection Manager** (Administrador de conexiones), resalte la conexión y haga clic en **Connect** (Conectar).

	![Captura de pantalla del administrador de conexiones de MongoChef](./media/documentdb-mongodb-mongochef/ConnectToAccount.png)

2. Haga clic con el botón derecho en el host y elija **Add Database** (Agregar base de datos). Especifique un nombre de base de datos y haga clic en **OK** (Aceptar).
	
	![Captura de pantalla de la opción Add Database (Agregar base de datos) de MongoChef](./media/documentdb-mongodb-mongochef/AddDatabase1.png)

3. Haga clic con el botón derecho en la base de datos y elija **Add Collection** (Agregar colección). Especifique un nombre para la colección y haga clic en **Create** (Create).

	![Captura de pantalla de la opción Add Collection (Agregar colección) de MongoChef](./media/documentdb-mongodb-mongochef/AddCollection.png)

4. Haga clic en el elemento de menú **Collection** (Colección) y en **Add Document** (Agregar documento).

	![Captura de pantalla del elemento de menú Add Document (Agregar documento) de MongoChef](./media/documentdb-mongodb-mongochef/AddDocument1.png)

5. En el cuadro de diálogo Add Document (Agregar documento), pegue lo siguiente y haga clic en **Add Document** (Agregar documento).

		{
    	"_id": "AndersenFamily",
    	"lastName": "Andersen",
    	"parents": [
       		{ "firstName": "Thomas" },
       		{ "firstName": "Mary Kay"}
    	],
    	"children": [
       	{
           	"firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
           	"pets": [{ "givenName": "Fluffy" }]
       	}
    	],
    	"address": { "state": "WA", "county": "King", "city": "seattle" },
    	"isRegistered": true
		}

	
6. Agregue otro documento, esta vez con el contenido siguiente.

		{
	    "_id": "WakefieldFamily",
	    "parents": [
    	    { "familyName": "Wakefield", "givenName": "Robin" },
        	{ "familyName": "Miller", "givenName": "Ben" }
    	],
    	"children": [
	        {
            	"familyName": "Merriam", 
             	"givenName": "Jesse", 
            	"gender": "female", "grade": 1,
            	"pets": [
	                { "givenName": "Goofy" },
                	{ "givenName": "Shadow" }
            	]
        	},
        	{ 
	            "familyName": "Miller", 
             	"givenName": "Lisa", 
             	"gender": "female", 
             	"grade": 8 }
    	],
    	"address": { "state": "NY", "county": "Manhattan", "city": "NY" },
    	"isRegistered": false
		}

7. Ejecute una consulta de ejemplo. Por ejemplo, busque familias con el apellido 'Andersen' y devuelva los campos parents (padres) y state (estado).

	![Captura de pantalla de resultados de la consulta de MongoChef](./media/documentdb-mongodb-mongochef/QueryDocument1.png)
	

## Pasos siguientes

- Explore DocumentDB con soporte de protocolo para buscar [ejemplos](documentdb-mongodb-samples.md) de MongoDB.
- Obtenga información sobre la [versión preliminar de las instrucciones de desarrollo](documentdb-mongodb-guidelines.md) para DocumentDB con soporte de protocolo para MongoDB.

 

<!---HONumber=AcomDC_0601_2016-->