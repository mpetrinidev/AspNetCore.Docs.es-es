---
title: "Partes de la aplicación de ASP.NET Core"
author: ardalis
description: "Obtenga información acerca de cómo utilizar componentes de la aplicación, que son abstrations sobre los recursos de una aplicación, para configurar la aplicación para detectar o evitar la carga de características desde un ensamblado."
keywords: "Núcleo de ASP.NET, parte de la aplicación, se parte de la aplicación"
ms.author: riande
manager: wpickett
ms.date: 1/4/2017
ms.topic: article
ms.assetid: b355a48e-a15c-4d58-b69c-899963613a98
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/extensibility/app-parts
ms.openlocfilehash: a5205ebab6c827b4e6af63287e56fe2b8f72c933
ms.sourcegitcommit: 418e6aa4ab79474ecc4d0a6af573a3759b113fe4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2017
---
# <a name="application-parts-in-aspnet-core"></a><span data-ttu-id="bba5c-104">Partes de la aplicación de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bba5c-104">Application Parts in ASP.NET Core</span></span>

[<span data-ttu-id="bba5c-105">Ver o descargar el código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="bba5c-105">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample)

<span data-ttu-id="bba5c-106">Un *parte de la aplicación* es una abstracción sobre los recursos de una aplicación, desde el que MVC características como controladores, componentes de la vista, o se pueden detectar aplicaciones auxiliares de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="bba5c-106">An *Application Part* is an abstraction over the resources of an application, from which MVC features like controllers, view components, or tag helpers may be discovered.</span></span> <span data-ttu-id="bba5c-107">Un ejemplo de una parte de la aplicación es un AssemblyPart, que encapsula una referencia de ensamblado y expone los tipos y las referencias de la compilación.</span><span class="sxs-lookup"><span data-stu-id="bba5c-107">One example of an application part is an AssemblyPart, which encapsulates an assembly reference and exposes types and compilation references.</span></span> <span data-ttu-id="bba5c-108">*Proveedores de características* funcionan con los componentes de la aplicación para rellenar las características de una aplicación de MVC de ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bba5c-108">*Feature providers* work with application parts to populate the features of an ASP.NET Core MVC app.</span></span> <span data-ttu-id="bba5c-109">Es el caso de uso principal para las partes de la aplicación para que pueda configurar la aplicación para detectar (o si se evita la carga) características MVC desde un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="bba5c-109">The main use case for application parts is to allow you to configure your app to discover (or avoid loading) MVC features from an assembly.</span></span>

## <a name="introducing-application-parts"></a><span data-ttu-id="bba5c-110">Introducción a partes de la aplicación</span><span class="sxs-lookup"><span data-stu-id="bba5c-110">Introducing Application Parts</span></span>

