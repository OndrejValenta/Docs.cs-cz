---
title: Konfigurace Identity primární klíče datový typ v ASP.NET Core
author: AdrienTorris
description: Další informace o kroky pro konfiguraci požadovaný datový typ používaný pro ASP.NET Core Identity primární klíč.
ms.author: scaddie
ms.date: 09/28/2017
uid: security/authentication/identity-primary-key-configuration
ms.openlocfilehash: cfec91e1194556385bb884ee44cf79c1fcbbcb56
ms.sourcegitcommit: a1afd04758e663d7062a5bfa8a0d4dca38f42afc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36274353"
---
# <a name="configure-identity-primary-key-data-type-in-aspnet-core"></a>Konfigurace Identity primární klíče datový typ v ASP.NET Core

Jádro ASP.NET Identity můžete konfigurovat datový typ, který používá k reprezentování primární klíč. Používá identity `string` datový typ ve výchozím nastavení. Toto chování můžete přepsat.

## <a name="customize-the-primary-key-data-type"></a>Přizpůsobení primární klíče datový typ

1. Vytvořit vlastní implementaci [IdentityUser](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuser-1) třídy. Představuje typ, který má být použit pro vytvoření uživatelské objekty. V následujícím příkladu, výchozí `string` se nahradí typ `Guid`.

    [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationUser.cs?highlight=4&range=7-13)]

2. Vytvořit vlastní implementaci [IdentityRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityrole-1) třídy. Představuje typ, který se má použít pro vytváření objektů role. V následujícím příkladu, výchozí `string` se nahradí typ `Guid`.

    [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Models/ApplicationRole.cs?highlight=3&range=7-12)]

3. Vytvoření třídy kontextu vlastní databázi. Dědí z třídy kontextu databáze Entity Framework použitý pro identitu. `TUser` a `TRole` argumenty odkazovat na vlastní třídy pro uživatele a role vytvořili v předchozím kroku, v uvedeném pořadí. `Guid` Datový typ je definována pro primární klíč.

    [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Data/ApplicationDbContext.cs?highlight=3&range=9-26)]

4. Registrace třídy kontextu vlastní databázi při přidávání Identity služby v třída při spuštění aplikace.

   # <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET základní 2.x](#tab/aspnetcore2x/)

   `AddEntityFrameworkStores` Metoda nebude přijímat `TKey` argument, protože nebyla v ASP.NET Core 1.x. Datový typ primární klíč je odvodit analýzou `DbContext` objektu.

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=6-8&range=25-37)]

   # <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

   `AddEntityFrameworkStores` Metoda přijímá `TKey` argument označující typ dat primární klíč.

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?highlight=9-11&range=39-55)]

   ---

## <a name="test-the-changes"></a>Testování změny

Po dokončení změny konfigurace odráží vlastnost představující primární klíč nového datového typu. Následující příklad ukazuje, přístupu k vlastnosti v kontroler MVC.

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Controllers/AccountController.cs?name=snippet_GetCurrentUserId&highlight=6)]
