---
title: Introducción a las cuentas de usuario en Azure Active Directory B2C | Microsoft Docs
description: Obtenga información sobre las cuentas de usuario en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0ef4bca3a2b08271b3623a346df6613b5ad16995
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063138"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>Introducción a las cuentas de usuario en Azure Active Directory B2C

En Azure Active Directory B2C (Azure AD B2C), puede usar diferentes tipos de cuentas. Azure Active Directory, Azure Active Directory B2B y Azure Active Directory B2C comparten los tipos de cuentas de usuario que se pueden usar.

Están disponibles los siguientes tipos de cuentas:

- **Cuenta profesional**: una cuenta profesional puede tener acceso a los recursos en un inquilino y, con un rol de administrador, puede administrar inquilinos.
- **Cuenta de invitado**: una cuenta de invitado solo puede ser una cuenta de Microsoft o un usuario de Azure Active Directory que se pueda utilizar para tener acceso a aplicaciones o administrar inquilinos.
- **Cuenta de consumidor**: una cuenta de consumidor se crea mediante un flujo de usuario de registro en una aplicación Azure AD B2C o el uso de Azure AD Graph API y la usan los usuarios de las aplicaciones registrados con Azure AD B2C.

## <a name="work-account"></a>Cuenta profesional

Una cuenta profesional se crea de la misma manera para todos los inquilinos basada en Azure AD. Para crear una cuenta profesional, puede usar la información de [Inicio rápido: Incorporación de nuevos usuarios a Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). Una nueva cuenta profesional se crea mediante la elección de **Nuevo usuario** en Azure Portal.

Cuando se agrega una nueva cuenta profesional, es preciso tener en cuenta las siguientes opciones de configuración:

- **Nombre** y **Nombre de usuario**: la propiedad **Nombre** contiene el nombre y el apellido del usuario. El **Nombre de usuario** es el identificador que escribe el usuario para iniciar sesión. El nombre de usuario incluye el dominio completo. La parte del nombre de dominio del nombre de usuario debe ser el nombre de dominio predeterminado inicial *su-dominio.onmicrosoft.com* o un nombre de [dominio personalizado](../active-directory/fundamentals/add-custom-domain.md) comprobado y no federado, como *contoso.com*.
- **Perfil**: la cuenta está configurada con un perfil de datos de usuario. Tiene la oportunidad de especificar un nombre, los apellidos, el puesto y el nombre de departamento. Puede editar el perfil después de crear la cuenta.
- **Grupos**: use un grupo para realizar tareas de administración, como asignar licencias o permisos a varios usuarios o dispositivos a la vez. Puede colocar la nueva cuenta en un [grupo](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) existente en su inquilino.
- **Rol de directorio**: debe especificar el nivel de acceso que tiene la cuenta de usuario a los recursos en el inquilino. Están disponibles los siguientes niveles de permiso:

    - **Usuario**: los usuarios pueden acceder a los recursos asignados, pero no pueden administrar la mayoría de los recursos de inquilino.
    - **Administrador global**: los administradores globales tienen el control completo de todos los recursos de inquilino.
    - **Administrador limitado**: seleccione el rol administrativo o los roles para el usuario. Para obtener más información acerca de los roles que se pueden seleccionar, consulte [Asignación de roles de administrador en Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

### <a name="create-a-work-account"></a>Crear una cuenta profesional

Puede usar la siguiente información para crear una nueva cuenta profesional:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)

### <a name="update-a-user-profile"></a>Actualizar un perfil de usuario

Puede usar la siguiente información para actualizar el perfil de un usuario:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

### <a name="reset-a-password-for-a-user"></a>Restablecer la contraseña de un usuario

Puede usar la siguiente información para restablecer la contraseña de un usuario:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0)

## <a name="guest-user"></a>Usuario invitado

Puede invitar a usuarios externos al inquilino como usuario invitado. Un escenario típico para invitar a un usuario invitado al inquilino de Azure AD B2C es compartir las responsabilidades de administración. Para obtener un ejemplo del uso de una cuenta de invitado, consulte [Propiedades de un usuario de colaboración B2B de Azure Active Directory](../active-directory/b2b/user-properties.md).

Cuando se invita a un usuario invitado al inquilino, se proporciona la dirección de correo electrónico del destinatario y un mensaje que describe la invitación. El vínculo de invitación lleva al usuario a la página de consentimiento donde el botón **Empezar** está seleccionado y se acepta la revisión de permisos. Si no hay una bandeja de entrada asociada a la dirección de correo electrónico, el usuario puede navegar a la página de consentimiento, para ello debe ir a una página de Microsoft con las credenciales de invitación. A continuación, se obliga al usuario a canjear la invitación del mismo modo que cuando hace clic en el vínculo del correo electrónico. Por ejemplo: `https://myapps.microsoft.com/B2CTENANTNAME`.

También puede usar [Microsoft Graph API](https://docs.microsoft.com/graph/api/invitation-post?view=graph-rest-beta) para invitar a un usuario invitado.

## <a name="consumer-user"></a>Usuario consumidor

El usuario consumidor puede iniciar sesión en aplicaciones protegidas por Azure AD B2C, pero no puede acceder a recursos de Azure como Azure Portal.  El usuario consumidor puede utilizar una cuenta local o cuentas federadas, como Facebook o Twitter. Una cuenta de consumidor se crea mediante el uso de un [flujo de usuario de registro o de inicio de sesión](../active-directory-b2c/active-directory-b2c-reference-policies.md).

Puede especificar los datos que se recopilan cuando se crea una cuenta de usuario consumidor mediante el uso de atributos personalizados de usuario. Para obtener más información, vea [Definición de atributos personalizados en Azure Active Directory B2C](../active-directory-b2c/active-directory-b2c-reference-custom-attr.md).

Puede usar la información de la sección **Creación de cuentas de usuario consumidor** de [Uso de Graph API de Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) para crear una cuenta de consumidor de Azure AD B2C. También puede usar la información de la sección **Actualización de cuentas de usuario consumidor** en el mismo artículo para administrar las propiedades de la cuenta.

### <a name="migrate-consumer-user-accounts"></a>Migrar cuentas de usuario consumidor

En este artículo se explica cómo migrar las cuentas de usuario consumidor existentes desde cualquier proveedor de identidades a Azure AD B2C. Para obtener más información, consulte [Migración de usuarios](active-directory-b2c-user-migration.md) o [Migración de usuarios con identidades de redes sociales](active-directory-b2c-social-migration.md).
