---
title: Interfaz de usuario de Razor reutilizable en bibliotecas de clases con ASP.NET Core
author: Rick-Anderson
description: Explica cómo crear la interfaz de usuario Razor reutilizable con las vistas parciales en una biblioteca de clases en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 06/28/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: d59f643a23b48ccbddf498ef534ee8432b010f40
ms.sourcegitcommit: 6d9cf728465cdb0de1037633a8b7df9a8989cccb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67463253"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>Crear la interfaz de usuario reutilizable con el proyecto de biblioteca de clases de Razor en ASP.NET Core

Por [Rick Anderson](https://twitter.com/RickAndMSFT)

Las vistas de Razor, páginas, controladores, modelos de página, [componentes Razor](xref:blazor/class-libraries), [ver componentes](xref:mvc/views/view-components), y se pueden generar modelos de datos en una biblioteca de clases de Razor (RCL). Las RCL se pueden empaquetar y reutilizar. Las aplicaciones pueden incluir la RCL y reemplazar las vistas y páginas que contienen. Cuando existe una vista, vista parcial o página de Razor tanto en la aplicación web como en la RCL, tendrá prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web.

Esta característica requiere [!INCLUDE[](~/includes/2.1-SDK.md)]

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="create-a-class-library-containing-razor-ui"></a>Crear una biblioteca de clases que contenga interfaz de usuario de Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**.
* Asigne un nombre a la biblioteca (por ejemplo, "RazorClassLib") > **Aceptar**. Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.
* Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.
* Seleccione **Razor Class Library** (Biblioteca de clases de Razor) > **Aceptar**.

Un RCL tiene el siguiente archivo del proyecto:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Ejecute `dotnet new razorclasslib` desde la línea de comandos. Por ejemplo:

```console
dotnet new razorclasslib -o RazorUIClassLib
```

Para más información, vea [dotnet new](/dotnet/core/tools/dotnet-new). Para evitar un conflicto de nombres de archivo con la biblioteca de vistas generada, asegúrese de que el nombre de la biblioteca no acaba en `.Views`.

---

Agregue archivos de Razor a la RCL.

Las plantillas de ASP.NET Core se suponen que el contenido RCL está en el *áreas* carpeta. Consulte [diseño de páginas RCL](#afs) para crear contenido en un RCL que expone `~/Pages` lugar `~/Areas/Pages`.

## <a name="referencing-rcl-content"></a>Hacer referencia a contenido RCL

Los siguientes elementos pueden hacer referencia a la RCL:

* Paquete NuGet. Vea [Creación de paquetes NuGet](/nuget/create-packages/creating-a-package), [dotnet add package](/dotnet/core/tools/dotnet-add-package) y [Creación y publicación de un paquete NuGet](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).
* *{NombreDeProyecto}.csproj*. Vea [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>Tutorial: Cree un proyecto RCL y use desde un proyecto de páginas de Razor

En lugar de crearlo, puede descargar el [proyecto completo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) y comprobarlo. La descarga de ejemplo contiene más código y vínculos que hacen que el proyecto sea fácil de comprobar. Puede dejar sus comentarios en [este problema de GitHub](https://github.com/aspnet/AspNetCore.Docs/issues/6098) sobre las descargas de ejemplo en comparación con las instrucciones paso a paso.

### <a name="test-the-download-app"></a>Comprobar la aplicación de descarga

Si no ha descargado la aplicación final y prefiere crear el proyecto de tutorial, omita este paso y vaya a la [siguiente sección](#create-an-rcl).

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Abra el archivo *.sln* en Visual Studio. Ejecute la aplicación.

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Desde un símbolo del sistema en el directorio *cli*, cree la RCL y la aplicación web.

```console
dotnet build
```

Vaya al directorio *WebApp1* y ejecute la aplicación:

```console
dotnet run
```

---

Siga las instrucciones de [Probar WebApp1](#test).

## <a name="create-an-rcl"></a>Crear un RCL

En esta sección, se crea un RCL. y se agregarán a ella archivos de Razor.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Cree el proyecto de RCL:

* En el menú **Archivo** de Visual Studio, seleccione **Nuevo** > **Proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**.
* Nombre de la aplicación **RazorUIClassLib** > **Aceptar**.
* Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.
* Seleccione **Razor Class Library** (Biblioteca de clases de Razor) > **Aceptar**.
* Agregue un archivo de vista parcial de Razor denominado *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Ejecute lo siguiente desde la línea de comandos:

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

Los comandos anteriores:

* Crea el `RazorUIClassLib` RCL.
* Crean una página _Message de Razor y la agrega a la RCL. El parámetro `-np` crea la página sin un `PageModel`.
* Crea un [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) de archivo y lo agrega a la RCL.

El *_ViewStart.cshtml* archivo es necesario para usar el diseño del proyecto de páginas de Razor (que se agrega en la sección siguiente).

---

### <a name="add-razor-files-and-folders-to-the-project"></a>Agregar archivos de Razor y carpetas al proyecto

* Reemplace el marcado de *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* por el siguiente código:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* Reemplace el marcado de *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* por el siguiente código:

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

Se necesita `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` para usar la vista parcial (`<partial name="_Message" />`). En lugar de incluir la directiva `@addTagHelper`, puede agregar un archivo *_ViewImports.cshtml*. Por ejemplo:

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

Para obtener más información sobre *_ViewImports.cshtml*, consulte [importar directivas compartidas](xref:mvc/views/layout#importing-shared-directives)

* Compile la biblioteca de clases para confirmar que no hay ningún error de compilador:

```console
dotnet build RazorUIClassLib
```

El resultado de la compilación contiene *RazorUIClassLib.dll* y *RazorUIClassLib.Views.dll*. *RazorUIClassLib.Views.dll* incluye el contenido de Razor compilado.

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>Usar la biblioteca de interfaz de usuario de Razor desde un proyecto de páginas de Razor

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Cree la aplicación web de páginas de Razor:

* En el **Explorador de soluciones**, haga clic con el botón derecho en la solución > **Agregar** >  **Nuevo proyecto**.
* Seleccione **Aplicación web de ASP.NET Core**.
* Denomine la aplicación **WebApp1**.
* Confirme que **ASP.NET Core 2.1** o una versión posterior está seleccionado.
* Seleccione **Aplicación web** > **Aceptar**.

* En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Establecer como proyecto de inicio**.
* En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Dependencias de compilación** > **Dependencias del proyecto**.
* Marque **RazorUIClassLib** como dependencia de **WebApp1**.
* En el **Explorador de soluciones**, haga clic con el botón derecho en **WebApp1** y seleccione **Agregar** > **Referencia**.
* En el cuadro de diálogo **Administrador de referencias**, marque **RazorUIClassLib** > **Aceptar**.

Ejecute la aplicación.

# <a name="net-core-clitabnetcore-cli"></a>[CLI de .NET Core](#tab/netcore-cli)

Crear una aplicación web de páginas de Razor y un archivo de solución que contiene la aplicación de las páginas de Razor y el RCL:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

Compile y ejecute la aplicación web:

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>Probar WebApp1

Compruebe que la biblioteca de clases de interfaz de usuario de Razor está en uso:

* Vaya a `/MyFeature/Page1`.

## <a name="override-views-partial-views-and-pages"></a>Reemplazar vistas, vistas parciales y páginas

Cuando existe una vista, vista parcial o página de Razor tanto en la aplicación web como en la RCL, tendrá prioridad el marcado de Razor (archivo *.cshtml*) de la aplicación web. Por ejemplo, agregar *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* a WebApp1, y Page1 en la WebApp1 tendrá prioridad sobre Page1 en la RCL.

En la descarga de ejemplo, cambie el nombre *WebApp1/Areas/MyFeature2* por *WebApp1/Areas/MyFeature* para comprobar la prioridad.

Copie la vista parcial *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* en *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*. Actualice el marcado para señalar la nueva ubicación. Compile y ejecute la aplicación para comprobar si se está usando la versión de la vista parcial de la aplicación.

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>Diseño de páginas RCL

Para hacer referencia RCL contenido como si formara parte de la aplicación web a *páginas* carpeta, cree el proyecto RCL con la siguiente estructura de archivos:

* *Páginas/RazorUIClassLib*
* *RazorUIClassLib/páginas/Shared*

Supongamos que *RazorUIClassLib, compartidos o páginas* contiene dos archivos parciales: *_Header.cshtml* y *_Footer.cshtml*. El `<partial>` se puede agregar etiquetas a *_Layout.cshtml* archivo:

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a>Crear un RCL con activos estáticos

Un RCL puede requerir recursos estáticos de complementaria que pueden hacer referencia a la aplicación de consumo de la RCL. ASP.NET Core permite crear RCLs que incluyen recursos estáticos que están disponibles para una aplicación que lo consume.

Para incluir recursos complementarios como parte de un RCL, cree un *wwwroot* carpeta en la biblioteca de clases e incluir los archivos necesarios en esa carpeta.

Cuando se empaqueta un RCL, companion en todos los recursos en el *wwwroot* carpeta se incluyen automáticamente en el paquete y se ponen a disposición para hacer referencia al paquete de aplicaciones.

### <a name="consume-content-from-a-referenced-rcl"></a>Consumir contenido de un RCL que se hace referencia

Los archivos incluidos en el *wwwroot* carpeta de la RCL están expuestas a la aplicación que lo consume en el prefijo `_content/{LIBRARY NAME}/`. `{LIBRARY NAME}` es el nombre del proyecto de biblioteca que se convierte en minúsculas con períodos (`.`) eliminado. Por ejemplo, una biblioteca denominada *Razor.Class.Lib* da como resultado una ruta de acceso a contenido estático en `_content/razorclasslib/`.

La aplicación que hace referencia a recursos estáticos proporcionados por la biblioteca con `<script>`, `<style>`, `<img>`y otras etiquetas HTML. Debe tener la aplicación consumidora [compatibilidad con archivos estáticos](xref:fundamentals/static-files) habilitado.

### <a name="multi-project-development-flow"></a>Flujo de desarrollo de varios proyectos

Cuando se ejecuta la aplicación de consumo:

* Los activos de la estancia RCL en sus carpetas originales. Los recursos no se mueven a la aplicación que lo consume.
* Cualquier cambio en el RCL *wwwroot* carpeta se refleja en la aplicación consumidora después de que se vuelve a generar el RCL y sin volver a generar la aplicación que lo consume.

Cuando se compila el RCL, se genera un manifiesto que describe las ubicaciones de recurso web estático. La aplicación que lee el manifiesto en tiempo de ejecución para consumir los recursos de los paquetes y proyectos que se hace referencia. Cuando se agrega un nuevo recurso a un RCL, se debe regenerar el RCL para actualizar su manifiesto antes de una aplicación que pueda acceder el nuevo recurso.

### <a name="publish"></a>Publicar

Cuando se publica la aplicación, los recursos complementarios de paquetes y proyectos de todos los que se hace referencia se copian en el *wwwroot* carpeta de la aplicación publicada en `_content/{LIBRARY NAME}/`.

::: moniker-end
