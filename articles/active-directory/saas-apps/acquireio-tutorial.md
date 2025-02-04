---
title: 'Tutorial: Integración de Azure Active Directory con AcquireIO | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75a92e78e7293316cad6e567ae49c4998299415c
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544566"
---
# <a name="tutorial-integrate-acquireio-with-azure-active-directory"></a>Tutorial: Integración de AcquireIO con Azure Active Directory

En este tutorial, obtendrá información sobre cómo integrar AcquireIO con Azure Active Directory (Azure AD). Al integrar AcquireIO con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AcquireIO.
* Permitir que los usuarios puedan iniciar sesión automáticamente en AcquireIO con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AcquireIO.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. AcquireIO admite el inicio de sesión único iniciado por **IDP**.

## <a name="adding-acquireio-from-the-gallery"></a>Adición de AcquireIO desde la galería

Para configurar la integración de AcquireIO en Azure AD, deberá agregar AcquireIO desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **AcquireIO** en el cuadro de búsqueda.
1. Seleccione **AcquireIO** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

Configure y pruebe el inicio de sesión único de Azure AD con AcquireIO mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de AcquireIO.

Para configurar y probar el inicio de sesión único de Azure AD con AcquireIO, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
2. **[Configuración de AcquireIO](#configure-acquireio)** , para configurar el inicio de sesión único en la aplicación.
3. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B. Simon para que use el inicio de sesión único de Azure AD.
5. **[Creación de un usuario de prueba de AcquireIO](#create-acquireio-test-user)** , para tener un homólogo de Britta Simon en AcquireIO que esté vinculado a la representación del usuario en Azure AD.
6. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **AcquireIO**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, siga estos pasos:

    En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > Este valor no es real. Obtendrá la dirección URL de respuesta real, que se explica más adelante en la sección **Configuración de AcquireIO** del tutorial. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

   ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Set up AcquireIO** (Configurar AcquireIO), copie las direcciones URL adecuadas según sus necesidades.

   ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="configure-acquireio"></a>Configuración de AcquireIO

1. En otra ventana del explorador web, inicie sesión en AcquireIO como administrador.

2. En el lado izquierdo del menú, haga clic en **App Store**.

     ![Configuración de AcquireIO](./media/acquireio-tutorial/config01.png)

3. Desplácese hacia abajo hasta **Active Directory** y haga clic en **Instalar**.

    ![Configuración de AcquireIO](./media/acquireio-tutorial/config02.png)

4. En el elemento emergente de Active Directory, realice los siguientes pasos:

    ![Configuración de AcquireIO](./media/acquireio-tutorial/config03.png)

    a. Haga clic en **Copiar** para copiar la dirección URL de respuesta de la instancia y péguela en el cuadro de texto **URL de respuesta** de la sección **Configuración básica de SAML** de Azure Portal.

    b. En el cuadro de texto **Login URL** (Dirección URL de inicio de sesión), pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    c. Abra el certificado codificado en Base64 con el Bloc de notas, copie su contenido y péguelo en el cuadro de texto **Certificado X.509**.

    d. Haga clic en **Conectar ahora**.

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B. Simon acceda a AcquireIO utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **AcquireIO**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="create-acquireio-test-user"></a>Creación de un usuario de prueba de AcquireIO

Para permitir que los usuarios de Azure AD inicien sesión en AcquireIO, deben aprovisionarse en AcquireIO. En AcquireIO, el aprovisionamiento es una tarea manual.

**Para aprovisionar una cuenta de usuario, realice estos pasos:**

1. En otra ventana del explorador web, inicie sesión en AcquireIO como administrador.

2. En el lado izquierdo del menú, haga clic en **Perfiles** y vaya a **Agregar perfil**.

     ![Configuración de AcquireIO](./media/acquireio-tutorial/config04.png)

3. En el elemento emergente **Agregar cliente**, lleve a cabo los pasos siguientes:

    ![Configuración de AcquireIO](./media/acquireio-tutorial/config05.png)

    a. En el cuadro de texto **Nombre**, escriba el nombre de usuario, por ejemplo, **B.simon**.

    b. En el cuadro de texto **E-mail** (Correo electrónico), escriba el correo electrónico del usuario; por ejemplo, **B.simon@contoso.com** .

    c. Haga clic en **Enviar**.

### <a name="test-sso"></a>Prueba de SSO

Al seleccionar el icono de AcquireIO en el panel de acceso, debería iniciar sesión automáticamente en la versión de AcquireIO para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)