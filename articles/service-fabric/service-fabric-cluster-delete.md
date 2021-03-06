<properties
   pageTitle="Eliminación de un clúster y los recursos que contiene | Microsoft Azure"
   description="Aprenda a eliminar completamente un clúster de Service Fabric, bien mediante la eliminación del grupo de recursos que contiene el clúster o por medio de la eliminación selectiva de recursos."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/04/2016"
   ms.author="chackdan"/>

# Eliminación de un clúster de Service Fabric y de los recursos que utiliza

Un clúster de Service Fabric está formado por muchos otros recursos de Azure, además del recurso del clúster propiamente dicho. De modo que, para eliminar completamente un clúster de Service Fabric, también debe eliminar todos los recursos que lo componen. Para ello, tiene dos opciones: eliminar el grupo de recursos en el que se encuentra el clúster (lo que elimina el recurso del clúster y todos los demás recursos del grupo) o eliminar específicamente el recursos del clúster y sus recursos asociados (pero ningún otro recurso del grupo).

>[AZURE.NOTE] Con la eliminación del recurso del clúster **no** se eliminan todos los demás recursos de los que consta el clúster de Service Fabric.

## Eliminación del grupo de recursos completo (RG) en el que se encuentra el clúster de Service Fabric

Es la forma más sencilla de asegurarse de que elimina todos los recursos asociados al clúster, incluido el grupo de recursos. Puede eliminar el grupo de recursos con PowerShell o mediante el Portal de Azure. Si el grupo de recursos tiene recursos que no están relacionados con el clúster de Service fabric, puede eliminar recursos específicos.

### Eliminación del grupo de recursos mediante Azure PowerShell

Otra forma de eliminar el grupo de recursos es ejecutar los siguientes cmdlets de Azure PowerShell. Asegúrese de que tiene instalado en su equipo Azure PowerShell 1.0 o una versión superior. Si no lo ha hecho antes, siga los pasos que se describen en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Abra una ventana de PowerShell y ejecute los siguientes cmdlets de PS:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Si no ha utilizado la opción *-Force*, recibirá un mensaje para confirmar la eliminación. Tras la confirmación, el grupo de recursos y todos los recursos que contienen se eliminarán.

### Eliminación de un grupo de recursos en el Portal de Azure  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Desplácese hasta el clúster de Service Fabric que quiere eliminar.
3. Haga clic en el nombre del grupo de recursos en la página de información básica del clúster.
4. Se abrirá la página **Resource Group Essentials** (Información básica del grupo de recursos).
5. Hacer clic en **Eliminar**.
6. Siga las instrucciones que se indican en esa página para completar la eliminación del grupo de recursos.

![Eliminación de un grupo de recursos][ResourceGroupDelete]


## Eliminación del recurso de clúster y de los recursos que utiliza, pero no de otros recursos del grupo de recursos

Si el grupo de recursos tiene únicamente recursos que están relacionados con el clúster de Service Fabric que quiere eliminar, lo más sencillo es eliminar el grupo de recursos completo. Si quiere eliminar de forma selectiva los recursos del grupo uno a uno, siga los pasos que se muestra a continuación.

Si ha implementado el clúster mediante el portal o por medio de una de las plantillas de ARM de Service Fabric de la galería de plantillas, entonces todos los recursos del clúster están etiquetados con las dos etiquetas siguientes. Puede utilizarlas para decidir qué recursos quiere eliminar.

***Etiqueta 1 :*** Clave = clusterName, valor = 'nombre del clúster'

***Etiqueta 2:*** Clave = resourceName, valor = ServiceFabric

### Eliminación de recursos específicos en el Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Desplácese hasta el clúster de Service Fabric que quiere eliminar.
3. Vaya a **All settings** (Toda la configuración) en la hoja de información básica.
4. Haga clic en **Etiquetas** en **Administración de recursos** en la hoja de configuración.
5. Haga clic en una de las **etiquetas** en la hoja de etiquetas para obtener una lista de todos los recursos con esa etiqueta.

    ![Etiquetas del recurso][ResourceTags]

6. Cuando tenga la lista de recursos etiquetados, haga clic en cada uno de los recursos y elimínelos.

    ![Recursos etiquetados][TaggedResources]

### Eliminación de los recursos con Azure PowerShell

Puede eliminar los recursos uno a uno mediante la ejecución de los siguientes cmdlets de Azure PowerShell. Asegúrese de que tiene instalado en su equipo Azure PowerShell 1.0 o una versión superior. Si no lo ha hecho antes, siga los pasos que se describen en [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Abra una ventana de PowerShell y ejecute los siguientes cmdlets de PS:

```powershell
Login-AzureRmAccount
```
Para cada uno de los recursos que quiere eliminar, ejecute lo siguiente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Para eliminar el recurso de clúster, ejecute lo siguiente:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## Pasos siguientes
Lea la siguiente información para saber también sobre la actualización de un clúster y los servicios de creación de particiones:

- [Obtener información sobre actualizaciones de clúster](service-fabric-cluster-upgrade.md)
- [Obtener información sobre los servicios con estado de creación de particiones para una escala máxima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG

<!---HONumber=AcomDC_0518_2016-->