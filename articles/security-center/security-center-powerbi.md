<properties
   pageTitle="Obtención de información mediante los datos de Azure Security Center con Power BI| Microsoft Azure"
   description="El paquete de contenido de Power BI para Azure Security Center facilita la búsqueda de alertas de seguridad, recomendaciones, recursos atacados y tendencias en función de un conjunto de datos que se ha creado para los informes."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Obtención de información mediante los datos de Azure Security Center con Power BI
El [panel de Power BI](http://aka.ms/azure-security-center-power-bi) para Azure Security Center le permite visualizar, analizar y filtrar las recomendaciones y alertas de seguridad desde cualquier lugar, incluido su dispositivo móvil. Utilice el panel de Power BI para mostrar tendencias y patrones de ataque. Consulte alertas de seguridad por recursos o dirección IP de origen y riesgos de seguridad no solucionados por recursos o antigüedad. Puede también recopilar recomendaciones y alertas de seguridad de Security Center junto con otros datos de diversas maneras, por ejemplo con los [registros de auditoría de Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) y la [auditoría de Base de datos SQL de Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/), ya que ambos ofrecen paneles de Power BI, o puede exportar estos datos a Excel para obtener informes fácilmente sobre el estado de seguridad de los recursos en la nube.

> [AZURE.NOTE] La información de este documento se aplica a la versión preliminar del Centro de seguridad de Azure.


##Utilización del panel de Azure Security Center para acceder a Power BI
También puede utilizar el panel de Azure Security Center para tener acceso a informes de Power BI. Siga estos pasos para realizar esta tarea:

1. En el panel de **Azure Security Center**, haga clic en el botón **Explore in Power BI** (Explorar en Power BI).

	![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig9-new.png)

2. La hoja **Explore in Power BI** (Explorar en Power BI) se abre a la derecha como se muestra a continuación:

	![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig2-new.png)

3. Si va a crear el panel de Power BI por primera vez, puede elegir una de las siguientes opciones en la hoja Explore in Power BI (Explorar en Power BI):

	- **Security insights dashboard** (Panel de información de seguridad): elija esta opción si desea crear un panel que incluya el estado de seguridad, los subprocesos y las detecciones. Se trata de una opción más habitual para el rol de DevOps, cuya responsabilidad es analizar su estado de protección y las alertas detectadas en las suscripciones.
	- **Policy management dashboard** (Panel de administración de directivas): elija esta opción si desea explorar la administración y la aplicación de directivas. Se trata de una opción más habitual para TI central, que se concentra más en el control. Pueden usar este panel para obtener visibilidad e información sobre el cumplimiento de las directivas de seguridad en toda su organización.
	- Si ya tiene un panel de Power BI, haga clic en **Go to your current Power BI dashboard** (Ir a su panel de Power BI actual).

4. Para este ejemplo, haga clic en **Security insights dashboard** (Panel de información de seguridad) y aparecerá la ventana siguiente:

	![Security Insights dashboard (Panel de información de seguridad) de Azure Security Center](./media/security-center-powerbi/security-center-powerbi-fig3-new.png)

5. Asegúrese de que el valor de **Método de autenticación** sea **oAuth2** y haga clic en **Iniciar sesión**.
6. Se abre la ventana **Power BI**, donde verá un informe con una estructura parecida al siguiente:
	
	![Security Insights dashboard (Panel de información de seguridad)](./media/security-center-powerbi/security-center-powerbi-fig5.png)

> [AZURE.NOTE] Hay una actualización del informe programada para realizarse a diario; si se produce un error al actualizar, consulte [Potential Refresh Issues with the Azure Security Center Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/) (Posibles problemas de actualización en Azure Security Center con Power BI), para más información sobre cómo solucionar problemas.

Aquí se ve el número de alertas de seguridad y recomendaciones, así como el número de máquinas virtuales, bases de datos SQL de Azure y recursos de red supervisados por Azure Security Center.

Un vínculo a Azure Security Center le redirigirá al Portal de Azure. Los gráficos facilitan la visualización de la información acerca de las recomendaciones y alertas de seguridad, incluidas:

- Estado de seguridad de los recursos
- Información general de recomendaciones pendientes
- Recomendaciones de máquina virtual
- Alertas a lo largo del tiempo
- Recursos atacados
- Direcciones IP atacadas

Detrás de cada gráfico hay información adicional. Seleccione un icono para ver más información. Por ejemplo, el icono Resource Security Health (Estado de seguridad de los recursos) muestra detalles adicionales sobre recomendaciones pendientes por recurso, tal como se muestra a continuación:

![Recomendaciones](./media/security-center-powerbi/security-center-powerbi-fig6.png)

Si hace clic en cualquier línea de este gráfico, las demás se atenuarán y podrá centrarse en la que ha seleccionado. Para volver al panel, haga clic en **Azure Security Center** en la opción **Paneles** del panel izquierdo de esta página.

> [AZURE.NOTE] Si desea personalizar los informes agregando más campos o cambiando los objetos visuales existentes, puede editar el informe. Lea el artículo [Interacción con un informe en la vista de edición en Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) para más información.

Los iconos para **Alerts over Time (Alertas a lo largo del tiempo), Attacked Resources** (Recursos atacados) y **Attacked IPs** (Direcciones IP atacadas) proporcionarán un resultado similar al hacer clic en cada uno de ellos. Esto se debe a que el informe agrega información acerca de las tres variables y la llama **Resources under Attack** (Recursos bajo ataque) tal como se muestra a continuación:

![Resources under attack (Recursos bajo ataque)](./media/security-center-powerbi/security-center-powerbi-fig7.png)

En este momento puede también guardar una copia de este informe, imprimirlo o publicarlo en la Web mediante las opciones del menú **Archivo**.

![Menú Archivo](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## Exploración de los datos de Azure Security Center con los servicios de Power BI

Conéctese a los [servicios de paquete de contenido de Power BI](https://msit.powerbi.com/groups/me/getdata/services) en Power BI y siga estos pasos:

1. En la ventana **Content Pack for Power BI** (Paquete de contenido para Power BI), verá dos opciones, como se muestra a continuación.

	![Paquete de contenido para Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

2. Para este ejemplo, haga clic en **Obtener** en el icono **Azure Security Center Policy Management** (Administración de directivas de Azure Security Center).

3. En la ventana **Connect to Azure Security Center Policy Management** (Conectarse a Administración de directivas de Azure Security Center), asegúrese de seleccionar **oAuth2** en la lista desplegable **Método de autenticación**, como se ve a continuación, y haga clic en el botón **Iniciar sesión**.

	![Ventana Administración de directivas](./media/security-center-powerbi/security-center-powerbi-fig4-new.png)

4. Se le redirigirá a una página de autenticación donde debe escribir las credenciales que usa para conectarse a Azure Security Center. Una vez completado el proceso de autenticación, Power BI iniciará la importación de datos para generar los informes. Durante este tiempo puede ver el mensaje siguiente en la esquina derecha del explorador:

	![Conexión a Azure Security Center mediante Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

	>[AZURE.NOTE] Cuando se crea por primera vez el panel, puede tardar más de lo habitual, sobre todo en los escenarios con varias suscripciones.

5. Una vez finalizado el proceso, se cargará el panel de Power BI para Azure Security Center con el informe **Administración de directivas**.


## Pasos siguientes
En este documento, ha aprendido a usar Power BI en Azure Security Center. Para obtener más información sobre el Centro de seguridad de Azure, consulte los siguientes recursos:

- [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md): aprenda a planear la adopción de Azure Security Center.
- [Establecimiento de directivas de seguridad en el Centro de seguridad de Azure](security-center-policies.md): obtenga información sobre cómo configurar los ajustes de seguridad en el Centro de seguridad de Azure.
- [Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure](security-center-managing-and-responding-alerts.md): obtenga información sobre cómo administrar y responder a alertas de seguridad.
- [Preguntas más frecuentes sobre el Centro de seguridad de Azure](security-center-faq.md): Encuentre las preguntas más frecuentes sobre el uso del servicio.
- [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/): Encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.

<!---HONumber=AcomDC_0608_2016-->