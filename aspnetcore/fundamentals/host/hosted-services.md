---
title: Tareas en segundo plano con servicios hospedados en ASP.NET Core
author: guardrex
description: Obtenga información sobre cómo implementar tareas en segundo plano con servicios hospedados en ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 2dbb1a84a380ab06a4be7ecf628799a070afc9e3
ms.sourcegitcommit: 5dd2ce9709c9e41142771e652d1a4bd0b5248cec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692517"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Tareas en segundo plano con servicios hospedados en ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

En ASP.NET Core, las tareas en segundo plano se pueden implementar como *servicios hospedados*. Un servicio hospedado es una clase con lógica de tarea en segundo plano que implementa la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>. En este tema se incluyen tres ejemplos de servicio hospedado:

* Una tarea en segundo plano que se ejecuta según un temporizador.
* Un servicio hospedado que activa un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes). El servicio con ámbito puede usar la inserción de dependencias.
* Tareas en segundo plano en cola que se ejecutan en secuencia.

[Vea o descargue el código de ejemplo](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([cómo descargarlo](xref:index#how-to-download-a-sample))

La aplicación de ejemplo se ofrece en dos versiones:

* Host web: el host web resulta útil para hospedar aplicaciones web. El código de ejemplo que se muestra en este tema corresponde a la versión de host web del ejemplo. Para más información, vea el sitio web [Host de web](xref:fundamentals/host/web-host).
* Host genérico: el host genérico es nuevo en ASP.NET Core 2.1. Para más información, vea el sitio web [Host genérico](xref:fundamentals/host/generic-host).

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a>Plantilla Worker Service

La plantilla Worker Service de ASP.NET Core sirve de punto de partida para escribir aplicaciones de servicio de larga duración. Para usar la plantilla como base de una aplicación de servicios hospedados:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Cree un nuevo proyecto.
1. Seleccione **Aplicación web de ASP.NET Core**. Seleccione **Siguiente**.
1. Proporcione un nombre para el proyecto en el campo **Nombre del proyecto** o acepte el predeterminado. Seleccione **Crear**.
1. En el cuadro de diálogo **Crear una aplicación web ASP.NET Core**, confirme que las opciones **.NET Core** y **ASP.NET Core 3.0** estén seleccionadas.
1. Seleccione la plantilla **Worker Service**. Seleccione **Crear**.

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code y CLI de .NET Core](#tab/visual-studio-code+netcore-cli)

Desde un shell de comandos, use la plantilla Worker Service (`worker`) con el comando [dotnet new](/dotnet/core/tools/dotnet-new). En el ejemplo siguiente, se crea una aplicación Worker Service llamada `ContosoWorkerService`. Al ejecutar el comando, se crea automáticamente una carpeta para la aplicación `ContosoWorkerService`.

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a>Package

Haga referencia al [metapaquete Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) o agregue una referencia de paquete al paquete [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting).

## <a name="ihostedservice-interface"></a>Interfaz IHostedService

Los servicios hospedados implementan la interfaz <xref:Microsoft.Extensions.Hosting.IHostedService>. Esta interfaz define dos métodos para los objetos administrados por el host:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contiene la lógica para iniciar la tarea en segundo plano. Al utilizar el [host web](xref:fundamentals/host/web-host), se llama a `StartAsync` después de que el servidor se haya iniciado y se haya activado [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*). Al utilizar el [host genérico](xref:fundamentals/host/generic-host), se llama a `StartAsync` antes de que se desencadene `ApplicationStarted`.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): se activa cuando el host está realizando un cierre estable. `StopAsync` contiene la lógica para finalizar la tarea en segundo plano. Implemente <xref:System.IDisposable> y los [finalizadores (destructores)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) para desechar los recursos no administrados.

  El token de cancelación tiene un tiempo de espera predeterminado de cinco segundos para indicar que el proceso de cierre ya no debería ser estable. Cuando se solicita la cancelación en el token:

  * Se deben anular las operaciones restantes en segundo plano que realiza la aplicación.
  * Los métodos llamados en `StopAsync` deberían devolver contenido al momento.

  No obstante, las tareas no se abandonan después de solicitar la cancelación, sino que el autor de la llamada espera a que se completen todas las tareas.

  Si la aplicación se cierra inesperadamente (por ejemplo, porque se produzca un error en el proceso de la aplicación), puede que no sea posible llamar a `StopAsync`. Por lo tanto, los métodos llamados o las operaciones llevadas a cabo en `StopAsync` podrían no producirse.

  Para ampliar el tiempo de espera predeterminado de apagado de 5 segundos, establezca:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> cuando se usa el host genérico. Para obtener más información, vea <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Configuración de los valores de host de tiempo de espera de apagado cuando se usa el host web. Para obtener más información, vea <xref:fundamentals/host/web-host#shutdown-timeout>.

El servicio hospedado se activa una vez al inicio de la aplicación y se cierra de manera estable cuando dicha aplicación se cierra. Si se produce un error durante la ejecución de una tarea en segundo plano, hay que llamar a `Dispose`, aun cuando no se haya llamado a `StopAsync`.

## <a name="timed-background-tasks"></a>Tareas en segundo plano temporizadas

Una tarea en segundo plano temporizada hace uso de la clase [System.Threading.Timer](xref:System.Threading.Timer). El temporizador activa el método `DoWork` de la tarea. El temporizador está deshabilitado en `StopAsync` y se desecha cuando el contenedor de servicios se elimina en `Dispose`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

El servicio se registra en `Startup.ConfigureServices` con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Consumir un servicio con ámbito en una tarea en segundo plano

Para usar [servicios con ámbito](xref:fundamentals/dependency-injection#service-lifetimes) en un elemento `IHostedService`, cree un ámbito. No se crean ámbitos de forma predeterminada para los servicios hospedados.

El servicio de tareas en segundo plano con ámbito contiene la lógica de la tarea en segundo plano. En el siguiente ejemplo, <xref:Microsoft.Extensions.Logging.ILogger> se inserta en el servicio:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

El servicio hospedado crea un ámbito con objeto de resolver el servicio de tareas en segundo plano con ámbito para llamar a su método `DoWork`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Los servicios se registran en `Startup.ConfigureServices`. La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Tareas en segundo plano en cola

Las colas de tareas en segundo plano se basan en <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> de .NET 4.x ([está previsto que se integre en ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

En `QueueHostedService`, las tareas en segundo plano en la cola se quitan de la cola y se ejecutan como un servicio <xref:Microsoft.Extensions.Hosting.BackgroundService>, que es una clase base para implementar `IHostedService` de ejecución prolongada:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Los servicios se registran en `Startup.ConfigureServices`. La implementación `IHostedService` se registra con el método de extensión `AddHostedService`:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

En la clase de modelo de página de índice:

* Se inserta `IBackgroundTaskQueue` en el constructor y se asigna a `Queue`.
* Se inserta <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> y se asigna a `_serviceScopeFactory`. Se usa el generador se para crear instancias de <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, que se usa para crear servicios dentro de un ámbito. Se crea un ámbito para poder usar el elemento `AppDbContext` de la aplicación (un [servicio con ámbito](xref:fundamentals/dependency-injection#service-lifetimes)) para escribir registros de base de datos en `IBackgroundTaskQueue` (un servicio de singleton).

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

Cuando se hace clic en el botón **Agregar tarea** en la página de índice, se ejecuta el método `OnPostAddTask`. Se llama a `QueueBackgroundWorkItem` para poner en cola el elemento de trabajo:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a>Recursos adicionales

* [Implementar tareas en segundo plano en microservicios con IHostedService y la clase BackgroundService](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
