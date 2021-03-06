---
uid: mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2
title: Jak upgradovat ASP.NET MVC 4 a webového rozhraní API projektu ASP.NET MVC 5 a webového rozhraní API 2 | Dokumentace Microsoftu
author: Rick-Anderson
description: ASP.NET MVC 5 a webovým rozhraním API 2 přeneste celou řadu nových funkcí včetně směrování atributů, filtry ověřování a mnoho dalšího.
ms.author: aspnetcontent
ms.date: 10/17/2013
ms.assetid: db0d02d9-58e8-4a0b-8d7d-b8df8ea97b88
msc.legacyurl: /mvc/overview/releases/how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2
msc.type: authoredcontent
ms.openlocfilehash: 8a50c188a2283af46789739e710be69159799695
ms.sourcegitcommit: b28cd0313af316c051c2ff8549865bff67f2fbb4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37826741"
---
<a name="how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2"></a>Postup upgradu ASP.NET MVC 4 a projektem webového rozhraní API a webovém rozhraní API 2 technologie ASP.NET MVC 5
====================
Podle [Rick Anderson](https://github.com/Rick-Anderson)

> ASP.NET MVC 5 a webovým rozhraním API 2 přeneste celou řadu nových funkcí včetně směrování atributů, filtry ověřování a mnoho dalšího. Zobrazit [ https://www.asp.net/vnext ](https://www.asp.net/core) další podrobnosti.
> 
> Tento názorný postup vás provede kroky nutné k upgradu na nejnovější verzi vaší aplikace.  
> 
> > [!NOTE]
> > Podrobnosti najdete na [ASP.NET and Web Tools pro Visual Studio 2013 – poznámky k](../../../visual-studio/overview/2013/release-notes.md) informace o nejnovější změny z MVC 4 a webového rozhraní API na novou verzi.
> 
>   
> 
> Tento článek zapsal Youngjune Hongkong a Rick Anderson ( [ @RickAndMSFT ](https://twitter.com/#!/RickAndMSFT) )


## <a name="upgrade-steps"></a>Postup upgradu

1. Zálohování vašeho projektu. Tento názorný postup bude nutné provést změny do souboru projektu, konfigurace balíčku a soubory web.config.
2. Pro upgrade z webového rozhraní API pro webové rozhraní API 2 v souboru global.asax, změňte:

    [!code-csharp[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample1.cs)]

   až

    [!code-csharp[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample2.cs)]
3. Zajistěte, aby všechny balíčky, které používají vaše projekty jsou kompatibilní s MVC 5 a webovým rozhraním API 2. Následující tabulka uvádí MVC 4 a webového rozhraní API související s balíčky, než se musí změnit. Pokud máte balíček, který je závislý na jednu z níže uvedených balíčků, kontaktujte prosím vydavatele získat novější verze, které jsou kompatibilní s MVC 5 a webovým rozhraním API 2. Pokud máte zdrojový kód pro tyto balíčky, by je znovu zkompilovat pomocí nového sestavení MVC 5 a webovým rozhraním API 2.   

    | **Id balíčku** | **Stará verze** | **Nová verze** |
    | --- | --- | --- |
    | Microsoft.AspNet.Razor | 2.0.x.x | 3.0.0 |
    | Microsoft.AspNet.WebPages | 2.0.x.x | 3.0.0 |
    | Microsoft.AspNet.WebPages.WebData | 2.0.x.x | 3.0.0 |
    | Microsoft.AspNet.WebPages.OAuth | 2.0.x.x | 3.0.0 |
    | Microsoft.AspNet.Mvc | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.Mvc.Facebook | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.Core | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.SelfHost | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.Client | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.OData | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.WebHost | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.Tracing | 4.0.x.x | 5.0.0 |
    | Microsoft.AspNet.WebApi.HelpPage | 4.0.x.x | 5.0.0 |
    | Microsoft.Net.Http | 2.0.x. | 2.2.x. |
    | Microsoft.Data.OData | 5.2.x | 5.6.x |
    | System.Spatial | 5.2.x | 5.6.x |
    | Microsoft.Data.Edm | 5.2.x | 5.6.x |
    | Microsoft.AspNet.Mvc.FixedDisplayModes | <o:p> </o:p> | Odebrat |
    | Microsoft.AspNet.WebPages.Administration | <o:p> </o:p> | Odebrat |
    | Microsoft-Web-Helpers | <o:p> </o:p> | Microsoft.AspNet.WebHelpers |

    > [!NOTE]
    > Microsoft-weboví Pomocníci bylo nahrazeno tématem Microsoft.AspNet.WebHelpers. Doporučujeme nejprve odeberte starý balíček a pak nainstalujte novější balíček.   
    >   
    > Neexistuje žádná různé verze kompatibilita mezi hlavní balíčky technologie ASP.NET. Například MVC 5 je kompatibilní s pouze Razor 3 a ne 2 Razor.
4. Otevřete svůj projekt v sadě Visual Studio 2013.
5. Odeberte některé z následujících balíčků ASP.NET NuGet, které jsou nainstalovány. Bude odeberte pomocí konzoly Správce balíčků (PMC). Otevřete konzolu PMC, vyberte **nástroje** nabídky a pak vyberte **Správce balíčků knihoven** vyberte **Konzola správce balíčků**. Váš projekt nemusí zahrnovat všechny z nich.

    1. `Microsoft.AspNet.WebPages.Administration`  
   Tento balíček bude přidán obvykle při upgradu z MVC 3 na MVC 4. Chcete-li ho odebrat, spusťte následující příkaz v konzole PMC:  
        `Uninstall-Package -Id Microsoft.AspNet.WebPages.Administration`
    2. `Microsoft-Web-Helpers`   
   Tento balíček obsahuje byla přejmenované jako `Microsoft.AspNet.WebHelpers`. Chcete-li ho odebrat, spusťte následující příkaz v konzole PMC:  
        `Uninstall-Package -Id Microsoft-Web-Helpers`
    3. `Microsoft.AspNet.Mvc.FixedDisplayMode`  
   Tento balíček obsahuje alternativní řešení pro chyby v MVC 4, která byla opravena v MVC 5. Chcete-li ho odebrat, spusťte následující příkaz v konzole PMC:  
        `Uninstall-Package -Id Microsoft.AspNet.Mvc.FixedDisplayModes`
6. Upgrade všech balíčků ASP.NET NuGet pomocí konzole PMC. V konzole PMC spusťte následující příkaz:  
    `Update-Package`  
   `Update-Package` Příkaz bez parametrů se aktualizuje každý balíček. Můžete aktualizovat balíčky samostatně pomocí argumentu ID. Další informace o příkazu update spuštění `get-help update-package` .

## <a name="update-the-application-webconfig-file"></a>Aktualizovat aplikaci *web.config* souboru

Nezapomeňte tyto změny v aplikaci *web.config* soubor není *web.config* soubor *zobrazení* složky.

Vyhledejte `<runtime>/<assemblyBinding>` části a proveďte následující změny:

1. Elementy s atributem name "System.Web.Mvc" změňte číslo verze z "4.0.0.0" na "5.0.0.0". (Dvě změny v tomto elementu.)
2. Elementy s atributem název &quot;System.Web.Helpers "a &quot;System.Web.WebPages&quot; změnit číslo verze z"2.0.0.0"na"3.0.0.0". Čtyři změny se projeví, dva v jednotlivých prvků.

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample3.xml?highlight=6,10,14)]
3. Vyhledejte `<appSettings>` části a aktualizovat webpages:version z 2.0.0.0.0 k 3.0.0.0, jak je znázorněno níže:

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample4.xml?highlight=2)]
4. Odeberte všechny úrovně důvěryhodnosti než úplné. Příklad:

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample5.xml?highlight=2)]

