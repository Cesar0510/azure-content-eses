<properties
	pageTitle="Orientación de renovación de certificado para los usuarios de Office 365 y Azure AD | Microsoft Azure"
	description="Este artículo explica a los usuarios de Office 365 cómo solucionar problemas con mensajes de correo electrónico que informan sobre la renovación de un certificado."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="billmath"/>


# Renovación de certificados de federación para Office 365 y Azure AD

##Información general

Para una federación correcta entre Azure AD y AD FS, los certificados usados por AD FS para firmar los tokens de seguridad en Azure AD deben coincidir con lo que está configurado en Azure AD. Cualquier error de coincidencia puede conducir a la interrupción de la relación de confianza entre AD FS y AAD. Azure AD garantiza que esta información se mantiene sincronizada cuando se implementa AD FS y Proxy de aplicación web (para el acceso de extranet).

En este artículo se proporciona información adicional para administrar los certificados de firma de tokens y mantenerlos sincronizados con Azure AD en los casos siguientes:

* No implementa el Proxy de aplicación web y, por tanto, los metadatos de federación no están disponible en la extranet.
* No utiliza la configuración predeterminada de AD FS para los certificados de firma de tokens.
* Utiliza un proveedor de identidades de terceros.

## Configuración predeterminada de AD FS para los certificados de firma de tokens

La firma de tokens y los certificados de descifrado de tokens son normalmente certificados autofirmados y son válidos durante un año. La configuración predeterminada de AD FS en relación con la firma de tokens y los certificados de descifrado de tokens incluyen un proceso de renovación automática denominado **AutoCertificateRollover**. Si utiliza AD FS 2.0 o versiones posteriores, Office 365 y Azure AD actualizan automáticamente el certificado antes de que expire.

### Notificación de renovación: notificación por correo electrónico y del portal de O365

