<properties
	pageTitle="Versión preliminar de Servicios de dominio de Azure Active Directory: guía de administración | Microsoft Azure"
	description="Administración de dominios administrados con Servicios de dominio de Azure Active Directory"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="maheshu"/>

# Administración de un dominio administrado con Servicios de dominio de Azure AD
Este artículo muestra cómo administrar un dominio administrado con Servicios de dominio de Azure AD.

## Tareas administrativas que puede realizar en un dominio administrado
Para empezar, eche un vistazo a las tareas administrativas que puede realizar en un dominio administrado. A los miembros del grupo "Administradores del controlador de dominio de AAD" se les conceden privilegios en el dominio administrado que les permiten realizar tareas como:

- Unir máquinas al dominio.

- Configurar el GPO integrado para los contenedores Equipos y Usuarios en el dominio.

- Administrar DNS en el dominio.

- Crear unidades organizativas personalizadas en el dominio.

- Obtener acceso administrativo a los equipos unidos al dominio administrado.


## Privilegios de administrador que no tiene en un dominio administrado
El dominio es administrado por Microsoft, incluidas actividades como aplicación de revisiones, supervisión, realizar copias de seguridad, etc. Por lo tanto, el dominio está bloqueado y no tiene privilegios para realizar ciertas tareas administrativas en él. A continuación se proporcionan algunos ejemplos de tareas que no puede realizar.

- No tiene privilegios de administrador de dominio o administrador de organización para el dominio administrado.

- No puede extender el esquema del dominio administrado.

- No se puede conectarse a controladores de dominio mediante el Escritorio remoto.

- No puede agregar controladores de dominio al dominio.


## Aprovisionamiento de una máquina virtual unida a un dominio para la administración remota el dominio administrado
Los dominios administrados con Servicios de dominio de Azure AD pueden administrarse mediante las conocidas herramientas administrativas de Active Directory, como el Centro de administración de Active Directory (ADAC) o AD PowerShell. Los administradores de inquilinos no tienen privilegios para conectarse a controladores de dominio en el dominio administrado mediante el Escritorio remoto. Por lo tanto, los miembros del grupo "Administradores del controlador de dominio de AAD" pueden administrar dominios administrados de forma remota mediante las herramientas administrativas de AD desde un equipo cliente/Windows Server que se ha unido al dominio administrado. Las herramientas administrativas de AD pueden instalarse como parte de la característica opcional Herramientas de administración remota del servidor (RSAT) en Windows Server y equipos cliente unidos al dominio administrado.

El primer paso es configurar una máquina virtual de Windows Server que se ha unido al dominio administrado. Para ver instrucciones para hacerlo, consulte el artículo [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).

### Administración remota del dominio administrado desde un equipo cliente (p. ej. Windows 10)
Tenga en cuenta que en las instrucciones de este artículo se usa una máquina virtual de Windows Server para administrar el dominio administrado con AAD-DS. Sin embargo, también puede elegir usar una máquina virtual de cliente de Windows (p. ej. Windows 10) para hacerlo.

También puede [instalar Herramientas de administración remota del servidor(RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) en una máquina virtual cliente de Windows siguiendo las instrucciones en TechNet.


## Instalación de herramientas de administración de Active Directory en la máquina virtual
Realice los pasos siguientes para instalar las herramientas de administración de Active Directory en la máquina virtual unida al dominio. Para más detalles para [Implementar Herramientas de administración remota del servidor](https://technet.microsoft.com/library/hh831501.aspx), consulte TechNet.

1. Vaya al nodo **Máquinas virtuales** en el Portal de Azure clásico. Seleccione la máquina virtual que acaba de crear y haga clic en **Conectar** en la barra de comandos situada en la parte inferior de la ventana.

    ![Conexión a máquina virtual de Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. El portal clásico le solicitará que abra o guarde un archivo .rdp, que se utiliza para conectarse a la máquina virtual. Haga clic en el archivo .rdp cuando haya terminado de descargarse.

3. En el aviso de inicio de sesión, utilice las credenciales de un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Por ejemplo, en nuestro caso "bob@domainservicespreview.onmicrosoft.com".

4. En la pantalla Inicio, abra **Administrador del servidor**. Haga clic en **Agregar roles y características** en el panel central de la ventana Administrador del servidor.

    ![Inicio del Administrador del servidor en la máquina virtual](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)

5. En la página **Antes de comenzar** del **Asistente para agregar roles y características**, haga clic en **Siguiente**.

    ![Página Antes de comenzar](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)

6. En la página **Tipo de instalación**, deje la opción **Instalación basada en características o en roles** activada y haga clic en **Siguiente**.

	![Página Tipo de instalación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)

7. En la página **Selección de servidor**, seleccione la máquina virtual actual del grupo de servidores y haga clic en **Siguiente**.

	![Página Selección de servidor](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)

8. En la página **Roles de servidor**, haga clic en **Siguiente**. Se omitirá esta página ya que no se van a instalar roles en el servidor.

9. En la página **Características**, haga clic para expandir el nodo **Herramientas de administración remota del servidor** y, a continuación, haga clic para expandir el nodo **Herramientas de administración de roles**. Seleccione la característica **Herramientas de AD DS y AD LDS** en la lista de herramientas de administración de roles, como se muestra a continuación.

	![Página Características](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)

10. En la página **Confirmación** haga clic en **Instalar** para instalar la característica de herramientas de AD y AD LDS en la máquina virtual. Cuando finalice correctamente la instalación de la característica, haga clic en **Cerrar** para salir del **Asistente para agregar roles y características**.

	![Página de confirmación](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)


## Exploración del dominio administrado
Ahora que las herramientas administrativas de AD están instaladas en la máquina virtual unida a dominio, puede usarlas para explorar y administrar una unidad organizativa en el dominio administrado.

1. En la pantalla Inicio, haga clic en **Herramientas administrativas**. Debería ver las herramientas administrativas de AD instaladas en la máquina virtual.

	![Herramientas administrativas instaladas en el servidor](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Haga clic en **Centro de administración de Active Directory**.

	![Centro de administración de Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Haga clic en el nombre de dominio en el panel izquierdo (p. ej. "contoso100.com") para explorar el dominio. Observe dos contenedores denominados "AADDC equipos" y "Usuarios de AADDC" respectivamente.

    ![ADAC: ver dominio](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)

4. Haga clic en el contenedor **Usuarios de AADDC** para ver todos los usuarios y grupos que pertenecen al dominio administrado. Debería ver las cuentas de usuario y grupos de su inquilino de Azure AD en este contenedor. Observe que en este ejemplo hay una cuenta de usuario para el usuario "bob" y un grupo llamado "Administradores del controlador de dominio de AAD" disponibles en este contenedor.

    ![ADAC: usuarios del dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)

5. Haga clic en el contenedor llamado **AADDC equipos** para ver los equipos unidos a este dominio administrado. Debería ver una entrada para la máquina virtual actual, que está unida al dominio. Las cuentas de equipo para todos los equipos unidos al dominio administrado con Servicios de dominio de Azure AD aparecerán en este contenedor "AADDC equipos".

    ![ADAC: equipos unidos al dominio](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<!---HONumber=AcomDC_0420_2016-->