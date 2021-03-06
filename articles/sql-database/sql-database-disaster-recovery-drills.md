<properties 
   pageTitle="Maniobras de recuperación ante desastres de Base de datos SQL | Microsoft Azure" 
   description="Obtenga instrucciones e información sobre prácticas recomendadas acerca del uso de la base de datos SQL de Azure para la realización de tareas de obtención de detalles de la recuperación ante desastres. Dichas tareas le ayudarán a mantener la capacidad de recuperación ante errores y fallos de las aplicaciones de negocio críticas." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="mihaelab"/>

#Obtención de detalles de la recuperación ante desastres

Se recomienda validar periódicamente el flujo de trabajo de preparación de la aplicación para la recuperación. Comprobar el comportamiento de la aplicación y las implicaciones de las pérdidas de datos o de las interrupciones que conlleva la conmutación por error es una buena práctica de ingeniería. También es un requisito de la mayoría de estándares del sector como parte de la certificación de continuidad del negocio.

Obtener los detalles de una recuperación ante desastres implica lo siguiente:

- Simular la interrupción del nivel de datos.
- Realizar la recuperación. 
- Validar la integridad de la aplicación tras la recuperación.

Dependiendo de cómo [diseñó su aplicación para la continuidad del negocio](sql-database-business-continuity.md), el flujo de trabajo para la ejecución del proceso de obtención de detalles puede variar. A continuación se describen prácticas recomendadas de obtención de detalles de la recuperación ante desastres en el contexto de base de datos SQL de Azure.

##Restauración geográfica

Para evitar la posible pérdida de datos durante la obtención de detalles de la recuperación ante desastres, se recomienda obtener los detalles mediante la creación de una copia del entorno de producción y utilizando dicho entorno para comprobar el flujo de trabajo de conmutación por error de la aplicación.
 
####Simulación de interrupción

Puede simular la interrupción mediante la eliminación o el cambio de nombre de la base de datos de origen. Tenga en cuenta que esto puede producir un error de conectividad de la aplicación.

####Recuperación

- Realice la restauración geográfica de la base de datos en un servidor diferente, tal como se describe [aquí](sql-database-disaster-recovery.md). 
- Cambie la configuración de la aplicación para conectarse a las bases de datos recuperadas y siga las directrices de la guía [Configuración de una base de datos recuperada](sql-database-disaster-recovery.md) para completar la recuperación.

####Validación

- Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).

##Replicación geográfica

En una base de datos protegida mediante replicación geográfica, el ejercicio de obtención de detalles incluirá la conmutación por error planeada de la base de datos secundaria. La conmutación por error planeada, garantiza que las bases de datos principal y secundaria permanezcan sincronizadas cuando se cambian los roles. A diferencia de la conmutación por error no planeada, esta operación no provocará la pérdida de datos, por lo que la obtención de detalles se puede realizar en el entorno de producción.

####Simulación de interrupción

Para simular una interrupción puede deshabilitar la aplicación web o la máquina virtual conectada a la base de datos. Esto provocará errores de conectividad de los clientes web.

####Recuperación

- Asegúrese de que la configuración de la aplicación en la región de recuperación ante desastres, está conformada según la base de datos secundaria anterior; recuerde que esta se convertirá en una base de datos principal nueva y totalmente accesible. 
- Realice la [conmutación por error planeada](sql-database-geo-replication-powershell.md#initiate-a-planned-failover) para hacer que la base de datos secundaria se convierta en una primaria
- Siga las instrucciones de la guía [Configurar una base de datos recuperada](sql-database-disaster-recovery.md) para completar la recuperación.

####Validación

- Complete la obtención de detalles mediante la comprobación de la integridad de la aplicación posterior a la recuperación (es decir, las cadenas de conexión, los inicios de sesión, la comprobación de funciones básicas u otras validaciones que formen parte de los procedimientos estándar de validación de aplicaciones).


## Pasos siguientes

- Para obtener información sobre cómo usar y configurar la funcionalidad de replicación geográfica activa para realizar el proceso recuperación ante desastres, consulte [Replicación geográfica activa](sql-database-geo-replication-overview.md).
- Para obtener información sobre cómo utilizar la funcionalidad de replicación geográfica activa para realizar el proceso recuperación ante desastres, consulte [Restauración geográfica](sql-database-geo-restore.md).

## Recursos adicionales

- [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md)
- [Overview: SQL Database Point-in-Time Restore (Información general: Restauración a un momento dado de Base de datos SQL)](sql-database-point-in-time-restore.md)
- [Restauración geográfica](sql-database-geo-restore.md)
- [Replicación geográfica activa](sql-database-geo-replication-overview.md)
- [Diseño de aplicaciones para la recuperación ante desastres en la nube](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Configuración de seguridad para Replicación geográfica activa o estándar](sql-database-geo-replication-security-config.md)
- [P+F de BCDR de Base de datos SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->