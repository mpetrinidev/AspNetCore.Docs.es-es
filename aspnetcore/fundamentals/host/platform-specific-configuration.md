---
title: Uso de ensamblados de inicio de hospedaje en ASP.NET Core
author: guardrex
description: Sepa cómo mejorar una aplicación ASP.NET Core desde un ensamblado externo con una implementación de IHostingStartup.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/06/2019
uid: fundamentals/configuration/platform-specific-configuration
ms.openlocfilehash: df078a2a2a50538a070bb0b49ff3853682cb17df
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889060"
---
# <a name="use-hosting-startup-assemblies-in-aspnet-core"></a>Uso de ensamblados de inicio de hospedaje en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex) y [Pavel Krymets](https://github.com/pakrym)

Una implementación de [IHostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup) (inicio de hospedaje) permite agregar mejoras a una aplicación al iniciarla a partir de un ensamblado externo. Por ejemplo, una biblioteca externa puede usar una implementación de inicio de hospedaje para suministrar más servicios o proveedores de configuración a una aplicación.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

## <a name="hostingstartup-attribute"></a>Atributo HostingStartup

Un atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) indica la presencia de un ensamblado de inicio de hospedaje para activar en tiempo de ejecución.

El ensamblado de entrada o el ensamblado que contiene la clase `Startup` se analiza automáticamente para detectar el atributo `HostingStartup`. La lista de ensamblados para buscar atributos `HostingStartup` se carga en tiempo de ejecución desde la configuración de [WebHostDefaults.HostingStartupAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupassemblieskey). La lista de ensamblados que se excluirán de la detección se carga desde [WebHostDefaults.HostingStartupExcludeAssembliesKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.hostingstartupexcludeassemblieskey). Para más información, consulte [Host web: Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies) y [Host web: ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).

