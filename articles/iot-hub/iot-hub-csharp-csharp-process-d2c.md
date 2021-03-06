<properties
	pageTitle="Procesamiento de mensajes de dispositivo a la nube del centro de IoT | Microsoft Azure"
	description="Siga este tutorial para aprender sobre patrones útiles para procesar mensajes de dispositivo a nube del centro de IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# Tutorial: procesamiento de mensajes de dispositivo a la nube del Centro de IoT

## Introducción

El centro de IoT de Azure es un servicio totalmente administrado que permite la comunicación bidireccional fiable y segura entre millones de dispositivos IoT y una aplicación back-end. Otros tutoriales ([Introducción al Centro de IoT de Azure para .NET] y [Envío de mensajes de nube a dispositivo con el Centro de IoT]) muestran cómo usar la funcionalidad básica de mensajería de dispositivo a nube y de nube a dispositivo del Centro de IoT.

Este tutorial se basa en el código que se muestra en el tutorial [Introducción al Centro de IoT de Azure para .NET] y muestra dos patrones escalables que se pueden usar para procesar mensajes de dispositivo a nube:

- Almacenamiento confiable de mensajes de dispositivo a nube de [Almacenamiento de blobs de Azure]. Un escenario muy común es el *análisis en frío*, en el que se almacenan datos de telemetría en blobs para usarlos como entrada en los procesos de análisis. Estos procesos pueden estar controlados por herramientas como [Data Factory de Azure] o la pila [HDInsight (Hadoop)].

- Procesamiento confiable de mensajes de dispositivo a nube *interactivos*. Los mensajes de dispositivo a nube son interactivos cuando son desencadenantes inmediatos de un conjunto de acciones en el back-end de la aplicación. Por ejemplo, un dispositivo puede enviar un mensaje de alarma que desencadena la inserción de una incidencia en un sistema CRM. Por el contrario, los mensajes de *punto de datos* simplemente se envían a un motor de análisis. Por ejemplo, la telemetría de temperatura de un dispositivo que se almacena para su posterior análisis es un mensaje de punto de datos.

Puesto que Centro de IoT expone un punto de conexión compatible con los [Centros de eventos] para recibir mensajes de dispositivo a nube, este tutorial usa una instancia de [EventProcessorHost][lnk-event-hubs]. Esta instancia:

* Almacena de manera confiable mensajes de *punto de datos* en Almacenamiento de blobs de Azure.
* Reenvía mensajes de dispositivo a nube *interactivos* a una [cola del Bus de servicio] para su procesamiento inmediato.

El Bus de servicio ayuda a asegurar un procesamiento confiable de mensajes interactivos, ya que ofrece puntos de comprobación de cada mensaje y desduplicación basada en periodos de tiempo.

> [AZURE.NOTE] Una instancia de **EventProcessorHost** es solamente una de las formas de procesar los mensajes interactivos. Otras opciones incluyen [Azure Service Fabric][lnk-service-fabric] y [Análisis de transmisiones de Azure][lnk-stream-analytics].

Al final de este tutorial, ejecutará tres aplicaciones de consola de Windows:

* **SimulatedDevice**, una versión modificada de la aplicación creada en el tutorial [Introducción al Centro de IoT de Azure para .NET], que envía mensajes de dispositivo a nube de punto de datos cada segundo y mensajes de dispositivo a nube interactivos cada 10 segundos. Esta aplicación usa el protocolo AMQPS para comunicarse con el Centro de IoT.
* **ProcessDeviceToCloudMessages** utiliza la clase [EventProcessorHost] para recuperar mensajes desde el punto de conexión compatible con los Centros de eventos. A continuación, almacena los mensajes de punto de datos de forma confiable en Almacenamiento de blobs de Azure y envía mensajes interactivos a una cola de Bus de servicio.
* **ProcessD2CInteractiveMessages** quita los mensajes interactivos de la cola del Bus de servicio.

