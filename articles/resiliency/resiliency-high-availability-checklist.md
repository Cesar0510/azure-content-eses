<properties
   pageTitle="Lista de comprobación de alta disponibilidad | Microsoft Azure"
   description="Lista de comprobación rápida de la configuración y las acciones que puede realizar para asegurarse de que mejora la disponibilidad de aplicaciones con Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/09/2016"
   ms.author="aglick"/>

#Lista de comprobación de alta disponibilidad
Una de las grandes ventajas de usar Azure es la capacidad de aumentar la disponibilidad (y escalabilidad) de las aplicaciones con la ayuda de la nube. Para asegurarse de que saca el máximo partido de dichas opciones, la siguiente lista de comprobación sirve para ayudarle con algunos de los principales aspectos básicos de la infraestructura, con el fin de garantizar que sus aplicaciones son resistentes.

>[AZURE.NOTE] La mayoría de las sugerencias siguientes se pueden implementar en cualquier momento en la aplicación y, por consiguiente, son ideales para "revisiones rápidas". La mejor solución a largo plazo a menudo implica un diseño de aplicación que se ha generado para la nube. Para ver una lista de comprobación de estas (áreas más orientadas al diseño), consulte [Lista de comprobación de disponibilidad](../best-practices-availability-checklist.md).

##¿Usa Administrador de tráfico delante de los recursos?
Administrador de tráfico le ayuda a enrutar el tráfico de Internet a través de las regiones de Azure, o de las ubicaciones locales y de Azure. Puede hacerlo por varias razones, entre las que se incluyen la latencia y la disponibilidad. Si desea más información sobre cómo utilizar el Administrador de tráfico para aumentar la resistencia y propagar el tráfico por varias regiones, leer [¿Qué es el Administrador de tráfico?](../traffic-manager/traffic-manager-overview.md)

__¿Qué sucede si no se utiliza el Administrador de tráfico?__ Si no utiliza el Administrador de tráfico delante de la aplicación, estará limitado a una sola región para los recursos. Así se limita la escala, se aumenta la latencia de los usuarios que no están cerca de la región elegida y se reduce la protección en caso de que se produzca una interrupción del servicio en toda la región.

##¿Ha evitado utilizando usar sola máquina virtual para todos los roles?
Un buen diseño evita todos los puntos de error. Esto es importante en el diseño de todos los servicios (tanto local como en la nube), pero es especialmente útil en la nube, ya que la escalabilidad y resistencia pueden aumentarse a través del escalado horizontal (agregar máquinas virtuales), en lugar del escalado vertical (utilizar una máquina virtual más potente). Si desea más información sobre el diseño de aplicaciones escalables, consulte [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-high-availability-azure-applications.md).

__¿Qué ocurre si tiene una sola máquina virtual para un rol?__ Una máquina individual es un punto único de error. En el mejor de los casos, la aplicación se ejecutará correctamente, pero este no es un diseño resistente y cualquier punto único de error aumenta la posibilidad de tiempo de inactividad si se produce algún error.

##¿Utiliza un equilibrador de carga delante de las máquinas virtuales con conexión a Internet de la aplicación?
Los equilibradores de carga permiten expandir el tráfico entrante a la aplicación en un número arbitrario de máquinas. Puede agregar o eliminar máquinas del equilibrador de carga en cualquier momento, algo que funciona bien con las máquinas virtuales (y también con el ajuste de escala automático con conjunto de escalas de máquina virtual), ya que le permite controlar fácilmente el aumento de tráfico o los errores de las máquina virtuales. Si desea más información sobre los equilibradores de carga, consulte [Información general sobre Azure Load Balancer](../load-balancer/load-balancer-overview.md).

__¿Qué sucede si no utiliza un equilibrador de carga delante de las máquinas virtuales con conexión a Internet?__ Sin un equilibrador de carga no podrá realizar el escalado horizontal (agregar más máquinas), por lo que la única opción de la que dispondrá será realizar un escalado vertical (aumentar el tamaño de la máquina con conexión al Web). También se enfrentará a un punto único de error con esa máquina. Además, será preciso que escriba código DNS para saber si ha perdido una máquina con conexión a Internet y que vuelva a asignar la entrada DNS al nuevo equipo que inicie para que ocupe su lugar.

##¿Usa los conjuntos de disponibilidad para los servidores web y la aplicación sin estado?
Si coloca las máquinas en un conjunto de disponibilidad, las máquinas virtuales serán válidas para el Acuerdo de Nivel de Servicio de la máquina virtual de Azure. El mero hecho de formar parte de un conjunto de disponibilidad también garantiza que las máquinas se colocan en diferentes dominios de actualización (es decir, en diferentes máquinas a las que se aplican parches en distintos momentos) y dominios de error (es decir, máquinas host que comparten una fuente de alimentación y un conmutador de red comunes). Sin estar en un conjunto de disponibilidad, las máquinas virtuales pueden encontrarse en la misma máquina host y, por consiguiente, puede haber un único punto de error que no el usuario no pueda ver. Si desea más información acerca de cómo aumentar la disponibilidad de las máquinas virtuales con conjuntos de disponibilidad, consulte [Administración de la disponibilidad de las máquinas virtuales](../virtual-machines/virtual-machines-windows-manage-availability.md).

