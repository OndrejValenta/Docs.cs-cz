---
title: Mezipaměť in-memory v ASP.NET Core
author: rick-anderson
description: Zjistěte, jak data v paměti v ASP.NET Core do mezipaměti.
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2018
uid: performance/caching/memory
ms.openlocfilehash: b57e29965edc791ad4ecfe1b6b863a4a3dbe3f09
ms.sourcegitcommit: 506a199274e9fe5fb4070b273ba94f29f14cb619
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2018
ms.locfileid: "39332298"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Mezipaměť in-memory v ASP.NET Core

Podle [Rick Anderson](https://twitter.com/RickAndMSFT), [Jan Luo](https://github.com/JunTaoLuo), a [Steve Smith](https://ardalis.com/)

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([stažení](xref:tutorials/index#how-to-download-a-sample))

## <a name="caching-basics"></a>Základní informace o ukládání do mezipaměti

Ukládání do mezipaměti může výrazně zlepšit výkon a škálovatelnost aplikace tím, že snižuje práci potřebnou k generování obsahu. Ukládání do mezipaměti funguje nejlépe s daty, která se mění jen zřídka. Ukládání do mezipaměti ke zkopírování dat, které mohou být vráceny mnohem rychlejší než z původního zdroje. By měl zapsat a otestovat aplikaci tak, aby nikdy závisí na data uložená v mezipaměti.

ASP.NET Core podporuje několik různé mezipaměti. Nejjednodušší cache je založená na [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), která představuje mezipaměti uloženy v paměti na webovém serveru. Aplikace, které běží na serverové farmě několika serverů by měly zajistit relace jsou vždy navrchu, při použití mezipaměti umístěné v paměti. Rychlé relace Ujistěte se, že následné žádosti z klienta všech přejít na stejném serveru. Například Azure Web apps pomocí [směrování žádostí na aplikace](https://www.iis.net/learn/extensions/planning-for-arr) (směrování žádostí na aplikace) směrovat všechny následné požadavky na stejný server.

Vyžadují jiné rychlé relace ve webové farmě [distribuovaná mezipaměť](distributed.md) abyste se vyhnuli potížím konzistence mezipaměti. U některých aplikací může podporovat distribuované mezipaměti vyšší horizontální navýšení kapacity než mezipaměti v paměti. Pomocí distribuované mezipaměti snižuje zátěž při mezipaměť k externímu procesu.

`IMemoryCache` Mezipaměti vyřazení položky mezipaměti zatížení paměti, není-li [mezipaměti priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) je nastavena na `CacheItemPriority.NeverRemove`. Můžete nastavit `CacheItemPriority` nastavte prioritu, se kterým mezipaměti vyloučí položky tím snižuje jejich přetížení paměti.

Mezipaměť v paměti můžete uložit libovolný objekt; rozhraní distribuované mezipaměti je omezená na `byte[]`.

### <a name="cache-guidelines"></a>Pokyny k mezipaměti

* Kód by měl mít vždy záložní volbu pro načtení dat a **není** závisí na hodnotu uloženou v mezipaměti k dispozici.
* Mezipaměť používá prostředek scare paměti. Omezit růst mezipaměti:
  * Proveďte **není** použít externí vstup jako klíče mezipaměti.
  * Chcete-li omezit růst mezipaměti pomocí vypršení platnosti.
  * [Použití SetSize, velikost a hodnota parametru SizeLimit omezení velikosti mezipaměti](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a>Pomocí IMemoryCache

Ukládání do mezipaměti v paměti je *služby* , který se odkazuje z vaší aplikace pomocí [injektáž závislostí](../../fundamentals/dependency-injection.md). Volání `AddMemoryCache` v `ConfigureServices`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Žádosti `IMemoryCache` instance v konstruktoru:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), která je k dispozici v [metabalíček Microsoft.aspnetcore.all](xref:fundamentals/metapackage).

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache` vyžaduje balíček NuGet [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), která je k dispozici v [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app).

::: moniker-end

Následující kód používá [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) ke kontrole, jestli časem spadá do mezipaměti. Pokud čas neukládají do mezipaměti, je vytvořen a přidán do mezipaměti se nová položka [nastavit](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Aktuální čas a čas v mezipaměti se zobrazí:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

V mezipaměti `DateTime` hodnota zůstává v mezipaměti, i když existují požadavky v rámci časového limitu (a žádný vyřazení z důvodu přetížení paměti). Následující obrázek ukazuje aktuální čas a starší čas načtení z mezipaměti:

![Index zobrazení se zobrazí dva různé časy.](memory/_static/time.png)

Následující kód používá [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) a [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) ukládání dat do mezipaměti. 

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Následující kód volá [získat](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) načíst uložené v mezipaměti Doba:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

Zobrazit [IMemoryCache metody](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) a [CacheExtensions metody](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) popis metody mezipaměti.

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

Následující ukázka:

- Nastaví čas, absolutní vypršení platnosti. Toto je maximální doba, kterou položka mezipaměti a zabrání přílišnému zastarávání při průběžně obnovení klouzavé vypršení platnosti položky.
- Nastaví klouzavou dobu vypršení platnosti. Požadavky, které přístup k této položce v mezipaměti se resetuje klouzavé vypršení platnosti hodiny.
- Nastaví prioritu mezipaměti `CacheItemPriority.NeverRemove`.
- Nastaví [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) , která bude volána po provedení položky dojde k jeho vyřazení z mezipaměti. Zpětné volání je spuštěn na jiném podprocesu než kód, který odebere položku z mezipaměti.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Použití SetSize, velikost a hodnota parametru SizeLimit omezení velikosti mezipaměti

A `MemoryCache` instance může volitelně zadat a vynutit omezení velikosti. Omezení velikosti paměti nemá definovanou jednotka měření vzhledem k tomu, že mezipaměť nemá žádný mechanismus pro měření velikosti položky. Pokud je nastaven limit velikosti mezipaměti, musíte zadat všechny položky velikosti. Zadaná velikost je v jednotkách, které vývojář klikne.

Příklad:

* Pokud webová aplikace byla primárně ukládání do mezipaměti řetězce, každý velikost položky mezipaměti může být délka řetězce.
* Aplikace může určit velikost všechny položky jako 1 a maximální velikost je počet záznamů.

Následující kód vytvoří unitless pevnou velikost [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) přístupné [injektáž závislostí](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[Hodnota parametru SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) nemá žádné jednotky. Položky uložené v mezipaměti musí zadejte velikost v libovolné jednotky, které považují za nejvhodnější, pokud byla nastavena velikost mezipaměti. Všichni uživatelé instance mezipaměti by měla používat stejné jednotky systému. Položky nebudou zapisována do mezipaměti pokud součet velikostí položka uložená v mezipaměti přesahuje hodnotu zadanou pomocí `SizeLimit`. Pokud je nastaveno žádné omezení velikosti mezipaměti, se bude ignorovat velikost mezipaměti, nastavte v položce.

Následující kód registrů `MyMemoryCache` s [injektáž závislostí](xref:fundamentals/dependency-injection) kontejneru.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` je vytvořen jako nezávislé mezipaměti pro součásti, které jsou tato velikost omezená mezipaměť si vědomi a vědět, jak odpovídajícím způsobem nastavit velikost položky mezipaměti.

Následující kód používá `MyMemoryCache`:

[! [kód csharp] (memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet) [](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Velikost položky mezipaměti můžete nastavit [velikost](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) nebo [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) – metoda rozšíření:

[! [kód csharp] (memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2 & zvýraznění = 9, 10, 14, 15) [](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>Závislosti mezipaměti

Následující příklad ukazuje, jak do vypršení platnosti položky mezipaměti, když vyprší platnost závislé položky. A `CancellationChangeToken` se přidá do položku z mezipaměti. Když `Cancel` je volán na `CancellationTokenSource`, obě položky mezipaměti se vyřadí.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Použití `CancellationTokenSource` umožňuje více položek mezipaměti vyřazení jako skupinu. S `using` vzor ve výše uvedeném kódu, vytvářejí položky mezipaměti `using` bloku zdědí nastavení vypršení platnosti a aktivačních událostí.

## <a name="additional-notes"></a>Další poznámky

- Při použití zpětné volání k znovu vytvořit položku mezipaměti:

  - Více požadavků můžete najít hodnotu uloženou v mezipaměti klíče prázdný protože zpětného volání nebyl dokončen.
  - Výsledkem může být několik vláken opětovného vyplnění položku z mezipaměti.

- Při použití jedné položky cache a vytvořte další podřízené zkopíruje nastavení podle času vypršení platnosti a vypršení platnosti tokenů nadřazená položka. Podřízené není ruční odebrání vypršela platnost, nebo aktualizace nadřazené položky.

- Použití [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) nastavit zpětná volání, které aktivuje po položka mezipaměti je odstraněn z mezipaměti.

## <a name="additional-resources"></a>Další zdroje

* [Práce s distribuovanou mezipamětí](xref:performance/caching/distributed)
* [Zjištění změn se změna tokenů](xref:fundamentals/primitives/change-tokens)
* [Ukládání odpovědí do mezipaměti](xref:performance/caching/response)
* [Middleware pro ukládání odpovědí do mezipaměti](xref:performance/caching/middleware)
* [Uložení pomocné rutiny značky do mezipaměti](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)
* [Pomocná rutina značek v distribuované mezipaměti](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper)