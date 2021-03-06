<properties
	pageTitle="Uso de un dispositivo real con el SDK de puerta de enlace | Microsoft Azure"
	description="Tutorial de SDK de puerta de enlace del Centro de IoT de Azure mediante un dispositivo SensorTag de Texas Instruments para enviar datos al Centro de IoT a través de una puerta de enlace que se ejecuta en un módulo de cálculo Intel Edison"
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="05/31/2016"
     ms.author="cstreet"/>


# SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo real a través de Linux

Este tutorial de [ejemplo de baja energía de Bluetooth][lnk-ble-samplecode] muestra cómo usar el [SDK de puerta de enlace de IoT de Microsoft Azure][lnk-sdk] para la telemetría directa de dispositivos a la nube al Centro de IoT desde un dispositivo físico y cómo enrutar comandos desde el Centro de IoT a un dispositivo físico.

En este tutorial, se describen los siguientes procedimientos:

* **Arquitectura**: información de arquitectura importante sobre el ejemplo de baja energía de Bluetooth.

* **Compilación y ejecución**: los pasos necesarios para compilar y ejecutar el ejemplo.

## Arquitectura

El tutorial muestra cómo compilar y ejecutar una puerta de enlace de IoT en un módulo de cálculo Intel Edison que ejecuta Linux. La puerta de enlace se ha creado mediante el SDK de puerta de enlace de IoT. El ejemplo usa un dispositivo de baja energía de Bluetooth (BLE) SensorTag de Texas Instruments para recopilar datos de temperatura.

Al ejecutar la puerta de enlace:

- Se conecta a un dispositivo SensorTag mediante el protocolo de baja energía de Bluetooth (BLE).
- Se conecta al centro de IoT mediante el protocolo AMQP.
- Reenvía telemetría del dispositivo SensorTag al centro de IoT.
- Enruta los comandos desde el centro de IoT al dispositivo SensorTag.

La puerta de enlace contiene los siguientes módulos:

- Un *módulo BLE* que interactúa con un dispositivo BLE para recibir sus datos de temperatura y enviarle comandos.
- Un *módulo registrador* que genera diagnósticos de bus de mensajes.
- Un *módulo de asignación de identidad* que traduce entre direcciones MAC de los dispositivos BLE y las identidades de los dispositivos de centro de IoT de Azure.
- Un *módulo HTTP del centro de IoT* que carga los datos de telemetría en un centro de IoT y recibe los comandos del dispositivo de un centro de IoT.
- Un *módulo de impresora BLE* que interpreta la telemetría del dispositivo BLE e imprime los datos con formato en la consola para habilitar la solución de problemas y la depuración.

### Cómo fluyen los datos a través de la puerta de enlace

El siguiente diagrama de bloques muestra la canalización del flujo de datos de carga para telemetría:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

Etapas de la trayectoria de un elemento de telemetría desde un dispositivo BLE hasta un centro de IoT:

1. El dispositivo BLE genera un ejemplo de temperatura y lo envía a través de Bluetooth al módulo BLE de la puerta de enlace.
2. El módulo BLE recibe el ejemplo y lo publica en el bus de mensajes junto con la dirección MAC del dispositivo.
3. El módulo de asignación de identidad recoge este mensaje del bus y usa una tabla interna para convertir la dirección MAC del dispositivo en una identidad de dispositivo del centro de IoT (Id. de dispositivo y clave de dispositivo). A continuación, publica un mensaje nuevo en el bus que contiene los datos de ejemplo de la temperatura, la dirección MAC del dispositivo, el identificador de dispositivo y la clave del dispositivo.
4. El módulo HTTP de centro de IoT recibe este mensaje nuevo (generado por el módulo de asignación de identidad) del bus de mensajes y lo publica en el centro de IoT.
5. El módulo registrador recoge todos los mensajes del bus en un archivo de disco.

El siguiente diagrama de bloques muestra la canalización del flujo de datos de comandos para el dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. El módulo HTTP del centro de IoT sondea periódicamente el centro de IoT en busca de nuevos mensajes de comando.
2. Cuando el módulo HTTP del centro de IoT recibe un nuevo mensaje de comando, lo publica en el bus de mensajes.
3. El módulo de asignación de identidad recoge este mensaje del bus y usa una tabla interna para convertir el identificador del dispositivo del centro de IoT en una dirección MAC del dispositivo. A continuación, publica un mensaje nuevo en el bus que incluye la dirección MAC del dispositivo de destino en el mapa de propiedades del mensaje.
4. El módulo BLE recoge este mensaje y ejecuta la instrucción de E/S al comunicarse con el dispositivo BLE.
5. El módulo registrador recoge todos los mensajes del bus en un archivo de disco.

## Preparar el hardware