> [AZURE.NOTE] El Centro de IoT ofrece compatibilidad con el SDK para muchas plataformas de dispositivos y lenguajes, entre los que se incluyen C, Java y JavaScript. Para obtener instrucciones paso a paso sobre cómo reemplazar el dispositivo simulado de este tutorial con un dispositivo físico y, en general, sobre cómo conectar dispositivos al Centro de IoT de Azure, consulte el [Centro para desarrolladores de IoT de Azure].

Este tutorial se puede aplicar directamente a otras formas de consumir mensajes compatibles con Centros de eventos como, por ejemplo, proyectos de [HDInsight (Hadoop)]. Consulte la [Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube] para más información.

Para completar este tutorial, necesitará lo siguiente:

+ Microsoft Visual Studio 2015.

+ Una cuenta de Azure activa. <br/>Si carece de una suscripción de Azure, puede crear una [cuenta gratis](https://azure.microsoft.com/free/) en tan solo unos minutos.

También se dan por sentados ciertos conocimientos sobre [Almacenamiento de Azure] y [Bus de servicio de Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Ejecución de las aplicaciones

Ya está preparado para ejecutar las aplicaciones.

1.	En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y luego **Iniciar** como la acción para los proyectos **ProcessDeviceToCloudMessages**, **SimulatedDevice** y **ProcessD2CInteractiveMessages**.

2.	Presione **F5** para iniciar las tres aplicaciones de consola. La aplicación **ProcessD2CInteractiveMessages** debe procesar cada mensaje interactivo enviado desde la aplicación **SimulatedDevice**.

  ![Tres aplicaciones de consola][50]

> [AZURE.NOTE] Para ver las actualizaciones en el archivo de blob, debe reducir la constante **MAX\_BLOCK\_SIZE** de la clase **StoreEventProcessor** a un valor inferior, como **1024**. Esto se debe a que se tarda algún tiempo en alcanzar el límite de tamaño de bloque con los datos enviados por el dispositivo simulado. Con un tamaño de bloque menor, no tendrá que esperar tanto tiempo para ver el blob que se crea y se actualiza. Aunque un tamaño de bloque mayor hace que la aplicación sea más escalable.

## Pasos siguientes

En este tutorial, ha aprendido a procesar de manera confiable mensajes de dispositivo a nube interactivos y de punto de datos mediante la clase [EventProcessorHost].

El tutorial [Cómo cargar archivos desde dispositivos a la nube con un Centro de IoT] se basa en este tutorial utilizando una lógica de procesamiento de mensaje análoga. También describe un patrón que usa mensajes de nube a dispositivo para facilitar la carga de archivos desde los dispositivos.

Información adicional sobre el centro de IoT:

* [Información general sobre el centro de IoT]
* [Guía del desarrollador del centro de IoT]
* [Directrices sobre el centro de IoT]
* [Lenguajes y plataformas de dispositivos compatibles][Supported devices]
* [Centro para desarrolladores de Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Almacenamiento de blobs de Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Data Factory de Azure]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Centros de eventos]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guía del desarrollador del Centro de IoT de Azure - Dispositivo a nube]: iot-hub-devguide.md#d2c

[Almacenamiento de Azure]: https://azure.microsoft.com/documentation/services/storage/
[Bus de servicio de Azure]: https://azure.microsoft.com/documentation/services/service-bus/



[Envío de mensajes de nube a dispositivo con el Centro de IoT]: iot-hub-csharp-csharp-c2d.md
[Cómo cargar archivos desde dispositivos a la nube con un Centro de IoT]: iot-hub-csharp-csharp-file-upload.md

[Información general sobre el centro de IoT]: iot-hub-what-is-iot-hub.md
[Directrices sobre el centro de IoT]: iot-hub-guidance.md
[Guía del desarrollador del centro de IoT]: iot-hub-devguide.md
[Introducción al Centro de IoT de Azure para .NET]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centro para desarrolladores de Azure]: https://azure.microsoft.com/develop/iot
[Centro para desarrolladores de IoT de Azure]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0608_2016-->