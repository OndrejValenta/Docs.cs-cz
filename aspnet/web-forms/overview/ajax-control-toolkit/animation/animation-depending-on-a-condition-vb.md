---
uid: web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-vb
title: "Animace v závislosti na podmínce (VB) | Microsoft Docs"
author: wenz
description: "V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace. Zda je animace..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 1b87d8d6-b3f7-4126-b51c-d41442fbf947
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animation-depending-on-a-condition-vb
msc.type: authoredcontent
ms.openlocfilehash: cc8600f33f9c27e1045f5083a126b9d2d1e90303
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="animation-depending-on-a-condition-vb"></a>Animace v závislosti na podmínce (VB)
====================
podle [Christian Wenz](https://github.com/wenz)

[Stáhněte si kód](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation4.vb.zip) nebo [stáhnout PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation4VB.pdf)

> V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace. Zda je animace spustit nebo Ne můžete také závisí na podmínce v podobě určitý kód JavaScript.


## <a name="overview"></a>Přehled

V sadě nástrojů ovládacího prvku ASP.NET AJAX ovládacího prvku animace není právě ovládací prvek ale celé rozhraní pro přidání do ovládacího prvku animace. Zda je animace spustit nebo Ne můžete také závisí na podmínce v podobě určitý kód JavaScript.

## <a name="steps"></a>Kroky

První řadě zahrnují `ScriptManager` na stránce; potom knihovny ASP.NET AJAX je načtena, aby bylo možné použít Toolkitu:

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample1.aspx)]

Animace se použijí na panel textu, který vypadá takto:

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample2.aspx)]

Související třídy CSS pro panel definovat barvu pozadí dobrý a také nastavit pevnou šířku pro panel:

[!code-css[Main](animation-depending-on-a-condition-vb/samples/sample3.css)]

Poté, přidejte `AnimationExtender` na stránku, poskytuje `ID`, `TargetControlID` atribut a povinný údaj`runat="server":`

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample4.aspx)]

V rámci `<Animations>` uzlu, použijte `<OnLoad>` ke spuštění animací, jakmile byla úplným načtením stránky. Místo mezi regulární animace `<Condition>` element se stává play. Kód jazyka JavaScript, který je zadaný jako hodnota `ConditionScript` atribut je provést v době běhu. Pokud se vyhodnotí jako true, animace se spustí, jinak není. Následující kód obsahuje dvě animací, každý z nich vykonáván v případech při náhodných 50 %. Vzhledem k tomu, že může existovat pouze jedna animace v rámci `<OnLoad>`, dva `<Condition>` animací připojeni pomocí `<Sequence>` element:

[!code-aspx[Main](animation-depending-on-a-condition-vb/samples/sample5.aspx)]

Všimněte si, že menší než přihlašovací (`<`) v `ConditionScript` atribut musí být uvozovacími znaky (). Při spuštění tohoto skriptu, buď žádná animace spuštění, nebo jednu ze dvou nemá nebo obě provést.


[![Na panelu je pozvolného vysouvání bez změny velikosti, tak druhý animace spustí první z nich nebyl](animation-depending-on-a-condition-vb/_static/image2.png)](animation-depending-on-a-condition-vb/_static/image1.png)

Na panelu je pozvolného vysouvání bez změny velikosti, tak druhý animace spustí první z nich nebyl ([Kliknutím zobrazit obrázek v plné velikosti](animation-depending-on-a-condition-vb/_static/image3.png))

>[!div class="step-by-step"]
[Předchozí](executing-several-animations-after-each-other-vb.md)
[další](picking-one-animation-out-of-a-list-vb.md)