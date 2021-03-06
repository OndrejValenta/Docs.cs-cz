---
title: Zabránit útokům otevřete přesměrování v ASP.NET Core
author: ardalis
description: Ukazuje, jak zabránit otevřete přesměrování útoky na aplikace ASP.NET Core
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 75591e37753c24bc959b3a96a54abebb51728364
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36278295"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Zabránit útokům otevřete přesměrování v ASP.NET Core

Webové aplikace, který přesměruje na adresu URL, která je zadána prostřednictvím požadavku například řetězci dotazu nebo formuláře dat může potenciálně manipulováno k přesměrování uživatelů na externí, škodlivý URL. Tato manipulaci se nazývá útok otevřete přesměrování.

Vždy, když vaše aplikace logiky přesměruje na zadané adrese URL, je nutné ověřit, že adresu URL pro přesměrování nikdo neoprávněně nemanipuloval. ASP.NET Core obsahuje integrovanou funkci k ochraně aplikace před útoky otevřete přesměrování (označované také jako otevřený přesměrování).

## <a name="what-is-an-open-redirect-attack"></a>Co je útok otevřete přesměrování?

Při přístupu k prostředkům, které vyžadují ověřování webových aplikací často přesměrovat uživatele na přihlašovací stránku. Zahrnuje typlically přesměrování `returnUrl` parametr řetězce dotazu tak, aby uživatel se může vracet na původně požadovanou URL adresu po jejich úspěšně přihlášeni. Poté, co se uživatel ověřuje, že přesměrováni na měl původně požadovanou adresu URL.

Vzhledem k tomu, že cílová adresa URL je zadána v řetězci dotazu požadavku, uživatel se zlými úmysly může manipulovat s řetězci dotazu. Zmanipulovanou řetězce dotazu by se mohl lokality tak, aby přesměruje uživatele na stránku externí, škodlivý. Tento postup se nazývá útok otevřete přesměrování (nebo přesměrování).

### <a name="an-example-attack"></a>Příklad útoku

Uživatel se zlými úmysly může vyvíjet útok díky uživatel se zlými úmysly přístup k pověření uživatele nebo citlivé informace ve vaší aplikaci. Zahájit útoku, jejich přimět kliknutím na odkaz na přihlašovací stránku vašeho webu, s uživateli `returnUrl` hodnotu querystring adresu URL. Například [NerdDinner.com](http://nerddinner.com) ukázkovou aplikaci (napsané pro rozhraní ASP.NET MVC) zahrnuje takové přihlašovací stránky zde: `http://nerddinner.com/Account/LogOn?returnUrl=/Home/About`. Útoku pak postupujte podle následujících kroků:

1. Uživatel klikne na odkaz `http://nerddinner.com/Account/LogOn?returnUrl=http://nerddiner.com/Account/LogOn` (Všimněte si, druhý adresa URL je nerddi**n**če, není nerddi**nn**če).
2. Uživatel se přihlásí úspěšně.
3. Uživatel je přesměrován (lokalitou) na `http://nerddiner.com/Account/LogOn` (škodlivé weby, které vypadá skutečné lokality).
4. Uživatel znovu přihlásí (poskytnutí škodlivý lokality své přihlašovací údaje) a je přesměrován zpět na web skutečné.

Uživatel se pravděpodobně domníváte jejich první pokus o přihlášení se nezdařilo a jejich druhý byla úspěšná. S největší pravděpodobností zůstanou nebere v úvahu ohrožený přihlašovacích údajů.

![Proces útoku otevřete přesměrování](preventing-open-redirects/_static/open-redirection-attack-process.png)

Některé servery kromě přihlašovací stránky, zadejte přesměrování stránky nebo koncové body. Představte si aplikace se zobrazí stránka s otevřete přesměrování, `/Home/Redirect`. Útočník by mohl vytvořit, například pomocí odkazu v e-mailu, který přejde na `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Běžný uživatel na adrese URL a zjistit, že začíná název vaší lokality. Důvěřující, který, bude kliknutím na odkaz. Otevřete přesměrování by uživatel pak pošlete do lokality phishing, která vypadá totožná s tímto počítačem, a uživatel by pravděpodobně přihlášení, které budou věřit je váš web.

## <a name="protecting-against-open-redirect-attacks"></a>Ochrana proti útokům na otevřete přesměrování

Při vývoji webových aplikací, považovat za nedůvěryhodným všechna data zadaný uživatelem. Pokud aplikace obsahuje funkce, který přesměruje uživatele na základě obsahu adresy URL, zajistěte, aby takové přesměrování jsou pouze místně v rámci vaší aplikace (nebo známé adresy URL, není libovolnou URL, která může být zadána v řetězci dotazu).

### <a name="localredirect"></a>LocalRedirect

Použití `LocalRedirect` Pomocná metoda od základní `Controller` třídy:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` bude vyvolána výjimka, pokud je zadaná adresa URL není místní. Jinak se chová podobně jako `Redirect` metoda.

### <a name="islocalurl"></a>IsLocalUrl

Použití [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) metoda před přesměrování otestovat adresy URL:

Následující příklad ukazuje, jak zkontrolovat, zda je adresa URL místní před přesměrování.

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

`IsLocalUrl` Metoda chrání uživatelé z nechtěně přesměrování na škodlivé weby. Přihlaste se na podrobnosti o adresu URL, který byl poskytnut, pokud nejsou místní adresa URL je zadáno v situaci, kde je očekávána místní adresa URL. Protokolování přesměrování adresy URL může pomoci při diagnostice přesměrování útoky.
