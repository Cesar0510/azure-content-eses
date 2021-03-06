<properties
	pageTitle="Adición de un repositorio de artefactos Git a un laboratorio | Microsoft Azure"
	description="Adición de un repositorio Git de GitHub o Visual Studio Team Services para los artefactos personalizados de DevTest Labs"
	services="devtest-lab,virtual-machines,visual-studio-online"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Adición de un repositorio de artefactos Git a un laboratorio

> [AZURE.NOTE] Vea el vídeo que acompaña a este artículo: [How to add your private artifacts repository to a lab in DevTest Labs](/documentation/videos/how-to-add-your-private-artifacts-repository-in-a-devtest-lab) (Cómo agregar el repositorio de artefactos privados a un laboratorio de DevTest Labs).

## Información general

De forma predeterminada, un laboratorio incluye artefactos del repositorio oficial de artefactos de Azure DevTest Labs. Puede agregar un repositorio de artefactos de Git al laboratorio para incluir los artefactos que crea el equipo. El repositorio se puede hospedar en [GitHub](https://github.com) o en [Visual Studio Team Services (VSTS)](https://visualstudio.com).

- Para obtener información sobre cómo crear un repositorio de GitHub, consulte [Entrenamiento militar de GitHub](https://help.github.com/categories/bootcamp/).
- Para obtener información sobre cómo crear un proyecto Team Services con un repositorio de Git, vea [Conexión con Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

En la siguiente captura de pantalla se muestra un ejemplo del aspecto que podría tener en GitHub un repositorio que contiene artefactos: ![Repositorio de artefactos de GitHub de ejemplo](./media/devtest-lab-add-artifact-repo/devtestlab-github-artifact-repo-home.png)

## Adición de un repositorio de artefactos de GitHub al laboratorio

Para agregar un repositorio de artefactos de GitHub al laboratorio, primero debe obtener la dirección URL de clonación HTTPS y el token de acceso personal desde el repositorio de artefactos, y después escribir esa información en el laboratorio.

### Obtención de la dirección URL de clonación del repositorio de GitHub y el token de acceso personal

1. En la página principal del repositorio de GitHub que contiene los artefactos de equipo, guarde la **dirección URL de clonación HTTPS** para su posterior uso.

1. Seleccione la imagen de perfil en la esquina superior derecha y seleccione **Configuración**.

1. En el menú **Configuración personal** de la izquierda, seleccione **Tokens de acceso personal**.

1. Seleccione **Generar nuevo token**.

1. En la página **New personal access token (Nuevo token de acceso personal)** escriba la información que considere oportuno en **Token description (Descripción del token)**, acepte los elementos predeterminados en el **Select scopes (Seleccionar ámbitos)** y después elija **Generate Token (Generar token)**.

1. Guarde el token generado, ya que lo necesitará más adelante.

1. Ahora puede cerrar GitHub.

###Conexión del laboratorio con el repositorio de GitHub

1. Inicie sesión en el [Portal de Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Seleccione **Examinar**, y, a continuación, seleccione **DevTest Labs** en la lista.

1. En la lista de laboratorios, seleccione el laboratorio que desee.

1. En la hoja del laboratorio, seleccione **Configuración**.

1. En la hoja **Configuración** del laboratorio, seleccione **Artifacts Repository** (Repositorio de artefactos).

1. En la hoja **Repositorio de artefactos**:

    1. En **Nombre**, escriba un nombre para el repositorio.
    1. En **URL de clonación de Git**, escriba la dirección URL de clonación guardada.
    2. En **Ruta de acceso de carpeta**, escriba la ruta de acceso de la carpeta en el repositorio de los artefactos que contiene los artefactos.
    3. En **Token de acceso personal**, escriba el token de acceso personal guardado para el repositorio de artefactos.
    4. Seleccione **Guardar**.

Los artefactos del repositorio se muestran ahora en la hoja **Agregar artefactos**.

## Adición de un repositorio de artefactos de Git de Visual Studio al laboratorio

Para agregar un repositorio de artefactos de Git de Visual Studio al laboratorio, primero debe obtener la dirección URL de clonación HTTPS y el token de acceso personal desde el repositorio de artefactos, y después escribir esa información en el laboratorio.

### En la página web de Visual Studio del proyecto de artefacto

1. Abra la página principal de la colección de equipo (por ejemplo, `https://contoso-web-team.visualstudio.com`) y después seleccione el proyecto de artefacto.

2. En la página de inicio del proyecto, seleccione **Código**.

1. Para ver la dirección URL de clonación, en la página **Código** del proyecto, seleccione **Clonar**.

1. Guarde la dirección URL, ya que la necesitará más adelante en este tutorial.

1. Para crear un token de acceso personal, seleccione **Mi perfil** en el menú desplegable de la cuenta de usuario.

1. En la página de información de perfil, seleccione **Seguridad**.

1. En la pestaña **Seguridad** ficha, seleccione **Agregar**.

1. En la página **Crear un token de acceso personal**:

    1. Escriba la información que desee en **Descripción** para el token.
    2. Seleccione **180 días** en la lista **Expira en**.
    3. Elija **Todas las cuentas accesibles** en la lista **Cuentas**.
    4. Elija la opción **Todos los ámbitos**.
    5. Elija **Crear token**.

1. Cuando termine, el nuevo token aparecerá en la lista **Tokens de acceso personal**. Seleccione **Copiar token** y guarde el valor del token, ya que se utilizará en breve.

### En el laboratorio

1. En la hoja del laboratorio, seleccione **Configuración**.

    ![Elegir configuración.](./media/devtest-lab-add-artifact-repo/devtestlab-add-artifacts-repo-open-dtl-settings.png)

1. En la hoja **Configuración** del laboratorio, seleccione **Artifacts Repository** (Repositorio de artefactos).

1. En la hoja **Repositorio de artefactos**:

    1. En **Nombre**, escriba un nombre para mostrar para el repositorio.
    1. En **URL de clonación de Git**, escriba la dirección URL de clonación guardada.
    2. En **Ruta de acceso de carpeta**, escriba la ruta de acceso de la carpeta en el repositorio de los artefactos que contiene los artefactos.
    3. En **Token de acceso personal**, escriba el token de acceso personal guardado para el repositorio de artefactos.
    4. Seleccione **Guardar**.

<!---HONumber=AcomDC_0608_2016-->