## <a name="update-the-webconfig-files-under-the-views-folder"></a>Aktualizace *web.config* soubory ve složce zobrazení

Pokud vaše aplikace používá oblasti, budete také muset aktualizovat v každém *web.config* soubor *zobrazení* dílčí složku složky pro každou oblast.

1. Aktualizujte všechny prvky, které obsahují "System.Web.Mvc" z "4.0.0.0" verze na verzi "5.0.0.0".  

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample6.xml?highlight=2)]

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample7.xml?highlight=4-6,8)]
2. Aktualizujte všechny prvky, které obsahují "System.Web.WebPages.Razor" z "2.0.0.0" verze na verzi "3.0.0.0". Pokud tato část obsahuje "System.Web.WebPages", aktualizujte tyto prvky z verze "2.0.0.0" verze "3.0.0.0"  

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample8.xml?highlight=3-5)]
3. Pokud jste odebrali `Microsoft-Web-Helpers` při instalaci balíčku NuGet v předchozím kroku, `Microsoft.AspNet.WebHelpers` pomocí následujícího příkazu v konzole PMC:  
    `Install-Package -Id Microsoft.AspNet.WebHelpers`
4. Pokud vaše aplikace používá [User.IsInRole()](https://msdn.microsoft.com/en-us/library/system.web.security.roleprincipal.isinrole(v=vs.110).aspx) metodu, přidejte následující text do *Web.config* souboru.

    [!code-xml[Main](how-to-upgrade-an-aspnet-mvc-4-and-web-api-project-to-aspnet-mvc-5-and-web-api-2/samples/sample9.xml)]

## <a name="final-steps"></a>Závěrečné kroky

Sestavení a testování aplikace.

Odeberte tento typ projektů MVC 4 GUID ze souborů projektu.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na název projektu a pak vyberte **uvolnit projekt**.
2. Klikněte pravým tlačítkem na projekt a vyberte Upravit ProjectName.csproj.
3. Vyhledejte `ProjectTypeGuids` element a potom projekt MVC 4 odebrat identifikátor GUID, `{E3E379DF-F4C6-4180-9B81-6769533ABE47}`.
4. Uložte a zavřete soubor otevřít projekt.
5. Klikněte pravým tlačítkem na projekt a vyberte **znovu načíst projekt**.
