---
title: Hodnota hash hesla v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak hodnoty hash hesla pomocí rozhraní API ASP.NET Core Data Protection.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/password-hashing
ms.openlocfilehash: aef22ab91e76afdb5f54dc37bcee7128420b6f3b
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36272984"
---
# <a name="hash-passwords-in-aspnet-core"></a>Hodnota hash hesla v ASP.NET Core

Kód ochrany dat, základní obsahuje balíček *Microsoft.AspNetCore.Cryptography.KeyDerivation* obsahující funkce odvození kryptografické klíče. Tento balíček je součástí samostatné a nemá žádné závislosti na výkon systému ochrany dat. Můžete použít zcela nezávisle. Zdroj existuje souběžně s kód ochrany dat, základní pro potřeby.

Balíček teď nabízí metody `KeyDerivation.Pbkdf2` což umožňuje použití algoritmu hash hesla pomocí [PBKDF2 algoritmus](https://tools.ietf.org/html/rfc2898#section-5.2). Toto rozhraní API je velmi podobné existující rozhraní .NET Framework [Rfc2898DeriveBytes typu](/dotnet/api/system.security.cryptography.rfc2898derivebytes), ale jsou tři důležité rozdíly:

1. `KeyDerivation.Pbkdf2` Metoda podporuje použití více PRFs (aktuálně `HMACSHA1`, `HMACSHA256`, a `HMACSHA512`), zatímco `Rfc2898DeriveBytes` zadejte podporuje jenom `HMACSHA1`.

2. `KeyDerivation.Pbkdf2` Metoda zjistí v aktuálním operačním systému a pokusí se zvolí nejvíce optimalizované implementaci rutiny, poskytuje mnohem lepší výkon v některých případech. (V systému Windows 8, nabízí přibližně 10 x propustnost `Rfc2898DeriveBytes`.)

3. `KeyDerivation.Pbkdf2` Metoda vyžaduje volajícímu zadat všechny parametry (salt, PRF a počet iterací). `Rfc2898DeriveBytes` Typ poskytuje výchozí hodnoty pro tyto.

[!code-csharp[](password-hashing/samples/passwordhasher.cs)]

Najdete ve zdrojovém kódu pro identitu ASP.NET Core `PasswordHasher` případ použití typ pro reálného prostředí.
