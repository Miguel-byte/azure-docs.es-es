---
title: 'Configuración de la información de seguridad para usar una clave de seguridad (versión preliminar): Azure Active Directory | Microsoft Docs'
description: Configuración de su información de seguridad para comprobar su identidad con una clave de seguridad Fast Identity Online (FIDO2).
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25ac894db42b2b5ee2612e2e3b2906bc2bffcc4c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717383"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>Configuración de la información de seguridad para usar una clave de seguridad (versión preliminar)

Puede usar las claves de seguridad como un método de inicio de sesión sin contraseña dentro de su organización. Una clave de seguridad es un dispositivo físico que se usa con un PIN único para iniciar sesión en su cuenta profesional o educativa. Dado que las claves de seguridad requieren el dispositivo físico y algo que solo usted sepa, se considera un método de autenticación más seguro que un nombre de usuario y una contraseña.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>Este contenido está diseñado para usuarios. Si es un administrador, puede encontrar más información acerca de cómo configurar y administrar su entorno de Azure Active Directory (Azure AD) en la [documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory).

## <a name="what-is-a-security-key"></a>¿Qué es una clave de seguridad?

Actualmente, se admiten varios diseños y proveedores de claves de seguridad con los protocolos de autenticación sin contraseña de [Fast Identity online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2). Estas claves le permiten iniciar sesión en su cuenta profesional o educativa para acceder a todos los recursos basados en la nube de su organización cuando se encuentra en un dispositivo o explorador web admitido.

Su administrador u organización le proporcionará una clave de seguridad si lo requiere para su cuenta profesional o educativa. Hay diferentes tipos de claves de seguridad que puede usar; por ejemplo, una llave USB que conecta al dispositivo o una llave NFC que puede acercar a un lector NFC. Puede obtener más información acerca de la clave de seguridad, incluido el tipo, en la documentación del fabricante.

