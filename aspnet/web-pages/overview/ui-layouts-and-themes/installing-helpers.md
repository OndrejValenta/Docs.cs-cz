---
uid: web-pages/overview/ui-layouts-and-themes/installing-helpers
title: Instalace pomocné rutiny v ASP.NET Web Pages lokality (Razor) | Dokumentace Microsoftu
author: tfitzmac
description: Tento článek popisuje postup instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). Pomocné rutiny je opětovně použitelnou komponentu, která obsahuje kód a značky pro každý...
ms.author: aspnetcontent
ms.date: 02/18/2014
ms.assetid: 5e968ead-906a-45ea-ac2a-c70e57e1a9b1
msc.legacyurl: /web-pages/overview/ui-layouts-and-themes/installing-helpers
msc.type: authoredcontent
ms.openlocfilehash: db3dff9f2d70577bb0618335c0100b9899e87727
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37838611"
---
<a name="installing-a-helper-in-an-aspnet-web-pages-razor-site"></a>Instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor)
====================
podle [Tom FitzMacken](https://github.com/tfitzmac)

> Tento článek popisuje postup instalace pomocné rutiny na webu rozhraní ASP.NET Web Pages (Razor). A *pomocné rutiny* je opětovně použitelnou komponentu, která obsahuje kód a značky k provedení úkolu, který může být zdlouhavý nebo složitý.
> 
> Co se dozvíte:
> 
> - Postup instalace pomocné rutiny na webu vytvořené pomocí služby WebMatrix 3.
>   
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>V tomto kurzu použili verze softwaru
> 
> 
> - Služba WebMatrix 3


## <a name="overview-of-helpers"></a>Přehled pomocné rutiny

Některé úlohy, které uživatelé často chtějí dělat na webových stránkách vyžadují velké množství kódu nebo vyžadovat další znalostní báze. Mezi příklady patří zobrazení grafu pro data. Vložení tlačítko "Sledovat" Twitter na stránce. odeslání e-mailů z vašeho webu; oříznutí nebo změny velikosti obrázků; pomocí služby PayPal pro váš web. Abyste usnadnili snadnou udělat Tyhle druhy věcí, rozhraní ASP.NET Web Pages vám umožní používat *pomocné rutiny*. Pomocné rutiny jsou součásti nainstalovat pro lokality a, které umožní vám provádět typické úlohy s použitím pouze řádku či dvou od kódu Razor.

ASP.NET Web Pages má několik pomocných rutin součástí. Mnoho pomocné rutiny jsou však k dispozici v balíčcích (doplňky), které jsou k dispozici pomocí Správce balíčků NuGet. NuGet umožňuje vybrat balíček pro instalaci a pak se postará o všechny podrobnosti o instalaci.

## <a name="installing-a-helper-in-webmatrix-3"></a>Instalace pomocné rutiny v nástroji WebMatrix 3

1. Ve službě WebMatrix 3, klikněte na tlačítko **NuGet** tlačítko.

    ![Dialogové okno Galerie NuGet v nástroji WebMatrix](installing-helpers/_static/image1.png)
2. To spustí Správce balíčků NuGet a zobrazí dostupné balíčky. Do vyhledávacího pole zadejte klíčové slovo pro pomocné rutiny, které chcete nainstalovat.

    ![Dialogové okno Galerie NuGet v nástroji WebMatrix](installing-helpers/_static/image2.png)
3. Vyberte balíček a pak klikněte na tlačítko **nainstalovat**. Klikněte na tlačítko **Ano** když se zobrazí výzva, pokud chcete nainstalovat balíček a značí, že souhlasíte s podmínkami.

     Pokud to je poprvé, kdy jste nainstalovali pomocné rutiny, vytvoří NuGet složky na vašem webu pro kód, který tvoří pomocné rutiny.
4. K odinstalaci pomocné rutiny, klikněte na tlačítko **Galerie** tlačítko, klikněte na tlačítko **nainstalováno** kartu a vyberte balíček, který chcete odinstalovat.

## <a name="installing-the-twitter-helper"></a>Instalace pomocné rutiny Twitter

Nejnovější verzi rozhraní Twitter API není kompatibilní s Pomocník Twitter, kterou instalujete prostřednictvím balíčku NuGet. Místo toho [Pomocník Twitter se službou WebMatrix](twitter-helper.md) najdete informace o tom, jak nastavit pomocná rutina Twitteru ve vašem projektu.

<a id="Additional_Resources"></a>
## <a name="additional-resources"></a>Další prostředky


[Představujeme ASP.NET Web Pages 2 – základy programování](../getting-started/introducing-razor-syntax-c.md)

[Pomocník Twitter pomocí služby WebMatrix](twitter-helper.md)
