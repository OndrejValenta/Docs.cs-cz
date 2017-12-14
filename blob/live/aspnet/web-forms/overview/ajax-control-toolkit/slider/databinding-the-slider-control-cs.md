---
uid: web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-cs
title: "Datové vazby v ovládacím prvku posuvník (C#) | Microsoft Docs"
author: wenz
description: "Ovládacího prvku posuvník Toolkitu AJAX poskytuje grafické jezdce, která se dá řídit pomocí myši. Je možné svázat aktuální pozice..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: b7f77869-aa1d-4025-924f-622c57112db6
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-cs
msc.type: authoredcontent
ms.openlocfilehash: 2aa770bce5969a4ab57893d5ceecc127cf7f7872
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="databinding-the-slider-control-c"></a><span data-ttu-id="60141-104">Datové vazby v ovládacím prvku posuvník (C#)</span><span class="sxs-lookup"><span data-stu-id="60141-104">Databinding the Slider Control (C#)</span></span>
====================
<span data-ttu-id="60141-105">podle [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="60141-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="60141-106">[Stáhněte si kód](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider0.cs.zip) nebo [stáhnout PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/slider0CS.pdf)</span><span class="sxs-lookup"><span data-stu-id="60141-106">[Download Code](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider0.cs.zip) or [Download PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/slider0CS.pdf)</span></span>

> <span data-ttu-id="60141-107">Ovládacího prvku posuvník Toolkitu AJAX poskytuje grafické jezdce, která se dá řídit pomocí myši.</span><span class="sxs-lookup"><span data-stu-id="60141-107">The Slider control in the AJAX Control Toolkit provides a graphical slider that can be controlled using the mouse.</span></span> <span data-ttu-id="60141-108">Je možné k vytvoření vazby aktuální pozici jezdec na další ovládací prvek ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="60141-108">It is possible to bind the current position of the slider to another ASP.NET control.</span></span>


## <a name="overview"></a><span data-ttu-id="60141-109">Přehled</span><span class="sxs-lookup"><span data-stu-id="60141-109">Overview</span></span>

<span data-ttu-id="60141-110">Ovládacího prvku posuvník Toolkitu AJAX poskytuje grafické jezdce, která se dá řídit pomocí myši.</span><span class="sxs-lookup"><span data-stu-id="60141-110">The Slider control in the AJAX Control Toolkit provides a graphical slider that can be controlled using the mouse.</span></span> <span data-ttu-id="60141-111">Je možné k vytvoření vazby aktuální pozici jezdec na další ovládací prvek ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="60141-111">It is possible to bind the current position of the slider to another ASP.NET control.</span></span>

## <a name="steps"></a><span data-ttu-id="60141-112">Kroky</span><span class="sxs-lookup"><span data-stu-id="60141-112">Steps</span></span>

<span data-ttu-id="60141-113">Chcete aktivovat funkce ASP.NET AJAX a sady nástrojů ovládacího prvku `ScriptManager` řízení musíte umístit kdekoli na stránce (ale uvnitř `<form>` element):</span><span class="sxs-lookup"><span data-stu-id="60141-113">In order to activate the functionality of ASP.NET AJAX and the Control Toolkit, the `ScriptManager` control must be put anywhere on the page (but within the `<form>` element):</span></span>

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample1.aspx)]

<span data-ttu-id="60141-114">Dál přidejte dva `TextBox` ovládací prvky na stránku.</span><span class="sxs-lookup"><span data-stu-id="60141-114">Next, add two `TextBox` controls to the page.</span></span> <span data-ttu-id="60141-115">Jeden se převede na grafické jezdce a dalších jedna bude obsahovat pozice posuvníku.</span><span class="sxs-lookup"><span data-stu-id="60141-115">One will be transformed into a graphical slider, and the other one will hold the position of the slider.</span></span>

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample2.aspx)]

<span data-ttu-id="60141-116">Dalším krokem je již v posledním kroku.</span><span class="sxs-lookup"><span data-stu-id="60141-116">The next step is already the final step.</span></span> <span data-ttu-id="60141-117">`SliderExtender` Řízení z ovládacího prvku ASP.NET AJAX Toolkit díky jezdce mimo první textového pole a automaticky aktualizuje druhého textového pole, když posuvník umístit změny.</span><span class="sxs-lookup"><span data-stu-id="60141-117">The `SliderExtender` control from the ASP.NET AJAX Control Toolkit makes a slider out of the first text box and automatically updates the second text box when the slider position changes.</span></span> <span data-ttu-id="60141-118">V pořadí, pro který chcete pracovat `SliderExtender`na `TargetControlID` musí být nastaven na ID první textového pole; `BoundControlID` musí být nastaven na ID druhého textového pole.</span><span class="sxs-lookup"><span data-stu-id="60141-118">In order for that to work, The `SliderExtender`'s `TargetControlID` attribute must be set to the ID of the first text box; the `BoundControlID` attribute must be set to the ID of the second text box.</span></span>

[!code-aspx[Main](databinding-the-slider-control-cs/samples/sample3.aspx)]

<span data-ttu-id="60141-119">Jak vidíte v prohlížeči, datové vazby funguje v obou směrech: zadáním nové hodnoty v textovém poli aktualizace pozici posuvníku.</span><span class="sxs-lookup"><span data-stu-id="60141-119">As you can see in the browser, the data binding works in both directions: entering a new value in the text box updates the slider's position.</span></span> <span data-ttu-id="60141-120">Pokud provedete druhé textové pole jen pro čtení, můžete přidat slabé ochrany pro textové pole tak, aby se pro uživatele ručně aktualizovat hodnotu zde složitější.</span><span class="sxs-lookup"><span data-stu-id="60141-120">If you make the second text box read only, you may add a weak protection to the text field so that it is harder for the user to manually update the value in there.</span></span>


<span data-ttu-id="60141-121">[![Posuvník a textové pole, které jsou synchronizované](databinding-the-slider-control-cs/_static/image2.png)](databinding-the-slider-control-cs/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="60141-121">[![Slider and text box are in sync](databinding-the-slider-control-cs/_static/image2.png)](databinding-the-slider-control-cs/_static/image1.png)</span></span>

<span data-ttu-id="60141-122">Posuvník a textové pole, které jsou synchronizované ([Kliknutím zobrazit obrázek v plné velikosti](databinding-the-slider-control-cs/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="60141-122">Slider and text box are in sync ([Click to view full-size image](databinding-the-slider-control-cs/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="60141-123">[Předchozí](using-the-slider-control-with-auto-postback-cs.md)
[další](using-the-slider-control-with-auto-postback-vb.md)</span><span class="sxs-lookup"><span data-stu-id="60141-123">[Previous](using-the-slider-control-with-auto-postback-cs.md)
[Next](using-the-slider-control-with-auto-postback-vb.md)</span></span>