<properties
   pageTitle="Migración del esquema a Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para migrar el esquema a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Migración del esquema a Almacenamiento de datos SQL#

Los resúmenes siguientes le ayudarán a comprender las diferencias entre SQL Server y Almacenamiento de datos SQL para que le resulte más fácil migrar la base de datos.

### Características de tabla
Almacenamiento de datos SQL no usa ni admite estas características:

- Claves principales  
- Claves externas  
- Restricciones CHECK  
- Restricciones UNIQUE  
- Índices únicos  
- Columnas calculadas  
- Columnas dispersas  
- Tipos definidos por el usuario  
- Vistas indexadas  
- Identidades  
- Secuencias  
- Desencadenadores  
- Sinónimos  


### Diferencias de tipo de datos
Almacenamiento de datos SQL admite los tipos de datos empresariales comunes:

- bigint
- binary
- bit
- char
- fecha
- datetime
- datetime2
- datetimeoffset
- decimal
- float
- int
- money
- nchar
- nvarchar
- numeric
- real
- smalldatetime
- smallint
- smallmoney
- sysname
- Twitter en tiempo
- tinyint
- varbinary
- varchar
- uniqueidentifier

Puede usar esta consulta para identificar columnas del almacenamiento de datos que contienen tipos incompatibles:

```sql
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'sql_variant'
                ,   'text'
                ,   'timestamp'
                ,   'xml'
                )
AND  y.[is_user_defined] = 1
;

```

La consulta incluye los tipos de datos definidos por el usuario que tampoco se admiten.

No se preocupe si tiene tipos no admitidos en la base de datos. A continuación, se proponen algunas alternativas que puede usar en su lugar.

En lugar de:

- **geometry**, use un tipo varbinary
- **geography**, use un tipo varbinary
- **hierarchyid**, no se admite este tipo CLR
- **image**, **text**, **ntext**, use varchar/nvarchar (cuanto menor, mejor)
- **sql\_variant**, divida la columna en varias columnas fuertemente tipadas
- **table**, convierta en tablas temporales
- **timestamp**, vuelva a procesar el código para que use datetime2 y la función `CURRENT_TIMESTAMP`. Tenga en cuenta que no puede tener current\_timestamp como restricción DEFAULT y el valor no se actualizará automáticamente. Si tiene que migrar valores rowversion de una columna con tipo timestamp, use binary(8) o varbinary(8) para valores de versión de fila NOT NULL o NULL.
- **tipos definidos por el usuario**, vuelva a convertirlos a sus tipos nativos siempre que sea posible
- **xml**, use varchar(max) o más pequeño para un mejor rendimiento. Dividir en columnas si es necesario

Para mejorar el rendimiento, en lugar de:

- nvarchar(max), use nvarchar(4000) o más pequeño para un mejor rendimiento;
- varchar (max), use varchar(8000) o más pequeño para un mejor rendimiento.

Compatibilidad parcial:

- Las restricciones DEFAULT solo admiten literales y constantes. No se admiten funciones ni expresiones no deterministas, tales como `GETDATE()` o `CURRENT_TIMESTAMP`.

> [AZURE.NOTE] Si usa PolyBase para cargar sus tablas, defínalas para que el tamaño máximo posible de fila, incluida la longitud total de columnas de longitud variable, no supere los 32 767 bytes. Aunque puede definir una fila con datos de longitud variable que superen esta cifra, así como cargar filas con BCP, por ahora no podrá usar PolyBase para cargar estos datos. El soporte técnico de PolyBase para filas anchas se agregará en breve. Además, intente limitar el tamaño de las columnas de longitud variable para un rendimiento incluso mejor para ejecutar consultas.

## Pasos siguientes
Una vez migrado correctamente el esquema de base de datos a SQLDW puede continuar con uno de los siguientes artículos:

- [Migración de los datos][]
- [Migración del código][]

Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[Migración del código]: sql-data-warehouse-migrate-code.md
[Migración de los datos]: sql-data-warehouse-migrate-data.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0518_2016-->