> [!Note]
> Si no puede usar una clave de seguridad FIDO2, hay otros métodos de autenticación sin contraseña que puede usar, como la aplicación Microsoft Authenticator o Windows Hello. Para obtener más información sobre la aplicación Microsoft Authenticator, consulte [Introducción a la aplicación Microsoft Authenticator](user-help-auth-app-overview.md). Para obtener más información acerca de Windows Hello, consulte [Introducción a Windows Hello](https://www.microsoft.com/windows/windows-hello).

## <a name="before-you-begin"></a>Antes de empezar

Antes de poder registrar la clave de seguridad, debe cumplirse lo siguiente:

- El administrador ha activado esta característica para su uso dentro de la organización.

- Está en un dispositivo que ejecuta la actualización de Windows 10 de mayo de 2019 y usa un explorador compatible.

- Dispone de una clave de seguridad física que su administrador u organización ha aprobado. La clave de seguridad debe ser FIDO2 y compatible con Microsoft. Si tiene alguna pregunta sobre la clave de seguridad y sobre si es compatible, póngase en contacto con el departamento de soporte técnico de su organización.

## <a name="register-your-security-key"></a>Registro de la clave de seguridad

Debe crear la clave de seguridad y asignarle un PIN único para poder iniciar sesión en su cuenta profesional o educativa con la clave. Puede tener hasta 10 claves registradas en su cuenta. 

1. Vaya a la página **Mi perfil** en https://myprofile.microsoft.com e inicie sesión si todavía no lo ha hecho.

2. Seleccione **Información de seguridad**, **Agregar método** y, a continuación, **Clave de seguridad** en la lista desplegable **Agregar un método**.

    ![Cuadro Agregar método, con Clave de seguridad seleccionada](media/security-info/security-info-security-key-add-method.png)

3. Seleccione **Agregar** y, a continuación, seleccione el tipo de clave de seguridad que tiene, ya sea un **dispositivo USB** o **un dispositivo NFC**.

    ![Elija si tiene un tipo de clave de seguridad por USB o NFC](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > Si no está seguro de qué tipo de clave de seguridad tiene, consulte la documentación del fabricante. Si no está seguro del fabricante, póngase en contacto con el departamento de soporte técnico de su organización para obtener ayuda.

4. Haga que la clave de seguridad esté disponible físicamente y, a continuación, en el cuadro **Clave de seguridad**, seleccione **Siguiente**.

    ![Cuadro de registro de inicio de clave de seguridad](media/security-info/security-info-security-key-start-setup.png)

    Aparece un nuevo cuadro que le guiará a través de la configuración del nuevo método de inicio de sesión.

5. En el cuadro **Configuración del nuevo método de inicio de sesión**, seleccione **Siguiente** y, a continuación:

    - Si la clave de seguridad es un dispositivo USB, inserte la clave de seguridad en el puerto USB del dispositivo.

    - Si la clave de seguridad es un dispositivo NFC, acerque la clave de seguridad al lector.

6. Escriba el PIN único de la clave de seguridad en el cuadro **Seguridad de Windows** y, a continuación, seleccione **Aceptar**.

    Volverá al cuadro **Configuración del nuevo método de inicio de sesión**.

7. Seleccione **Next** (Siguiente).

8. Vuelva a la página **Información de seguridad**, escriba un nombre que pueda reconocer más tarde para la nueva clave de seguridad y, a continuación, seleccione **Siguiente**.

    ![Página de información de seguridad, nombrar la clave de seguridad](media/security-info/security-info-security-key-name.png)

    La clave de seguridad está registrada y lista para que la use para iniciar sesión en su cuenta profesional o educativa.

9. Seleccione **Listo** para cerrar el cuadro **Clave de seguridad**.

    La página **Información de seguridad** se actualiza con la información de la clave de seguridad.

    ![Página Información de seguridad, con todos los métodos registrados mostrados](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>Eliminación de una clave de seguridad de la información de seguridad

Si pierde o ya no quiere usar la clave de seguridad, puede eliminarla de la información de seguridad. Aunque esto impide que se use la clave de seguridad con su cuenta profesional o educativa, la clave de seguridad continúa almacenando los datos y la información de las credenciales. Para eliminar los datos y la información de credenciales de la clave de seguridad, debe seguir las instrucciones de la sección [Restablecimiento de una clave de seguridad compatible con Microsoft](#reset-your-security-key) de este artículo.

1. Seleccione el vínculo **Eliminar** de la clave de seguridad para quitarla.

2. Seleccione **Aceptar** en el cuadro **Eliminar clave de seguridad**.

    La clave de seguridad se eliminará y ya no podrá usarla para iniciar sesión en su cuenta profesional o educativa.

>[!Important]
>Si ha eliminado una clave de seguridad por error, puede volver a registrarla. Para ello, siga las instrucciones de la sección [Cómo registrar la clave de seguridad](#register-your-security-key) de este artículo.

## <a name="manage-your-security-key-settings-from-windows-settings"></a>Administración de la configuración de la clave de seguridad desde Configuración de Windows

Puede administrar la configuración de la clave de seguridad desde la aplicación **Configuración de Windows**, incluido el restablecimiento de la clave de seguridad y la creación de un nuevo PIN de clave de seguridad.

### <a name="reset-your-security-key"></a>Restablecimiento de la clave de seguridad

Si quiere eliminar toda la información de la cuenta almacenada en la clave de seguridad física, debe devolver la clave a sus valores predeterminados de fábrica. Al restablecer la clave de seguridad, se elimina todo el contenido de la clave, lo que le permite comenzar de nuevo.

>[!IMPORTANT]
>Al restablecer la clave de seguridad, se elimina todo el contenido de la clave, lo que la restablece a sus valores predeterminados de fábrica.
>
> **Se borrarán todos los datos y credenciales.**

#### <a name="to-reset-your-security-key"></a>Para restablecer la clave de seguridad

1. Abra la aplicación Configuración de Windows, seleccione **Cuentas**, **Opciones de inicio de sesión**, **Clave de seguridad** y, a continuación, **Administrar**.

2. Inserte la clave de seguridad en el puerto USB o acérquela al lector NFC para comprobar su identidad.

3. Siga las instrucciones que aparecen en pantalla, en función del fabricante de su clave de seguridad específica. Si el fabricante de la clave no aparece en las instrucciones de la pantalla, consulte el sitio del fabricante para obtener más información.

4. Seleccione **Cerrar** para cerrar la pantalla **Administrar**.

### <a name="create-a-new-security-key-pin"></a>Creación de un nuevo PIN de clave de seguridad

Puede crear un nuevo PIN de clave de seguridad para su clave de seguridad.

#### <a name="to-create-a-new-security-key-pin"></a>Para crear un nuevo PIN de clave de seguridad

1. Abra la aplicación Configuración de Windows, seleccione **Cuentas**, **Opciones de inicio de sesión**, **Clave de seguridad** y, a continuación, **Administrar**.

2. Inserte la clave de seguridad en el puerto USB o acérquela al lector NFC para comprobar su identidad.
3. Seleccione **Agregar** en el área **PIN de clave de seguridad**, escriba y confirme el nuevo PIN de seguridad y, a continuación, seleccione **Aceptar**.

     La clave de seguridad se actualiza con el nuevo PIN de clave de seguridad para su uso con su cuenta profesional o educativa. Si decide volver a cambiar el PIN, puede seleccionar el botón **Cambiar**.
4. Seleccione **Cerrar** para cerrar la pantalla **Administrar**.

## <a name="additional-security-info-methods"></a>Métodos de información de seguridad adicionales

Para registrar una clave de seguridad, debe tener al menos un método de comprobación de seguridad adicional registrado. Para más información, consulte la [sección Información general](security-info-add-update-methods-overview.md). 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre los métodos de autenticación sin contraseña, lea la entrada del blog [Azure AD de Microsoft inicia la versión preliminar pública de claves de seguridad FIDO2, lo que permite los inicios de sesión sin contraseña](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins), o consulte los artículos [Introducción a la aplicación Microsoft Authenticator](user-help-auth-app-overview.md) e [Introducción a Windows Hello](https://www.microsoft.com/windows/windows-hello).

- Para obtener información más detallada sobre las [claves de seguridad compatibles con Microsoft](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key).

- Restablezca la contraseña si ha perdido u olvidado la que tenía acudiendo al [portal de restablecimiento de contraseña](https://passwordreset.microsoftonline.com/) o siga los pasos descritos en el artículo [Restablecimiento de la contraseña profesional o educativa](user-help-reset-password.md).

- Obtenga soluciones de problemas, sugerencias y ayuda para los problemas de inicio de sesión en el artículo [Cuando no puedes iniciar sesión en tu cuenta de Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