En el ejemplo siguiente, el espacio de nombres del ensamblado de inicio de hospedaje es `StartupEnhancement`. La clase que contiene el código de inicio de hospedaje es `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Normalmente el atributo `HostingStartup` se encuentra en el archivo de clase de implementación `IHostingStartup` del ensamblado de inicio de hospedaje.

## <a name="discover-loaded-hosting-startup-assemblies"></a>Detectar los ensamblados de inicio de hospedaje cargados

Para detectar los ensamblados de inicio de hospedaje cargados, habilite el registro y consulte los registros de la aplicación. En ellos se registran los errores que se producen al cargar ensamblados. Los ensamblados de inicio de hospedaje se registran en el nivel de depuración y se registran todos los errores.

## <a name="disable-automatic-loading-of-hosting-startup-assemblies"></a>Deshabilitar la carga automática de ensamblados de inicio de hospedaje

Para deshabilitar la carga automática de los ensamblados de inicio de hospedaje, use uno de los enfoques siguientes:

* Para evitar que se carguen todos los ensamblados de inicio de hospedaje, establezca uno de los procedimientos siguientes en `true` o `1`:
  * La opción de configuración de hospedaje para [Evitar el inicio de hospedaje](xref:fundamentals/host/web-host#prevent-hosting-startup).
  * La variable de entorno `ASPNETCORE_PREVENTHOSTINGSTARTUP`.
* Para evitar la carga de ensamblados de inicio de hospedaje específicos, establezca una de las opciones siguientes en una cadena delimitada por punto y coma de ensamblados de inicio de hospedaje para excluir durante el inicio:
  * La opción de configuración de host para [Ensamblados de exclusión de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-exclude-assemblies).
  * La variable de entorno `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`.

Si se establecen la opción de configuración de host y la variable de entorno, la configuración de host controla el comportamiento.

Deshabilitar los ensamblados de inicio de hospedaje a través de la variable de entorno o de la configuración de host hace que el ensamblado se deshabilite globalmente y, asimismo, puede deshabilitar también otras características de una aplicación.

## <a name="project"></a>Proyecto

Cree un inicio de hospedaje con cualquiera de los siguientes tipos de proyecto:

* [Biblioteca de clases](#class-library)
* [Aplicación de consola sin un punto de entrada](#console-app-without-an-entry-point)

### <a name="class-library"></a>Biblioteca de clases

Una mejora de inicio de hospedaje se puede proporcionar en una biblioteca de clases. La biblioteca contiene un atributo `HostingStartup`.

El [código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) incluye una aplicación Razor Pages, *HostingStartupApp* y una biblioteca de clases, *HostingStartupLibrary*. La biblioteca de clases:

* Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación mediante el proveedor de configuración en memoria ([AddInMemoryCollection](/dotnet/api/microsoft.extensions.configuration.memoryconfigurationbuilderextensions.addinmemorycollection)).
* Incluye un atributo `HostingStartup` que identifica espacio de nombres y la clase del inicio de hospedaje.

El método [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) de la clase `ServiceKeyInjection` usa un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) para agregar mejoras a una aplicación.

*HostingStartupLibrary/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupLibrary/ServiceKeyInjection.cs?name=snippet1)]

La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje de la biblioteca de clases:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=5-6,11-12)]

El [código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) también incluye un proyecto de paquete NuGet que proporciona un inicio de hospedaje independiente, *HostingStartupPackage*. El paquete tiene las mismas características que la biblioteca de clases que se ha descrito anteriormente. El paquete:

* Contiene una clase de inicio de hospedaje, `ServiceKeyInjection`, que implementa `IHostingStartup`. `ServiceKeyInjection` agrega un par de cadenas de servicio a la configuración de la aplicación.
* Incluye un atributo `HostingStartup`.

*HostingStartupPackage/ServiceKeyInjection.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupPackage/ServiceKeyInjection.cs?name=snippet1)]

La página de índice de la aplicación lee y procesa los valores de configuración para las dos claves establecidas por el ensamblado de inicio de hospedaje del paquete:

*HostingStartupApp/Pages/Index.cshtml.cs*:

[!code-csharp[](platform-specific-configuration/samples/2.x/HostingStartupApp/Pages/Index.cshtml.cs?name=snippet1&highlight=7-8,13-14)]

### <a name="console-app-without-an-entry-point"></a>Aplicación de consola sin un punto de entrada

*Este enfoque solo está disponible para las aplicaciones de .NET Core, no de .NET Framework.*

Se puede proporcionar una mejora del inicio de hospedaje dinámico que no requiere una referencia de tiempo de compilación para la activación en una aplicación de consola sin un punto de entrada que contiene un atributo `HostingStartup`. Publicar la aplicación de consola genera un ensamblado de inicio de hospedaje que se puede consumir desde el almacén en tiempo de ejecución.

En este proceso se usa una aplicación de consola sin un punto de entrada porque:

* Se requiere un archivo de dependencias para consumir el inicio de hospedaje en el ensamblado de inicio de hospedaje. Un archivo de dependencias es un recurso de aplicación ejecutable que se genera mediante la publicación de una aplicación, no una biblioteca.
* Una biblioteca no se puede agregar directamente al [almacén de paquetes en tiempo de ejecución](/dotnet/core/deploying/runtime-store), lo que requiere un proyecto ejecutable que tenga como destino el tiempo de ejecución compartido.

En la creación de un inicio de hospedaje dinámico:

* Se crea un ensamblado de inicio de hospedaje desde la aplicación de consola sin un punto de entrada que:
  * Incluye una clase que contiene la implementación de `IHostingStartup`.
  * Incluye un atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) para identificar la clase de implementación `IHostingStartup`.
* La aplicación de consola se publica para obtener las dependencias del inicio de hospedaje. Una consecuencia de la publicación de la aplicación de consola es que las dependencias no usadas se cortan en el archivo de dependencias.
* El archivo de dependencias se modifica para establecer la ubicación en tiempo de ejecución del ensamblado de inicio de hospedaje.
* El ensamblado de inicio de hospedaje y su archivo de dependencias se colocan en el almacén de paquetes en tiempo de ejecución. Para detectar el ensamblado de inicio de hospedaje y su archivo de dependencias, se enumeran en un par de variables de entorno.

La aplicación de consola hace referencia al paquete [Microsoft.AspNetCore.Hosting.Abstractions](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.Abstractions/):

[!code-xml[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.csproj)]

Un atributo [HostingStartup](/dotnet/api/microsoft.aspnetcore.hosting.hostingstartupattribute) identifica una clase como una implementación de `IHostingStartup` para la carga y ejecución al compilar [IWebHost](/dotnet/api/microsoft.aspnetcore.hosting.iwebhost). En el siguiente ejemplo, el espacio de nombres es `StartupEnhancement` y la clase, `StartupEnhancementHostingStartup`:

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet1)]

Una clase implementa `IHostingStartup`. El método [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) de la clase usa un [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) para agregar mejoras a una aplicación. El tiempo de ejecución llama a `IHostingStartup.Configure` en el ensamblado de inicio del hospedaje antes de `Startup.Configure` en el código de usuario, lo que permite que el código de usuario sobrescriba cualquier configuración facilitada por el ensamblado de inicio del hospedaje.

[!code-csharp[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement.cs?name=snippet2&highlight=3,5)]

Al crear un proyecto de `IHostingStartup`, el archivo de dependencias (*\*.deps.json*) establece la ubicación de `runtime` del ensamblado en la carpeta *bin*:

[!code-json[](platform-specific-configuration/samples-snapshot/2.x/StartupEnhancement1.deps.json?range=2-13&highlight=8)]

Solo se muestra parte del archivo. El nombre del ensamblado en el ejemplo es `StartupEnhancement`.

## <a name="configuration-provided-by-the-hosting-startup"></a>Configuración proporcionada por el inicio de hospedaje

Existen dos enfoques para controlar la configuración según si quiere que la configuración del inicio de hospedaje tenga prioridad o si la configuración de la aplicación tenga prioridad:

1. Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> para cargar la configuración después de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación. La configuración del inicio de hospedaje tiene prioridad sobre la configuración de la aplicación con este enfoque.
1. Proporcione la configuración a la aplicación utilizando <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> para cargar la configuración antes de que se ejecuten los delegados <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureAppConfiguration*> de la aplicación. Los valores de configuración de la aplicación tienen prioridad a sobre los proporcionados por el inicio de hospedaje con este enfoque.

```csharp
public class ConfigurationInjection : IHostingStartup
{
    public void Configure(IWebHostBuilder builder)
    {
        Dictionary<string, string> dict;

        builder.ConfigureAppConfiguration(config =>
        {
            dict = new Dictionary<string, string>
            {
                {"ConfigurationKey1", 
                    "From IHostingStartup: Higher priority than the app's configuration."},
            };

            config.AddInMemoryCollection(dict);
        });

        dict = new Dictionary<string, string>
        {
            {"ConfigurationKey2", 
                "From IHostingStartup: Lower priority than the app's configuration."},
        };

        var builtConfig = new ConfigurationBuilder()
            .AddInMemoryCollection(dict)
            .Build();

        builder.UseConfiguration(builtConfig);
    }
}
```

## <a name="specify-the-hosting-startup-assembly"></a>Especificar el ensamblado de inicio de hospedaje

En el caso de un inicio de hospedaje proporcionado por una biblioteca de clase o una aplicación de consola, especifique el nombre del ensamblado de inicio de hospedaje en la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`. La variable de entorno es una lista de ensamblados delimitada por punto y coma.