__¿Qué sucede si no utiliza un conjunto de disponibilidad con las aplicaciones sin estado y los servidores web?__ Si no utiliza un conjunto de disponibilidad, no podrá sacar provecho del Acuerdo de Nivel de Servicio de la máquina virtual de Azure. También significa que todas las máquinas de esa capa de la aplicación pueden quedarse sin conexión si hay una actualización en la máquina host (la máquina que hospeda las máquinas virtuales que usa) o un error de hardware común.

##¿Utiliza conjuntos de escalado de máquina virtual (VMSS) para los servidores web o la aplicación sin estado?
Un buen diseño resistente y escalable utiliza VMSS para asegurarse de que se puede aumentar o reducir el número de máquinas en un nivel de la aplicación (como su capa web). VMSS permite definir cómo se ajusta la capa de aplicación (mediante la incorporación o eliminación de servidores en función de los criterios que elija). Si desea más información acerca de cómo utilizar conjuntos de escalado de máquina virtual de Azure para aumentar la resistencia a los picos de tráfico, consulte [Información general de conjuntos de escala de máquinas virtuales](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

__¿Qué ocurre si no usa un conjunto de escalado de máquina virtual con mi aplicación sin estado del servidor web?__ Sin un VMSS está limitada su capacidad para escalar sin límites y para optimizar el uso de los recursos. Los diseños que carecen de VMSS tienen un límite de escalado que tiene que controlarse con código adicional (o manualmente). Esta falta de un VMSS también significa que la aplicación no puede agregar y quitar fácilmente máquinas (independientemente de la escala) que le ayuden a controlar grandes picos de tráfico (como por ejemplo si se produce una promoción o si su sitio, aplicación o producto se hace viral).

##¿Usa el almacenamiento premium y cuentas de almacenamiento independientes para cada una de las máquinas virtuales?
Se recomienda usar el almacenamiento premium para las máquinas virtuales de producción. Además, debe asegurarse de que utiliza una cuenta de almacenamiento independiente para cada máquina virtual (esto sucede en las implementaciones a pequeña escala. En las implementaciones mayores, puede reutilizar cuentas de almacenamiento en varias máquinas, pero es preciso que exista un equilibrio entre los dominios de actualización y entre los niveles de la aplicación). Si desea más información acerca del rendimiento y la escalabilidad de Almacenamiento de Azure, consulte [Lista de comprobación de rendimiento y escalabilidad de Almacenamiento de Microsoft Azure](../storage/storage-performance-checklist.md).

__¿Qué sucede si no utiliza cuentas de almacenamiento independientes para cada máquina virtual?__ Una cuenta de almacenamiento, al igual que muchos otros recursos, es un punto único de error. Aunque hay muchas protecciones y características de resistencia de Almacenamiento de Azure, un único punto de error nunca es un buen diseño. Por ejemplo, si los derechos de acceso de una cuenta resultan dañados o se alcanzan un límite de almacenamiento o un límite de IOPS, todas las máquinas virtuales que usen dicha cuenta de almacenamiento se verán afectadas. Además, si se produce una interrupción del servicio que afecte a una marca de almacenamiento que incluya dicha cuenta de almacenamiento concreta, varias máquinas virtuales podrían verse afectadas.

##¿Utiliza un equilibrador de carga o una cola entre cada una de las capas de la aplicación?
El uso de equilibradores de carga o colas entre cada nivel de la aplicación le permite escalar fácilmente cada capa de la aplicación de forma fácil e independiente. Debe elegir entre estas tecnologías basándose en su latencia, complejidad, y necesidades de distribución (es decir, el alcance de la distribución de la aplicación). En general, las colas tienden a tener una mayor latencia y agregan complejidad, pero a cambio son más resistentes y permiten distribuir la aplicación en áreas mayores (como por ejemplo, de una región a otra). Si desea más información sobre cómo utilizar equilibradores de carga internos o colas, lea [Información general sobre el equilibrador de carga interno](../load-balancer/load-balancer-internal-overview.md) y [Colas de Service Bus y colas de Azure: comparación y diferencias](../service-bus/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

__¿Qué pasa si no utiliza un equilibrador de carga o una cola entre cada una de las capas de la aplicación?__ Sin un equilibrador de carga, o una cola, entre cada una de las capas de la aplicación es difícil escalar la aplicación horizontal o verticalmente, y distribuir su carga entre varias máquinas. No hacerlo puede provocar el exceso, o la falta, de aprovisionamiento de los recursos y un riesgo de tiempo de inactividad, o una mala experiencia del usuario, si se producen cambios inesperados en el tráfico o errores en el sistema.
 
##¿Usan sus Bases de datos SQL replicación geográfica activa? 
La replicación geográfica activa permite configurar hasta cuatro bases de datos secundarias legibles en las mismas regiones, o en otras. Las bases de datos secundarias están disponibles en caso de interrupción del servicio o de imposibilidad de conectarse a la base de datos principal. Si desea más información acerca de la replicación geográfica activa de Bases de datos SQL, consulte [Información general: Replicación geográfica activa para Base de datos SQL de Azure](../sql-database/sql-database-geo-replication-overview.md).
 
 __¿Qué sucede si no utiliza la replicación geográfica activa con sus Bases de datos SQL?__ Sin la replicación geográfica activa, si alguna vez se desconecta la base de datos principal (debido a un mantenimiento planeado, una interrupción del servicio, un error de hardware, etc.), la base de datos de la aplicación estará sin conexión hasta que la base de datos principal vuelva a estar en línea en un estado correcto.
 
##¿Usa una memoria caché (Caché en Redis de Azure) delante de las bases de datos?
Si la aplicación tiene una carga alta de la base de datos, donde la mayoría de las llamadas de base de datos son lecturas, puede aumentar la velocidad de la aplicación y reducir la carga de la base de datos mediante la implementación de una capa de almacenamiento en caché delante de la base de datos para descargar estas operaciones de lectura. Para aumentar la velocidad de la aplicación y reducir la carga de la base de datos (lo que aumenta la escala que puede controlar), coloque una capa de almacenamiento en caché delante de la base de datos. Si desea más información acerca de Caché en Redis de Azure, consulte [Guía sobre el almacenamiento en caché](../best-practices-caching.md).
 
 __¿Qué ocurre si no usa una memoria caché delante de la base de datos?__ Si la máquina con la base de datos tiene la potencia suficiente para controlar la carga de tráfico que se le asigna, la aplicación responderá con normalidad, aunque esto pueda significar que si la carga es inferior, estará pagando por una máquina más cara de lo necesario. Si la máquina con la base de datos no tiene la potencia suficiente para controlar la carga, empezará a sentir que no es agradable trabajar con la aplicación (latencia, tiempos de espera Y, posiblemente, tiempo de inactividad del servicio).
 
##¿Se ha puesto en contacto con el soporte técnico de Microsoft Azure si espera un evento a gran escala?
El soporte técnico de Azure puede ayudarle a aumentar los límites de su servicio para tratar eventos planeados que se espera que generen mucho tráfico (como el lanzamiento de nuevos productos o festividades especiales). El soporte técnico de Azure también puede ayudarle a conectar con expertos, quienes pueden ayudarle no solo a revisar su diseño con el equipo de su cuenta sino también a encontrar la solución que mejor cubra sus necesidades en los eventos a gran escala. Si desea más información sobre cómo ponerse en contacto con el soporte técnico de Azure, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

__¿Qué ocurre si no se pone en contacto con el soporte técnico de Azure para un evento a gran escala?__ Si no comunica, ni planea, un evento que prevea que va a conllevar mucho tráfico, se arriesga a alcanzar ciertos [límites de los servicios de Azure](../azure-subscription-service-limits.md) y, por consiguiente, a crear una mala experiencia del usuario (o peor aún, tiempo de inactividad) durante el evento. Las revisiones arquitectónicas y la comunicación anticipada de los picos pueden ayudar a mitigar estos riesgos.

##¿Utiliza una red de entrega de contenido (CDN de Azure) delante de los blobs de almacenamiento con conexión al Web y los recursos estáticos?
El uso de una red CDN le ayudará a descargar los servidores mediante el almacenamiento en caché del contenido en las ubicaciones POP o del borde de la red CDN que se encuentran en todo el mundo. Puede hacerlo para reducir la latencia, aumentar la escalabilidad, reducir la carga del servidor y como parte de una estrategia de protección contra los ataques de denegación de servicio (DOS). Si desea más información sobre cómo usar CDN de Azure para aumentar la resistencia y reducir la latencia del cliente, consulte [Información general de la red de entrega de contenido (CDN) de Azure](../cdn/cdn-overview.md).

__¿Qué sucede si no utiliza una red CDN?__ Si no usa una red CDN, todo el tráfico de cliente llega directamente a los recursos, lo que significa que verá mayores cargas en los servidores, lo que reduce su escalabilidad. Además, los clientes pueden experimentar mayores latencias, ya que las redes CDN ofrecen ubicaciones en todo el mundo que probablemente están más próximas a los clientes.

##Pasos siguientes:
Si desea más información acerca de cómo diseñar las aplicaciones para que tengan alta disponibilidad, consulte [Alta disponibilidad para aplicaciones creadas en Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0608_2016-->