<span data-ttu-id="bba5c-111">Aplicaciones MVC cargar sus características de [partes de la aplicación](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span><span class="sxs-lookup"><span data-stu-id="bba5c-111">MVC apps load their features from [application parts](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpart).</span></span> <span data-ttu-id="bba5c-112">En concreto, el [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) clase representa una parte de la aplicación que está respaldada por un ensamblado.</span><span class="sxs-lookup"><span data-stu-id="bba5c-112">In particular, the [AssemblyPart](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) class represents an application part that is backed by an assembly.</span></span> <span data-ttu-id="bba5c-113">Puede utilizar estas clases para detectar y cargar las características MVC, tales como controladores, componentes de la vista, aplicaciones auxiliares de etiquetas y orígenes de compilación de razor.</span><span class="sxs-lookup"><span data-stu-id="bba5c-113">You can use these classes to discover and load MVC features, such as controllers, view components, tag helpers, and razor compilation sources.</span></span> <span data-ttu-id="bba5c-114">El [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) es responsable de seguimiento de los componentes de la aplicación y proveedores de características disponibles para la aplicación MVC.</span><span class="sxs-lookup"><span data-stu-id="bba5c-114">The [ApplicationPartManager](/aspnet/core/api/microsoft.aspnetcore.mvc.applicationparts.applicationpartmanager) is responsible for tracking the application parts and feature providers available to the MVC app.</span></span> <span data-ttu-id="bba5c-115">Puede interactuar con el `ApplicationPartManager` en `Startup` cuando configura MVC:</span><span class="sxs-lookup"><span data-stu-id="bba5c-115">You can interact with the `ApplicationPartManager` in `Startup` when you configure MVC:</span></span>

```csharp
// create an assembly part from a class's assembly
var assembly = typeof(Startup).GetTypeInfo().Assembly;
services.AddMvc()
    .AddApplicationPart(assembly);

// OR
var assembly = typeof(Startup).GetTypeInfo().Assembly;
var part = new AssemblyPart(assembly);
services.AddMvc()
    .ConfigureApplicationPartManager(apm => p.ApplicationParts.Add(part));
```

<span data-ttu-id="bba5c-116">De forma predeterminada MVC buscará el árbol de dependencias y buscar controladores (incluso en otros ensamblados).</span><span class="sxs-lookup"><span data-stu-id="bba5c-116">By default MVC will search the dependency tree and find controllers (even in other assemblies).</span></span> <span data-ttu-id="bba5c-117">Para cargar un ensamblado arbitrario (por ejemplo, desde un complemento que no se hace referencia en tiempo de compilación), puede utilizar una parte de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bba5c-117">To load an arbitrary assembly (for instance, from a plugin that isn't referenced at compile time), you can use an application part.</span></span>

<span data-ttu-id="bba5c-118">Puede utilizar elementos de aplicación para *evitar* buscando controladores en una ubicación o un ensamblado concreto.</span><span class="sxs-lookup"><span data-stu-id="bba5c-118">You can use application parts to *avoid* looking for controllers in a particular assembly or location.</span></span> <span data-ttu-id="bba5c-119">Puede controlar qué elementos (o ensamblados) están disponibles para la aplicación mediante la modificación de la `ApplicationParts` colección de la `ApplicationPartManager`.</span><span class="sxs-lookup"><span data-stu-id="bba5c-119">You can control which parts (or assemblies) are available to the app by modifying the `ApplicationParts` collection of the `ApplicationPartManager`.</span></span> <span data-ttu-id="bba5c-120">El orden de las entradas de la `ApplicationParts` colección no es importante.</span><span class="sxs-lookup"><span data-stu-id="bba5c-120">The order of the entries in the `ApplicationParts` collection is not important.</span></span> <span data-ttu-id="bba5c-121">Es importante configurar totalmente el `ApplicationPartManager` antes de usarlo para configurar los servicios en el contenedor.</span><span class="sxs-lookup"><span data-stu-id="bba5c-121">It is important to fully configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="bba5c-122">Por ejemplo, debe configurar totalmente el `ApplicationPartManager` antes de invocar `AddControllersAsServices`.</span><span class="sxs-lookup"><span data-stu-id="bba5c-122">For example, you should fully configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="bba5c-123">Si no lo hace así, significará que agregar controladores en partes de la aplicación después de que no se verán afectadas las llamadas de método (no obtener registrado como servicios) que podría dar lugar a bevavior incorrecta de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="bba5c-123">Failing to do so, will mean that controllers in application parts added after that method call will not be affected (will not get registered as services) which might result in incorrect bevavior of your application.</span></span>

<span data-ttu-id="bba5c-124">Si tiene un ensamblado que contiene los controladores que no desea que se utilicen, quítelo de la `ApplicationPartManager`:</span><span class="sxs-lookup"><span data-stu-id="bba5c-124">If you have an assembly that contains controllers you do not want to be used, remove it from the `ApplicationPartManager`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p =>
    {
        var dependentLibrary = p.ApplicationParts
            .FirstOrDefault(part => part.Name == "DependentLibrary");

        if (dependentLibrary != null)
        {
           p.ApplicationParts.Remove(dependentLibrary);
        }
    })