Solo se examinan los ensamblados de inicio de hospedaje en busca del atributo `HostingStartup`. En la aplicación de ejemplo, *HostingStartupApp*, para descubrir los nuevos inicios de hospedaje descritos anteriormente, la variable de entorno se establece en el siguiente valor:

```
HostingStartupLibrary;HostingStartupPackage;StartupDiagnostics
```

Un ensamblado de inicio de hospedaje también se puede establecer mediante la configuración de host [Ensamblados de inicio de hospedaje](xref:fundamentals/host/web-host#hosting-startup-assemblies).

Cuando existen varios ensamblados de inicio del hospedaje, sus métodos [Configure](/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup.configure) se ejecutan en el orden en el que aparecen los ensamblados.

## <a name="activation"></a>Activación

Las opciones de activación del inicio de hospedaje son:

* [Almacén en tiempo de ejecución](#runtime-store) &ndash; la activación no requiere una referencia de tiempo de compilación para la activación. La aplicación de ejemplo coloca el ensamblado de inicio de hospedaje y los archivos de dependencias en una carpeta, *implementación*, para facilitar la implementación del inicio del hospedaje en un entorno de varios equipos. La carpeta *implementación* también incluye un script de PowerShell que crea o modifica las variables de entorno en el sistema de implementación para habilitar el inicio de hospedaje.
* Se requiere una referencia al tiempo de compilación para la activación
  * [Paquete NuGet](#nuget-package)
  * [Carpeta bin del proyecto](#project-bin-folder)

### <a name="runtime-store"></a>Almacén en tiempo de ejecución

La implementación de inicio de hospedaje se coloca en el [almacén en tiempo de ejecución](/dotnet/core/deploying/runtime-store). No es necesario que la aplicación mejorada haga ninguna referencia de tiempo de compilación al ensamblado.

Una vez compilado el inicio de hospedaje, se genera un almacén en tiempo de ejecución mediante el archivo del proyecto de manifiesto y el comando [dotnet store](/dotnet/core/tools/dotnet-store).

```console
dotnet store --manifest {MANIFEST FILE} --runtime {RUNTIME IDENTIFIER} --output {OUTPUT LOCATION} --skip-optimization
```

En la aplicación de ejemplo (proyecto *RuntimeStore*), se usa el siguiente comando:

``` console
dotnet store --manifest store.manifest.csproj --runtime win7-x64 --output ./deployment/store --skip-optimization
```

Para que el tiempo de ejecución detecte el almacén en tiempo de ejecución, la ubicación del almacén en tiempo de ejecución se agrega a la variable de entorno `DOTNET_SHARED_STORE`.

**Modificar y colocar el archivo de dependencias del inicio de hospedaje**

Para activar la mejora sin una referencia de paquete a la mejora, especifique dependencias adicionales del tiempo de ejecución en `additionalDeps`. `additionalDeps` permite:

* Ampliar el gráfico de la biblioteca de la aplicación al proporcionar un conjunto de archivos *\*.deps.json* adicionales para combinarlos con el propio archivo *\*.deps.json* de la aplicación durante el inicio.
* Permitir la detección y la carga del ensamblado de inicio de hospedaje.

El enfoque recomendado para generar el archivo de dependencias adicionales es:

 1. Ejecutar `dotnet publish` en el archivo de manifiesto del almacén en tiempo de ejecución al que se hace referencia en la sección anterior.
 1. Quitar la referencia del manifiesto de las bibliotecas y de la sección `runtime` del archivo *\*deps.json* resultante.

En el proyecto de ejemplo, la propiedad `store.manifest/1.0.0` se quita de las secciones `targets` y `libraries`:

```json
{
  "runtimeTarget": {
    "name": ".NETCoreApp,Version=v2.1",
    "signature": "4ea77c7b75ad1895ae1ea65e6ba2399010514f99"
  },
  "compilationOptions": {},
  "targets": {
    ".NETCoreApp,Version=v2.1": {
      "store.manifest/1.0.0": {
        "dependencies": {
          "StartupDiagnostics": "1.0.0"
        },
        "runtime": {
          "store.manifest.dll": {}
        }
      },
      "StartupDiagnostics/1.0.0": {
        "runtime": {
          "lib/netcoreapp2.1/StartupDiagnostics.dll": {
            "assemblyVersion": "1.0.0.0",
            "fileVersion": "1.0.0.0"
          }
        }
      }
    }
  },
  "libraries": {
    "store.manifest/1.0.0": {
      "type": "project",
      "serviceable": false,
      "sha512": ""
    },
    "StartupDiagnostics/1.0.0": {
      "type": "package",
      "serviceable": true,
      "sha512": "sha512-oiQr60vBQW7+nBTmgKLSldj06WNLRTdhOZpAdEbCuapoZ+M2DJH2uQbRLvFT8EGAAv4TAKzNtcztpx5YOgBXQQ==",
      "path": "startupdiagnostics/1.0.0",
      "hashPath": "startupdiagnostics.1.0.0.nupkg.sha512"
    }
  }
}
```

Coloque el archivo *\*.deps.json* en la siguiente ubicación:

```
{ADDITIONAL DEPENDENCIES PATH}/shared/{SHARED FRAMEWORK NAME}/{SHARED FRAMEWORK VERSION}/{ENHANCEMENT ASSEMBLY NAME}.deps.json
```

* `{ADDITIONAL DEPENDENCIES PATH}`: ubicación agregada a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.
* `{SHARED FRAMEWORK NAME}`: marco compartido necesario para este archivo de dependencias adicionales.
* `{SHARED FRAMEWORK VERSION}`: versión mínima del marco compartido.
* `{ENHANCEMENT ASSEMBLY NAME}`: nombre del ensamblado de la mejora.

En la aplicación de ejemplo (proyecto *RuntimeStore*), el archivo de dependencias adicionales se coloca en la siguiente ubicación:

```
additionalDeps/shared/Microsoft.AspNetCore.App/2.1.0/StartupDiagnostics.deps.json
```

Para que el tiempo de ejecución detecte la ubicación del almacén en tiempo de ejecución, la ubicación del archivo de dependencias adicionales se agrega a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.

En la aplicación de ejemplo (proyecto *RuntimeStore*), la creación del almacén en tiempo de ejecución y la generación del archivo de dependencias adicionales se realizan con un script de [PowerShell](/powershell/scripting/powershell-scripting).

Para obtener ejemplos de cómo establecer variables de entorno en distintos sistemas operativos, vea [Uso de varios entornos](xref:fundamentals/environments).

**Implementación**

Para facilitar la implementación de un inicio de hospedaje en un entorno de varios equipos, la aplicación de ejemplo crea una carpeta de *implementación* en el resultado publicado que contiene:

* El almacén en tiempo de ejecución de inicio de hospedaje.
* El archivo de dependencias del inicio de hospedaje.
* Un script de PowerShell que crea o modifica `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`, `DOTNET_SHARED_STORE` y `DOTNET_ADDITIONAL_DEPS` para admitir la activación del inicio del hospedaje. Ejecute el script desde un símbolo del sistema de PowerShell administrativo en el sistema de implementación.

### <a name="nuget-package"></a>Detección de

Una mejora de inicio de hospedaje se puede proporcionar en un paquete NuGet. El paquete tiene un atributo `HostingStartup`. Los tipos de inicio de hospedaje proporcionados por el paquete están disponibles en la aplicación mediante cualquiera de los métodos siguientes:

* El archivo de proyecto de la aplicación mejorada hace una referencia al paquete para el inicio de hospedaje en el archivo de proyecto de la aplicación (una referencia de tiempo de compilación). Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación (*\*. deps.json*). Este enfoque se aplica a un paquete de ensamblado de inicio de hospedaje publicado en [nuget.org](https://www.nuget.org/).
* El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).

Para obtener más información sobre los paquetes NuGet y el almacén en tiempo de ejecución, vea los temas siguientes:

* [Cómo crear un paquete NuGet con herramientas multiplataforma](/dotnet/core/deploying/creating-nuget-packages)
* [Publicar paquetes](/nuget/create-packages/publish-a-package)
* [Runtime package store](/dotnet/core/deploying/runtime-store) (Almacenamiento de paquetes en tiempo de ejecución)

### <a name="project-bin-folder"></a>Carpeta bin del proyecto

Un ensamblado implementado por *bin* puede proporcionar una mejora del inicio de hospedaje en la aplicación mejorada. Los tipos de inicio de hospedaje proporcionados por el ensamblado están disponibles en la aplicación mediante cualquiera de los métodos siguientes:

* El archivo de proyecto de la aplicación mejorada hace referencia de ensamblado al inicio de hospedaje (una referencia de tiempo de compilación). Con la referencia de tiempo de compilación realizada, el ensamblado de inicio de hospedaje y todas sus dependencias se incorporan en el archivo de dependencia de la aplicación (*\*. deps.json*). Este enfoque se aplica cuando el escenario de implementación llama para mover el ensamblado de la biblioteca de inicio de hospedaje compilado (archivo DLL) al proyecto de consumo o a una ubicación a la que el proyecto de consumo pueda acceder y se realiza una referencia de tiempo de compilación en el ensamblado del inicio de hospedaje.
* El archivo de dependencias del inicio de hospedaje está disponible para la aplicación mejorada como se describe en la sección [Almacén en tiempo de ejecución](#runtime-store) (sin una referencia de tiempo de compilación).

## <a name="sample-code"></a>Código de ejemplo

En el [código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/platform-specific-configuration/samples/) ([cómo descargar](xref:index#how-to-download-a-sample)) se muestran escenarios de implementación de inicio de hospedaje:

* Cada uno de los dos ensamblados de inicio de hospedaje (bibliotecas de clases) establece un par clave-valor de configuración en memoria:
  * Paquete NuGet (*HostingStartupPackage*)
  * Biblioteca de clases (*HostingStartupLibrary*)
* Se activa un inicio de hospedaje desde un ensamblado implementado por el almacén de tiempo de ejecución (*StartupDiagnostics*). Este ensamblado agrega dos middleware a la aplicación (mientras se inicia) que proporcionan información de diagnóstico en:
  * Servicios registrados
  * Dirección (esquema, host, ruta de acceso base, ruta de acceso, cadena de consulta)
  * Conexión (dirección IP remota, puerto remoto, dirección IP local, puerto local, certificado de cliente)
  * Encabezados de solicitud
  * Variables de entorno

Para ejecutar el ejemplo:

**Activación desde un paquete NuGet**

1. Compile el paquete *HostingStartupPackage* con el comando [dotnet pack](/dotnet/core/tools/dotnet-pack).
1. Agregue el nombre de ensamblado del paquete de *HostingStartupPackage* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Compile y ejecute la aplicación. Una referencia de paquete está presente en la aplicación mejorada (una referencia de tiempo de compilación). El parámetro `<PropertyGroup>` del archivo del proyecto de la aplicación especifica el resultado del proyecto de paquete (*../HostingStartupPackage/bin/Debug*) como origen del paquete. Esto permite que la aplicación utilice el paquete sin cargar el paquete a [nuget.org](https://www.nuget.org/). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.

   ```xml
   <PropertyGroup>
     <RestoreSources>$(RestoreSources);https://api.nuget.org/v3/index.json;../HostingStartupPackage/bin/Debug</RestoreSources>
   </PropertyGroup>
   ```

1. Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por método `ServiceKeyInjection.Configure` del paquete.

Si realiza cambios en el proyecto *HostingStartupPackage* y lo vuelve a compilar, borre las cachés del paquete NuGet local para asegurarse de que *HostingStartupApp* recibe el paquete actualizado y no un paquete en desuso de la caché local. Para borrar las cachés de NuGet locales, ejecute el siguiente comando [dotnet nuget locals](/dotnet/core/tools/dotnet-nuget-locals):

```console
dotnet nuget locals all --clear
```

**Activación desde una biblioteca de clases**

1. Compile la biblioteca de clases *HostingStartupLibrary* con el comando [dotnet build](/dotnet/core/tools/dotnet-build).
1. Agregue el nombre del ensamblado de la biblioteca de clases *HostingStartupLibrary* a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
1. Implemente con *bin*-el ensamblado de la biblioteca de clases en la aplicación copiando el archivo *HostingStartupLibrary.dll* desde el resultado compilado de la biblioteca de aplicaciones en la carpeta *bin/Debug* de la aplicación.
1. Compile y ejecute la aplicación. Un parámetro `<ItemGroup>` del archivo del proyecto de la aplicación hace referencia al ensamblado de la biblioteca de clases (*.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll*) (una referencia de tiempo de compilación). Para obtener más información, vea las notas que encontrará en el archivo del proyecto de HostingStartupApp.

   ```xml
   <ItemGroup>
     <Reference Include=".\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll">
       <HintPath>.\bin\Debug\netcoreapp2.1\HostingStartupLibrary.dll</HintPath>
       <SpecificVersion>False</SpecificVersion>
     </Reference>
   </ItemGroup>
   ```

1. Observe que los valores de la clave de configuración del servicio representados por la página de índice coinciden con los valores establecidos por el método `ServiceKeyInjection.Configure` de la biblioteca de clases.

**Activación desde un ensamblado implementado por el almacén de tiempo de ejecución**

1. El proyecto *StartupDiagnostics* usa [PowerShell](/powershell/scripting/powershell-scripting) para modificar el archivo *StartupDiagnostics.deps.json*. PowerShell se instala de forma predeterminada en Windows a partir de Windows 7 SP1 y Windows Server 2008 R2 SP1. Para obtener PowerShell en otras plataformas, vea [Instalación de Windows PowerShell](/powershell/scripting/setup/installing-powershell#powershell-core).
1. Ejecute el script *build.ps1* en la carpeta *RuntimeStore*. El script:
   * Genera el paquete `StartupDiagnostics`.
   * Genera el almacén en tiempo de ejecución para `StartupDiagnostics` en la carpeta *store*. El comando `dotnet store` del script usa el [identificador de runtime (RID)](/dotnet/core/rid-catalog) `win7-x64` para un inicio de hospedaje implementado en Windows. Para proporcionar el inicio de hospedaje para otro tiempo de ejecución, sustituya el RID correcto en la línea 37 del script.
   * Genera `additionalDeps` para `StartupDiagnostics` en la carpeta *additionalDeps/shared/Microsoft.AspNetCore.App/{Shared Framework Version}/*.
   * Coloca el archivo *deploy.ps1* en la carpeta *deployment*.
1. Ejecute el script *deploy.ps1* en la carpeta *deployment*. El script anexa:
   * `StartupDiagnostics` a la variable de entorno `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`.
   * La ruta de acceso de las dependencias del inicio de hospedaje a la variable de entorno `DOTNET_ADDITIONAL_DEPS`.
   * La ruta de acceso del almacén en tiempo de ejecución a la variable de entorno `DOTNET_SHARED_STORE`.
1. Ejecute la aplicación de ejemplo.
1. Solicite al punto de conexión `/services` ver los servicios registrados de la aplicación. Solicite al punto de conexión `/diag` ver la información de diagnóstico.