Este tutorial se supone que usa un dispositivo [SensorTag de Texas Instruments](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a un panel Intel Edison.

### Configuración del panel Edison

Antes de comenzar, asegúrese de que puede conectar el dispositivo Edison a la red inalámbrica. Para configurar el dispositivo Edison, debe conectarlo a un equipo host. Intel proporciona guías de introducción para los siguientes sistemas operativos:

- [Get Started with the Intel Edison Development Board on Windows 64-bit][lnk-setup-win64] (Introducción a la placa de desarrollo de Intel Edison en Windows de 64 bits).
- [Get Started with the Intel Edison Development Board on Windows 32-bit][lnk-setup-win32] (Introducción a la placa de desarrollo de Intel Edison en Windows de 32 bits).
- [Get Started with the Intel Edison Development Board on Mac OS X][lnk-setup-osx] (Introducción a la placa de desarrollo de Intel Edison en Mac OS X).
- [Getting Started with the Intel® Edison Board on Linux][lnk-setup-linux] (Introducción a la placa de Intel® Edison en Linux).

Para configurar el dispositivo Edison y familiarizarse con él, debe completar todos los pasos de estos artículos de introducción, excepto el último paso, "Elija IDE", que no es necesario para este tutorial. Al final del proceso de instalación de Edison tiene:

- Grabado el firmware más reciente en el dispositivo Edison.
- Establecida una conexión en serie del host al dispositivo Edison.
- Ejecutado el script **configure\_edison** para establecer una contraseña y habilitar Wi-Fi en el dispositivo Edison.

### Habilitación de la conectividad al dispositivo SensorTag desde la placa Edison

Antes de ejecutar el ejemplo, debe comprobar que la placa Edison puede conectarse al dispositivo SensorTag.

Primero debe actualizar la versión del software BlueZ en el dispositivo Edison. Tenga en cuenta que, aunque ya tenga la versión 5.37 instalada, debe completar los pasos siguientes para garantizar que está correcta:

1. Detenga el demonio de bluetooth en ejecución.
    
    ```
    systemctl stop bluetooth
    ```

2. Descargue y extraiga el [código fuente](http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz) para la versión 5.37 de BlueZ.
    
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    tar -xvf bluez-5.37.tar.xz
    cd bluez-5.37
    ```

3. Cree e instale BlueZ.
    
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    make
    make install
    ```

4. Modifique el archivo **/lib/systemd/system/bluetooth.service** para cambiar la configuración del servicio *systemd* para el bluetooth, de forma que apunte al nuevo demonio de bluetooth. Reemplace el valor del atributo **ExecStart** para que tenga el siguiente aspecto:
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

5. Reiniciar el dispositivo Edison.

Lo siguiente que debe comprobar es que el dispositivo Edison se pueda conectar al dispositivo SensorTag.

1. Desbloquee el bluetooth en el dispositivo Edison y compruebe que el número de versión es **5.37**.
    
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```

2. Ejecute el comando **bluetoothctl**. Debería mostrarse una salida similar a esta:
    
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```

3. Ahora se encuentra en un shell interactivo de bluetooth. Escriba el comando **scan on** para buscar dispositivos bluetooth. Debería mostrarse una salida similar a esta:
    
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```

4. Presione el botón pequeño (el LED verde debe parpadear) para que el dispositivo SensorTag sea visible. El dispositivo Edison debe detectar el dispositivo SensorTag:
    
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
    
    En este ejemplo, puede ver que la dirección MAC del dispositivo SensorTag es **A0:E6:F8:B5:F6:00**.

5. Escriba el comando **scan off** para desactivar la búsqueda.
    
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```

6. Escriba **connect <MAC address>** para conectarse al dispositivo SensorTag con la dirección MAC. Tenga en cuenta que la salida de ejemplo siguiente está abreviada:
    
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
    
    Nota: Con el comando **list-attributes** puede volver a enumerar las características GATT del dispositivo.

7. Ahora puede desconectarse del dispositivo con el comando **disconnect** y salir del shell de bluetooth con el comando **quit**:
    
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Ya está listo para ejecutar el ejemplo de puerta de enlace de BLE en el dispositivo Edison.

## Ejecución del ejemplo de puerta de enlace de BLE

Para ejecutar el ejemplo BLE en el dispositivo Edison, debe realizar tres tareas:

- Configurar dos dispositivos de ejemplo en su centro de IoT.
- Crear el SDK de puerta de enlace en el dispositivo Edison.
- Configurar y ejecutar el ejemplo de BLE en el dispositivo Edison.

En el momento de la escritura, el SDK solo es compatible con puertas de enlace que usen módulos BLE en Linux.

### Configuración de dos dispositivos de ejemplo en su Centro de IoT

- [Cree un Centro de IoT][lnk-create-hub] en su suscripción de Azure, para este tutorial necesitará el nombre de su centro. Si aún no tiene una suscripción de Azure, puede obtener una [cuenta gratuita][lnk-free-trial].
- Agregue un dispositivo denominado **SensorTag\_01** a su Centro de IoT y tome nota de la clave y el identificador del dispositivo. Puede usar el [explorador de dispositivos o del Centro de IoT][lnk-explorer-tools] para agregar el dispositivo al Centro de IoT que creó en el paso anterior y recuperar la clave. Tendrá que asignar este dispositivo al dispositivo SensorTag cuando configure la puerta de enlace.

### Creación del SDK de puerta de enlace en el dispositivo Edison

La versión de **git** del dispositivo Edison no admite submódulos. Para descargar el código fuente completo del SDK de puerta de enlace para el dispositivo Edison tiene dos opciones:

- Opción 1: Clonar el repositorio del [SDK de puerta de enlace IoT de Microsoft Azure][lnk-sdk] en su dispositivo Edison y clonar manualmente el repositorio para cada submódulo.
- Opción 2: Clonar el repositorio del [SDK de puerta de enlace IoT de Microsoft Azure][lnk-sdk] en un dispositivo de escritorio donde **git** admite submódulos y copiar todo el repositorio, submódulos incluidos, en el dispositivo Edison.

Si elige la opción 2, use los siguientes comandos **git** para clonar el SDK de puerta de enlace y todos sus submódulos:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

A continuación, comprima el repositorio local en un solo archivo antes de copiarlo al dispositivo Edison. Puede usar una utilidad como **pscp**, que se incluye con **Putty**, para copiar el archivo de almacenamiento en el dispositivo Edison. Por ejemplo:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Cuando haya una copia de todo el repositorio del SDK de puerta de enlace en el dispositivo Edison, puede crear el SDK desde la carpeta que lo contiene con el comando siguiente:

```
./tools/build.sh
```

### Configuración y ejecución del ejemplo de BLE en el dispositivo Edison

Para arrancar y ejecutar el ejemplo, debe configurar todos los módulos que participan en la puerta de enlace. Esta configuración se proporciona en un archivo JSON y sirve para los cinco módulos. Se proporciona un archivo JSON de ejemplo en el repositorio, denominado **gateway\_sample.json**, como punto de partida para crear su propio archivo de configuración. Este archivo se encuentra en la carpeta **samples/ble\_gateway\_hl/src** de la copia local del repositorio del SDK de puerta de enlace.

En las secciones siguientes se describe cómo modificar este archivo de configuración para el ejemplo de BLE y se supone que el repositorio del SDK de puerta de enlace se encuentra en la carpeta **/home/root/azure-iot-gateway-sdk/** del dispositivo Edison. Si el repositorio se encuentra en otro lugar, ajuste las rutas de acceso según corresponda:


#### Configuración del registrador

Suponiendo que el repositorio de la puerta de enlace se encuentra en la carpeta **/home/root/azure-iot-gateway-sdk/**, configure el módulo del registrador como sigue:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### Configuración del módulo BLE

La configuración de ejemplo para el dispositivo BLE supone un dispositivo SensorTag de Texas Instruments. Cualquier dispositivo BLE estándar que funcione como GATT periférico debe valer, pero será necesario actualizar los identificadores de las características GATT y los datos (para las instrucciones de escritura). Agregue la dirección MAC del dispositivo SensorTag:

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### Módulo HTTP del Centro de IoT

Agregue el nombre de su Centro de IoT. El sufijo suele ser **azure-devices.net**:

```json
{
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothubhttp/libiothubhttp_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>"
  }
}
```

#### Configuración del módulo de asignación de identidad

Agregue la dirección MAC del dispositivo SensorTag, y el identificador del dispositivo y la clave del dispositivo **SensorTag\_01** agregado al Centro de IoT:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### Configuración de la impresora BLE

```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

Para ejecutar el ejemplo, ejecute el **ble\_gateway\_hl** binario pasando la ruta de acceso al archivo de configuración de JSON. Si ha usado el archivo **gateway\_sample.json**, el comando de ejecución tiene este aspecto:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Presione el botoncito del dispositivo SensorTag para que se pueda detectar antes de ejecutar el ejemplo.

Cuando ejecute el ejemplo, puede usar el [explorador de dispositivos o el explorador del Centro de IoT][lnk-explorer-tools] para supervisar los mensajes que la puerta de enlace remite desde el dispositivo SensorTag.

## Envío de mensajes de nube a dispositivo

El módulo BLE también admite instrucciones envío del Centro de IoT de Azure al dispositivo. Puede usar el [explorador de dispositivos del Centro de IoT de Azure](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) o el [explorador del Centro de IoT] (https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) para enviar mensajes JSON que el módulo de puerta de enlace BLE transmita al dispositivo BLE. Por ejemplo, si usa el dispositivo SensorTag de Texas Instruments, desde el Centro de IoT puede enviar los siguientes mensajes JSON al dispositivo.

- Restablecer todos los LED y el timbre (desactivarlos)

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```

- Configurar E/S como "remoto"

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Encender el LED rojo

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

- Encender el LED verde

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

- Encender el timbre

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

El comportamiento predeterminado de un dispositivo que usa el protocolo HTTP para conectarse al Centro de IoT es buscar nuevos comandos cada 25 minutos. Por lo tanto, si envía varios comandos, debe esperar 25 minutos para que el dispositivo reciba cada uno de ellos.

> [AZURE.NOTE] La puerta de enlace también busca nuevos comandos siempre que se inicia para que pueda forzar el procesamiento de un comando al detener e iniciar la puerta de enlace.

## Pasos siguientes

Para más información, consulte [Azure IoT Gateway SDK][lnk-sdk] (SDK de puerta de enlace de IoT de Azure).

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

<!---HONumber=AcomDC_0601_2016-->