```

<span data-ttu-id="bba5c-125">Además de ensamblado del proyecto y sus ensamblados dependientes, la `ApplicationPartManager` incluirá partes de `Microsoft.AspNetCore.Mvc.TagHelpers` y `Microsoft.AspNetCore.Mvc.Razor` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="bba5c-125">In addition to your project's assembly and its dependent assemblies, the `ApplicationPartManager` will include parts for `Microsoft.AspNetCore.Mvc.TagHelpers` and `Microsoft.AspNetCore.Mvc.Razor` by default.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="bba5c-126">Proveedores de características de la aplicación</span><span class="sxs-lookup"><span data-stu-id="bba5c-126">Application Feature Providers</span></span>

<span data-ttu-id="bba5c-127">Proveedores de características de la aplicación examinar los componentes de la aplicación y proporciona características para las partes.</span><span class="sxs-lookup"><span data-stu-id="bba5c-127">Application Feature Providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="bba5c-128">Existen proveedores de características integradas para las siguientes características MVC:</span><span class="sxs-lookup"><span data-stu-id="bba5c-128">There are built-in feature providers for the following MVC features:</span></span>

* [<span data-ttu-id="bba5c-129">Controladores</span><span class="sxs-lookup"><span data-stu-id="bba5c-129">Controllers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="bba5c-130">Referencia de metadatos</span><span class="sxs-lookup"><span data-stu-id="bba5c-130">Metadata Reference</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.compilation.metadatareferencefeatureprovider)
* [<span data-ttu-id="bba5c-131">Aplicaciones auxiliares de etiquetas</span><span class="sxs-lookup"><span data-stu-id="bba5c-131">Tag Helpers</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="bba5c-132">Componentes de la vista</span><span class="sxs-lookup"><span data-stu-id="bba5c-132">View Components</span></span>](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="bba5c-133">Proveedores de características que se heredan de `IApplicationFeatureProvider<T>`, donde `T` es el tipo de la característica.</span><span class="sxs-lookup"><span data-stu-id="bba5c-133">Feature providers inherit from `IApplicationFeatureProvider<T>`, where `T` is the type of the feature.</span></span> <span data-ttu-id="bba5c-134">Puede implementar su propia característica proveedores para cualquiera de los tipos de características de MVC mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="bba5c-134">You can implement your own feature providers for any of MVC's feature types listed above.</span></span> <span data-ttu-id="bba5c-135">El orden de los proveedores de características en el `ApplicationPartManager.FeatureProviders` colección puede ser importante, puesto que proveedores posteriores pueden reaccionar ante las acciones realizadas por proveedores anteriores.</span><span class="sxs-lookup"><span data-stu-id="bba5c-135">The order of feature providers in the `ApplicationPartManager.FeatureProviders` collection can be important, since later providers can react to actions taken by previous providers.</span></span>

### <a name="sample-generic-controller-feature"></a><span data-ttu-id="bba5c-136">Ejemplo: Característica de controladora genérica</span><span class="sxs-lookup"><span data-stu-id="bba5c-136">Sample: Generic controller feature</span></span>

<span data-ttu-id="bba5c-137">De forma predeterminada, ASP.NET Core MVC omite controladores genéricos (por ejemplo, `SomeController<T>`).</span><span class="sxs-lookup"><span data-stu-id="bba5c-137">By default, ASP.NET Core MVC ignores generic controllers (for example, `SomeController<T>`).</span></span> <span data-ttu-id="bba5c-138">Este ejemplo usa un proveedor de la característica de controlador que se ejecuta una vez que el proveedor predeterminado y agrega instancias de controlador genérico de una lista especificada de tipos (definido en `EntityTypes.Types`):</span><span class="sxs-lookup"><span data-stu-id="bba5c-138">This sample uses a controller feature provider that runs after the default provider and adds generic controller instances for a specified list of types (defined in `EntityTypes.Types`):</span></span>

<span data-ttu-id="bba5c-139">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]</span><span class="sxs-lookup"><span data-stu-id="bba5c-139">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerFeatureProvider.cs?highlight=13&range=18-36)]</span></span>

<span data-ttu-id="bba5c-140">Los tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="bba5c-140">The entity types:</span></span>

<span data-ttu-id="bba5c-141">[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]</span><span class="sxs-lookup"><span data-stu-id="bba5c-141">[!code-csharp[Main](./app-parts/sample/AppPartsSample/Model/EntityTypes.cs?range=6-16)]</span></span>

<span data-ttu-id="bba5c-142">El proveedor de características se agrega en `Startup`:</span><span class="sxs-lookup"><span data-stu-id="bba5c-142">The feature provider is added in `Startup`:</span></span>

```csharp
services.AddMvc()
    .ConfigureApplicationPartManager(p => 
        p.FeatureProviders.Add(new GenericControllerFeatureProvider()));
