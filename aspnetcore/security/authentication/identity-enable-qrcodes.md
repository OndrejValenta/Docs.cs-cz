---
title: Povolit generování kód QR pro TOTP aplikace v ASP.NET Core
author: rick-anderson
description: Zjistit, jak povolit generování kódu QR pro TOTP ověřovací aplikace, které pracovat s ASP.NET Core dvoufaktorové ověřování.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 09/24/2017
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b0d8f104119340b97bd65f1826bb921ca875acf8
ms.sourcegitcommit: 1faf2525902236428dae6a59e375519bafd5d6d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37089968"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Povolit generování kód QR pro TOTP aplikace v ASP.NET Core

ASP.NET Core se dodává s podporou pro ověřovací aplikací pro jednotlivé ověřování. Dva faktor ověřování (2FA), použití založené na čase jednorázové heslo algoritmus (TOTP), jsou tyto aplikace doporučenému přístupu pro 2FA odvětví. 2FA pomocí TOTP je upřednostňovaný k 2FA serveru SMS. Ověřovací aplikaci poskytuje 6 až 8 číslice kódu, které uživatelé musí zadat po potvrzení uživatelského jména a hesla. Ověřovací aplikaci je obvykle nainstalován na Smartphone.

Šablony webové aplikace ASP.NET Core podporovat ověřovací data, ale nemáte poskytují podporu pro generování QRCode. Generátory QRCode usnadňují nastavení 2FA. Tento dokument vás provede přidáním [kód QR](https://wikipedia.org/wiki/QR_code) generování na stránku konfigurace 2FA.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Přidání kódy QR na stránku konfigurace 2FA

Tyto pokyny používají *qrcode.js* z https://davidshimjs.github.io/qrcodejs/ úložišti.

* Stažení [knihovna javascript qrcode.js](https://davidshimjs.github.io/qrcodejs/) k `wwwroot\lib` složku ve vašem projektu.

* V *Pages\Account\Manage\EnableAuthenticator.cshtml* (stránky Razor) nebo *Views\Manage\EnableAuthenticator.cshtml* (MVC), vyhledejte `Scripts` na konci souboru:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Aktualizace `Scripts` části se přidat odkaz na `qrcodejs` knihovny, které jste přidali a volání generovat kód QR. By měla vypadat takto:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Odstraňte odstavce, který odkazy na tyto pokyny.

Spuštění aplikace a zkontrolujte, zda jste naskenujte kód QR a ověřit kód, který prokáže ověřovacích.

## <a name="change-the-site-name-in-the-qr-code"></a>Změňte název webu v kód QR

Název webu v kód QR je převzat ze název projektu, který si zvolíte při počátečním vytvoření projektu. Můžete ji změnit tak, že vyhledá `GenerateQrCodeUri(string email, string unformattedKey)` metoda v *Pages\Account\Manage\EnableAuthenticator.cshtml.cs* souboru (stránky Razor) nebo *Controllers\ManageController.cs* (MVC) souboru.

Ve výchozím kódu ze šablony vypadá takto:

```c#
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

Druhý parametr ve volání `string.Format` je název lokality, provedených od název řešení. Lze změnit na jakoukoli hodnotu, ale musí být vždycky kódovaná adresou URL.

## <a name="using-a-different-qr-code-library"></a>Pomocí různých kód QR knihovny

Kód QR knihovny můžete nahradit své upřednostňované knihovny. Obsahuje kód HTML `qrCode` obsahuje element, do kterého můžete umístit kód QR podle jakýmkoli své knihovny.

Je k dispozici v správně formátovaného adresa URL pro kód QR:

* `AuthenticatorUri` Vlastnost modelu.
* `data-url` Vlastnost `qrCodeData` elementu.

## <a name="totp-client-and-server-time-skew"></a>TOTP klientských a serverových čas zkosení

Ověření TOTP (založené na čase jednorázové heslo) závisí na serveru a ověřovací zařízení má přesnému času. Tokeny pouze trvat 30 sekund. Pokud se nedaří přihlášení 2FA TOTP, zkontrolujte, jestli čas serveru přesné a pokud možno synchronizovány s služby přesné NTP.
