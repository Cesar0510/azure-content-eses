<properties
	pageTitle="Escalado vertical de conjuntos de escalado de máquinas virtuales de Azure | Microsoft Azure"
	description="Cómo escalar verticalmente una máquina virtual en respuesta a las alertas de supervisión con Automatización de Azure"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="gbowerman"
	manager="madhana"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/14/2016"
	ms.author="guybo"/>

# Autoescala vertical con conjuntos de escalado de máquinas virtuales

Este artículo describe cómo escalar verticalmente [conjuntos de escalado de máquinas virtuales](https://azure.microsoft.com/services/virtual-machine-scale-sets/) de Azure con o sin reaprovisionamiento. Para el escalado vertical de máquinas virtuales que no están en conjuntos de escalado, consulte [Vertically scale Azure virtual machine with Azure Automation](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md) (Escalado vertical de máquinas virtuales de Azure con Automatización de Azure).

El escalado vertical, también conocido como _ampliación vertical_ y _reducción vertical_, significa aumentar o disminuir el tamaño de las máquinas virtuales en respuesta a una carga de trabajo. Compare esto con el [escalado horizontal](./virtual-machine-scale-sets-autoscale-overview.md), también denominado _ampliación horizontal_ y _reducción horizontal_, donde se modifica el número de máquinas virtuales según la carga de trabajo.

Reaprovisionar significa quitar una máquina virtual existente y reemplazarla por una nueva. Al aumentar o disminuir el tamaño de las máquinas virtuales en un conjunto de escalado de máquinas virtuales, en algunos casos puede que quiera cambiar el tamaño de las máquinas virtuales existentes y conservar los datos, aunque en otros casos puede que necesite implementar nuevas máquinas virtuales con el nuevo tamaño. Este documento describe ambos casos.

El escalado vertical puede resultar útil cuando:

- Un servicio integrado en máquinas virtuales se está infrautilizando (por ejemplo, los fines de semana). Reducir el tamaño de la máquina virtual puede reducir los costos mensuales.
- Aumentar el tamaño de la máquina virtual para hacer frente a una mayor demanda sin crear máquinas virtuales adicionales.

Puede configurar el escalado vertical para que se desencadene en función de las alertas basadas en métricas de su conjunto de escalado de máquinas virtuales. Cuando la alerta se activa, inicia un webhook que desencadena un Runbook que puede ampliar o reducir verticalmente el conjunto de escalado. El escalado vertical puede configurarse siguiendo estos pasos:

1. Cree una cuenta de Automatización de Azure con funciones de ejecución.
2. Importe a la suscripción los Runbooks de escalado vertical de Automatización de Azure para los conjuntos de escalado de máquinas virtuales.
3. Agregue un webhook al Runbook.
4. Agregue una alerta a su conjunto de escalado de máquinas virtuales mediante una notificación de webhook.

> [AZURE.NOTE] La autoescala vertical solo se puede realizar en determinados intervalos de tamaños de máquina virtual. Puede elegir escalar entre los siguientes pares de tamaños:

>| Pares de escalado de tamaños de VM | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## Creación de una cuenta de Automatización de Azure con funciones de ejecución

Lo primero que debe hacer es crear una cuenta de Automatización de Azure que hospedará los Runbooks que se usan para escalar las instancias del conjunto de escalado de máquinas virtuales. Recientemente, [Automatización de Azure](https://azure.microsoft.com/services/automation/) presentó la característica "Cuenta de ejecución", que facilita la configuración de la entidad de servicio para ejecutar los Runbooks automáticamente en nombre de un usuario. Encontrará más información al respecto en el siguiente artículo:

* [Autenticación de Runbooks con una cuenta de ejecución de Azure](../automation/automation-sec-configure-azure-runas-account.md)

## Importación de Runbooks de escalado vertical de Automatización de Azure a la suscripción

Los Runbooks necesarios para el escalado vertical de los conjuntos de escalado de máquinas virtuales están publicados en la galería de Runbooks de Automatización de Azure. Para importarlos a su suscripción, siga los pasos de este artículo:

* [Galerías de runbooks y módulos para la automatización de Azure](../automation/automation-runbook-gallery.md)

Elija la opción Examinar la galería en el menú Runbooks:

![Runbooks que desea importar][runbooks]

Se muestran los Runbooks que es necesario importar. Seleccione el Runbook en función de si desea un escalado vertical con o sin reaprovisionamiento:

![Galería de Runbooks][gallery]

## Agregar un webhook al runbook.

Una vez importados los Runbooks, deberá agregar un webhook al Runbook para que una alerta proveniente de un conjunto de escalado de máquinas virtuales pueda desencadenarlo. En este artículo puede leer los detalles sobre cómo crear un webhook para el Runbook:

* [Webhooks de Automatización de Azure ](../automation/automation-webhooks.md)

> [AZURE.NOTE] Asegúrese de copiar el URI del webhook antes de cerrar el cuadro de diálogo del webhook, porque lo necesitará en la siguiente sección.

## Incorporación de una alerta al conjunto de escalado de máquinas virtuales

El siguiente es un script de PowerShell que muestra cómo agregar una alerta a un conjunto de escalado de máquinas virtuales. Consulte el siguiente artículo para obtener el nombre de la métrica para desencadenar la alerta en: [Métricas comunes de escalado automático de Azure Insights](../azure-portal/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [AZURE.NOTE] Se recomienda configurar un período de tiempo razonable para la alerta con el fin de evitar activar el escalado vertical, y las interrupciones de servicio asociadas, con demasiada frecuencia. Considere un intervalo mínimo de 20 a 30 minutos. Considere la posibilidad de usar escalado horizontal si necesita evitar cualquier interrupción.

Para más información acerca de cómo crear alertas, consulte los artículos siguientes:

* [Ejemplos de inicio rápido de PowerShell de Azure Insights](../azure-portal/insights-powershell-samples.md)
* [Ejemplos de inicio rápido de CLI multiplataforma de Azure Insights](../azure-portal/insights-cli-samples.md)

## Resumen

En este artículo se mostraron ejemplos sencillos de escalado vertical. Con estos bloques de creación (cuenta de automatización, Runbooks, webhooks, alertas) puede conectar una gran variedad de eventos con un conjunto personalizado de acciones.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png

<!---HONumber=AcomDC_0420_2016-->