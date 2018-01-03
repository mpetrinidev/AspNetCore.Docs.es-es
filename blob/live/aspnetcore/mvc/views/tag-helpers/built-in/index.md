---
title: Aplicaciones auxiliares de etiquetas integradas de ASP.NET Core
author: pkellner
description: Aplicaciones auxiliares de etiquetas integradas de ASP.NET Core
keywords: "ASP.NET Core, aplicación auxiliar de etiquetas"
ms.author: riande
manager: wpickett
ms.date: 09/13/2017
ms.topic: article
ms.technology: aspnet
ms.prod: aspnet-core
uid: mvc/views/tag-helpers/builtin-th/Index
ms.openlocfilehash: dd732822a715df19c0ee4b6accad3455ad6537da
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="aspnet-core-built-in-tag-helpers"></a><span data-ttu-id="9ff27-104">Aplicaciones auxiliares de etiquetas integradas de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ff27-104">ASP.NET Core built-in Tag Helpers</span></span>

<span data-ttu-id="9ff27-105">Por [Peter Kellner](http://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="9ff27-105">By [Peter Kellner](http://peterkellner.net)</span></span> 

<span data-ttu-id="9ff27-106">ASP.NET Core incluye varias aplicaciones auxiliares de etiquetas integradas para aumentar la productividad.</span><span class="sxs-lookup"><span data-stu-id="9ff27-106">ASP.NET Core includes many built-in Tag Helpers to boost your productivity.</span></span> <span data-ttu-id="9ff27-107">En esta sección se proporciona un resumen de las aplicaciones auxiliares de etiquetas integradas.</span><span class="sxs-lookup"><span data-stu-id="9ff27-107">This section provides an overview of the built-in Tag Helpers.</span></span>

> [!NOTE]
> <span data-ttu-id="9ff27-108">Algunas aplicaciones auxiliares de etiquetas integradas no figuran en la sección, ya que se usan internamente en el motor de vistas de [Razor](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="9ff27-108">There are built-in Tag Helpers which aren't discussed, since they're used internally by the [Razor](xref:mvc/views/razor) view engine.</span></span> <span data-ttu-id="9ff27-109">Esto incluye una aplicación auxiliar de etiquetas para el carácter ~, que se expande hasta la ruta de acceso raíz del sitio web.</span><span class="sxs-lookup"><span data-stu-id="9ff27-109">This includes a Tag Helper for the ~ character, which expands to the root path of the website.</span></span>

## <a name="built-in-aspnet-core-tag-helpers"></a><span data-ttu-id="9ff27-110">Aplicaciones auxiliares de etiquetas integradas de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9ff27-110">Built-in ASP.NET Core Tag Helpers</span></span>

<span data-ttu-id="9ff27-111">**[Aplicación auxiliar de etiquetas de delimitador](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-111">**[Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)**</span></span>

<span data-ttu-id="9ff27-112">**[Aplicación auxiliar de etiquetas de caché](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-112">**[Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)**</span></span>

<span data-ttu-id="9ff27-113">**[Aplicación auxiliar de etiquetas de caché distribuida](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-113">**[Distributed Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)**</span></span>

<span data-ttu-id="9ff27-114">**[Aplicación auxiliar de etiquetas de entorno](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-114">**[Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)**</span></span>

[comment]: **[FormActionTagHelper](xref:mvc/views/tag-helpers/builtin-th/form-action-tag-helper)**

<span data-ttu-id="9ff27-115">**[Aplicación auxiliar de etiquetas de formulario](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-115">**[Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper)**</span></span>

<span data-ttu-id="9ff27-116">**[Aplicación auxiliar de etiquetas de imagen](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-116">**[Image Tag Helper](xref:mvc/views/tag-helpers/builtin-th/image-tag-helper)**</span></span>

<span data-ttu-id="9ff27-117">**[Aplicación auxiliar de etiquetas de entrada](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-117">**[Input Tag Helper](xref:mvc/views/working-with-forms#the-input-tag-helper)**</span></span>

<span data-ttu-id="9ff27-118">**[Aplicación auxiliar de etiquetas de elementos de etiqueta](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-118">**[Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper)**</span></span>

[comment]: **[LinkTagHelper](xref:mvc/views/tag-helpers/builtin-th/link-tag-helper)**

[comment]: **[OptionTagHelper](xref:mvc/views/tag-helpers/builtin-th/option-tag-helper)**

[comment]: **[ScriptTagHelper](xref:mvc/views/tag-helpers/builtin-th/script-tag-helper)**

<span data-ttu-id="9ff27-119">**[Aplicación auxiliar de etiquetas de selección](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-119">**[Select Tag Helper](xref:mvc/views/working-with-forms#the-select-tag-helper)**</span></span>

<span data-ttu-id="9ff27-120">**[Aplicación auxiliar de etiquetas de área de texto](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-120">**[Textarea Tag Helper](xref:mvc/views/working-with-forms#the-textarea-tag-helper)**</span></span>

<span data-ttu-id="9ff27-121">**[Aplicación auxiliar de etiquetas de mensaje de validación](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-121">**[Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper)**</span></span>

<span data-ttu-id="9ff27-122">**[Aplicación auxiliar de etiquetas de resumen de validación](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span><span class="sxs-lookup"><span data-stu-id="9ff27-122">**[Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper)**</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ff27-123">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="9ff27-123">Additional resources</span></span>

* [<span data-ttu-id="9ff27-124">Desarrollo en el cliente</span><span class="sxs-lookup"><span data-stu-id="9ff27-124">Client-Side Development</span></span>](xref:client-side/index)
* [<span data-ttu-id="9ff27-125">Aplicaciones auxiliares de etiquetas</span><span class="sxs-lookup"><span data-stu-id="9ff27-125">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)