>[AZURE.NOTE] Si recibe un correo electrónico o una notificación del portal que le solicita renovar el certificado de Office, puede seguir los pasos que se mencionan a continuación, en la sección de [administración de cambios en los certificados de firma de tokens](#managecerts) para comprobar si es necesario realizar alguna acción. Microsoft tiene constancia de un posible problema que puede dar lugar a notificaciones enviadas al usuario para la renovación de certificados, incluso cuando no se requiere ninguna acción.

Azure AD intenta supervisar los metadatos de federación y actualizar el token de firma de certificados, tal como indican los metadatos de federación. 30 días antes de la expiración de los certificados de firma de tokens, Azure AD comprobará si los certificados nuevos están disponibles mediante el sondeo de los metadatos de federación.

* Si puede sondear los metadatos de federación y recuperar los nuevos certificados correctamente, el usuario no recibirá ninguna notificación por correo electrónico ni ninguna advertencia de portal de Office 365.
* Si no se pueden recuperar los nuevos certificados de firma de tokens, ya sea porque los metadatos de federación no son accesibles o porque no está habilitada la sustitución automática de certificados, se emitirá una notificación de correo electrónico y se mostrará una advertencia en el portal de Office 365.

![Notificación del portal de O365](./media/active-directory-aadconnect-o365-certs/notification.png)

>[AZURE.IMPORTANT] Si utiliza AD FS, para garantizar la continuidad del negocio, compruebe que los servidores tienen las revisiones siguientes para que no se produzcan errores de autenticación de los problemas conocidos. Esta acción mitigará los problemas conocidos del servidor de proxy de AD FS para esta renovación y períodos de renovación futuros:
>
>Server 2012 R2: [paquete acumulativo de mayo de 2014 de Windows Server](http://support.microsoft.com/kb/2955164)
>
>Server 2008 R2 y 2012: [Se produce un error en la autenticación a través de proxy en Windows Server 2012 o Windows Server 2008 R2 SP1](http://support.microsoft.com/kb/3094446).

## Compruebe si los certificados necesitan actualización <a name="managecerts"></a>

### Paso 1: Comprobar el estado del proceso AutoCertificateRollover

En el servidor de AD FS, abra Microsoft Powershell. Compruebe que el valor AutoCertRollover está establecido en TRUE.

	Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

[AZURE.NOTE] Si usa AD FS 2.0, tendrá que ejecutar Add-Pssnapin Microsoft.Adfs.Powershell primero.

### Paso 2: Confirmar que AD FS y Azure AD están sincronizados

En el servidor de AD FS, abra el símbolo del sistema de Azure AD para Powershell y conéctese a Azure AD.

>[AZURE.NOTE] Puede descargar el módulo de Azure AD para Powershell [aquí](https://technet.microsoft.com/library/jj151815.aspx), si aún no está instalado.

	Connect-MsolService

Compruebe que los certificados configurados en AD FS y Azure AD confían en las propiedades para el dominio especificado.

	Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Si las huellas digitales en ambas salidas coinciden, se confirma que los certificados están sincronizados con Azure AD.

### Paso 3: Comprobar si el certificado está a punto de expirar

En la salida de Get-MsolFederationProperty o Get-AdfsCertificate compruebe la fecha en "No after". Si la fecha es inferior a 30 días, debe tomar medidas.

### Paso siguiente

| AutoCertificateRollover | Certificados sincronizados con Azure AD | Metadatos de federación disponibles públicamente | Validez | Acción |
|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|:-----------------------:|
| Sí | Sí | Sí | - | No es necesaria ninguna acción. Consulte [Renovación automática de certificados de firma de tokens](#autorenew) || Sí | No | - | Menos de 15 días | Renovar inmediatamente. Consulte [Renovación manual de certificados de firma de tokens](#manualrenew) | | No | - | - | Menos de 30 días | Renovar inmediatamente. Consulte [Renovación manual de certificados de firma de tokens](#manualrenew) |

[-] No importa

## Renovación automática de certificados de firma de tokens (recomendado) <a name="autorenew"></a>

Si ha implementado el Proxy de aplicación web para que pueda habilitar el acceso a los metadatos de federación desde la extranet y está utilizando la configuración predeterminada de AD FS, es decir, el proceso AutoCertificateRollover está habilitado, **no es necesario realizar ningún paso manual.** Compruebe lo siguiente para confirmar que puede tener lugar la actualización automática del certificado:

**Nº 1 La propiedad AutoCertificateRollover de AD FS debe establecerse en True**

Esto indica que AD FS generará automáticamente nuevos certificados de descifrado de tokens y de firma de tokens antes de que expiren los antiguos.

**Nº 2 Los metadatos de federación de AD FS están disponibles públicamente**

Compruebe que se puede obtener acceso públicamente a los metadatos de federación, desplácese hasta la siguiente dirección URL desde un equipo de la red de Internet pública (fuera de la red corporativa):


https://(your_FS_name)/federationmetadata/2007-06/federationmetadata.xml

donde `(your_FS_name) ` se reemplaza por el nombre de host de servicio de federación que usa su organización, por ejemplo, fs.contoso.com. Si es capaz de comprobar ambos de estos valores correctamente, no tiene que hacer nada más.

Ejemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovación manual de certificados de firma de tokens<a name="manualrenew"></a>

Puede optar por renovar manualmente los certificados de firma de tokens. Algunos de los escenarios comunes donde desea actualizar manualmente los certificados de firma de tokens son los siguientes:
* Los certificados de firma de tokens no son certificados autofirmados. La razón más común es que su organización administra certificados de AD FS inscritos de una entidad de certificación profesional. 
* La seguridad de red no permite que los metadatos de federación estén disponibles públicamente.

En estos escenarios, cada vez que actualice los certificados de firma de tokens, debe actualizar también el dominio de Office 365 con el comando de PowerShell Update-MsolFederatedDomain.

### Pasos para renovar el certificado de firma de token y actualizar la relación de confianza de federación de Office 365

**Paso 1: Asegurarse de que AD FS tiene nuevos certificados de firma de tokens**

### Configuración no predeterminada
Si está en una configuración no predeterminada de AD FS donde la propiedad **AutoCertificateRollover** está establecida en **False**, probablemente esté utilizando certificados personalizados (no autofirmados). Consulte la [guía para clientes que no usan certificados autofirmados AD FS](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert) para obtener instrucciones completas acerca de cómo renovar los certificados de firma de tokens de AD FS.

### Los metadatos de federación no están disponibles públicamente
Por otro lado si la propiedad **AutoCertificateRollover** está establecida en **True** pero los metadatos de federación no están disponibles públicamente, asegúrese primero de que los certificados de firma de tokens nuevos los ha generado AD FS. Siga los pasos siguientes para confirmar que dispone de nuevos certificados de firma de tokens.

1. Compruebe que la sesión en el servidor de AD FS principal está iniciada.
2. Compruebe los certificados de firma actuales en AD FS abriendo una ventana de comandos de PowerShell y ejecutando el siguiente comando:

	PS C:\>Get-ADFSCertificate –CertificateType token-signing

	>[AZURE.NOTE] Si usa AD FS 2.0, tendrá que ejecutar Add-Pssnapin Microsoft.Adfs.Powershell primero.

3. Examine la salida del comando en los certificados que se muestran. Si AD FS ha generado un nuevo certificado, debería ver dos certificados en la salida: uno para el que el valor de IsPrimary es True y la fecha de NotAfter está dentro de 5 días y uno para el que IsPrimary es False y NotAfter es aproximadamente un año en el futuro.

4. Si solo ve un certificado y la fecha de NotAfter está dentro de 5 días, deberá generar un nuevo certificado mediante la ejecución de los pasos siguientes.

5. Para generar un nuevo certificado, ejecute el siguiente comando en un símbolo del sistema de PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.

6. Compruebe la actualización ejecutando de nuevo el comando siguiente: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Ahora deben aparecer dos certificados, uno de los cuales tiene una fecha de NotAfter de aproximadamente un año en el futuro y para el que el valor de IsPrimary es False.

**Paso 2: Actualizar los nuevos certificados de firma de tokens para la relación de confianza de Office 365**

Siga los pasos indicados a continuación para actualizar Office 365 con los nuevos certificados de firma de token que se utilizarán para la relación de confianza.

1.	Abra el módulo Microsoft Azure Active Directory para Windows PowerShell.
2.	Ejecute $cred=Get-Credential. Cuando este cmdlet le solicite las credenciales, escriba sus credenciales de cuenta de administrador de servicios en la nube.
3.	Ejecute Connect-MsolService –Credential $cred. Este cmdlet le permite conectarse al servicio en la nube. Es necesario crear un contexto que le permita conectarse con el servicio en la nube antes de ejecutar cualquiera de los cmdlets adicionales que instala la herramienta.
4.	Si ejecuta estos comandos en un equipo que no sea el servidor de federación principal de AD FS, ejecute Set-MSOLAdfscontext -Computer <AD FS primary server>, donde <AD FS primary server> es el nombre de dominio completo interno del servidor de AD FS principal. Este cmdlet crea un contexto que le permite conectarse a AD FS.
5.	Ejecute Update-MSOLFederatedDomain –DomainName <domain>. Este cmdlet actualiza la configuración de AD FS en el servicio en la nube y configura la relación de confianza entre los dos.


>[AZURE.NOTE] Si necesita admitir varios dominios de nivel superior, por ejemplo, contoso.com y fabrikam.com, debe utilizar el modificador SupportMultipleDomain con cualquier cmdlet. Para más información, consulte [Support for Multiple Top Level Domains](active-directory-aadconnect-multiple-domains.md) (Compatibilidad con varios dominios de nivel superior).

## Reparar la relación de confianza de Azure AD con AAD Connect <a name="connectrenew"></a>

Si hubiera instalado y configurado la granja de servidores de AD FS o la relación de confianza de Azure AD con Azure AD Connect, puede usar Azure AD Connect para detectar si es necesario tomar alguna medida para los certificados de firma de tokens. Si necesita renovar los certificados, puede utilizar Azure AD Connect para hacer lo que precisa con unos pocos clic.

Para más información, consulte la sección [Reparación de la confianza](./active-directory-aadconnect-federation-management.md#repairing-the-trust).

<!---HONumber=AcomDC_0622_2016-->