```

<span data-ttu-id="bba5c-143">De forma predeterminada, los nombres de controlador genérico utilizados para el enrutamiento sería del formulario *GenericController'1 [Widget]* en lugar de *Widget*.</span><span class="sxs-lookup"><span data-stu-id="bba5c-143">By default, the generic controller names used for routing would be of the form *GenericController\`1[Widget]* instead of *Widget*.</span></span> <span data-ttu-id="bba5c-144">El siguiente atributo se usa para modificar el nombre que corresponda al tipo genérico usado por el controlador:</span><span class="sxs-lookup"><span data-stu-id="bba5c-144">The following attribute is used to modify the name to correspond to the generic type used by the controller:</span></span>

<span data-ttu-id="bba5c-145">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]</span><span class="sxs-lookup"><span data-stu-id="bba5c-145">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericControllerNameConvention.cs)]</span></span>

<span data-ttu-id="bba5c-146">La `GenericController` clase:</span><span class="sxs-lookup"><span data-stu-id="bba5c-146">The `GenericController` class:</span></span>

<span data-ttu-id="bba5c-147">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]</span><span class="sxs-lookup"><span data-stu-id="bba5c-147">[!code-csharp[Main](./app-parts/sample/AppPartsSample/GenericController.cs?highlight=5-6)]</span></span>

<span data-ttu-id="bba5c-148">El resultado, cuando se solicita una ruta coincidente:</span><span class="sxs-lookup"><span data-stu-id="bba5c-148">The result, when a matching route is requested:</span></span>

![Ejemplo de salida de la aplicación de ejemplo que se lee, 'Hello desde un controlador de Sproket genérico'.](app-parts/_static/generic-controller.png)

### <a name="sample-display-available-features"></a><span data-ttu-id="bba5c-150">Ejemplo: Características disponibles de pantalla</span><span class="sxs-lookup"><span data-stu-id="bba5c-150">Sample: Display available features</span></span>

<span data-ttu-id="bba5c-151">Puede iterar a través de las características rellenos disponibles para la aplicación solicitando una `ApplicationPartManager` a través de [inyección de dependencia](../../fundamentals/dependency-injection.md) y usarlo para rellenar las instancias de la característica adecuada:</span><span class="sxs-lookup"><span data-stu-id="bba5c-151">You can iterate through the populated features available to your app by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md) and using it to populate instances of the appropriate features:</span></span>

<span data-ttu-id="bba5c-152">[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]</span><span class="sxs-lookup"><span data-stu-id="bba5c-152">[!code-csharp[Main](./app-parts/sample/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]</span></span>

<span data-ttu-id="bba5c-153">Resultado de ejemplo:</span><span class="sxs-lookup"><span data-stu-id="bba5c-153">Example output:</span></span>

![Ejemplo de salida de la aplicación de ejemplo](app-parts/_static/available-features.png)