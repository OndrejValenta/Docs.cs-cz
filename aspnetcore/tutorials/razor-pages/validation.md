---
title: Přidání ověřování do stránky ASP.NET Core Razor
author: rick-anderson
description: Objevte, jak přidat ověření pro stránky Razor v ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 08/07/2017
uid: tutorials/razor-pages/validation
ms.openlocfilehash: ea3f26f9377715ea27f19908932d2dcf3cfcbea6
ms.sourcegitcommit: a3675f9704e4e73ecc7cbbbf016a13d2a5c4d725
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39202598"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a>Přidání ověřování do stránky ASP.NET Core Razor

Podle [Rick Anderson](https://twitter.com/RickAndMSFT)

V této části je přidat logiku ověřování k `Movie` modelu. Ověřovací pravidla se uplatňují kdykoli uživatel vytvoří nebo upraví videa.

## <a name="validation"></a>Ověřování

Klíčovým principem vývoj softwaru je volána [suchého](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**neměnit **R**epeat **Y**ourself"). Stránky Razor podporuje vývoj funkce určena jednou, kde se projeví v celé aplikaci. ZKUŠEBNÍ může pomoct snížit objem kódu v aplikaci. ZKUŠEBNÍ díky kód náchylné k chybám a usnadňuje testování a udržovat méně chyb.

Podpora ověřování poskytované stránkami Razor a technologií Entity Framework je typickým příkladem suchého zásady. Ověřovací pravidla deklarativně zadávají se na jednom místě (ve třídě modelu) a pravidel se vynucují kdekoli v aplikaci.

### <a name="adding-validation-rules-to-the-movie-model"></a>Přidání pravidel ověřování do modelu movie

Otevřít *Movie.cs* souboru. [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) poskytuje integrovanou sadu atributů ověření, která se použijí deklarativně třída nebo vlastnost. DataAnnotations taky obsahuje atributy formátování, jako je `DataType` , pomoct při formátování a neposkytují ověření.

Aktualizace `Movie` třídy výhod `Required`, `StringLength`, `RegularExpression`, a `Range` atributů ověření.

::: moniker range="= aspnetcore-2.0"
[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDA.cs?name=snippet1)]

::: moniker-end

Ověření atributy určují chování, které je vynucuje na vlastnosti modelu:

* `Required` a `MinimumLength` atributy znamená, že vlastnost musí mít hodnotu. Ale nic zabraňuje uživateli v zadávání prázdné znaky omezení ověření pro typ připouštějící hodnotu Null. Neumožňující [typů hodnot](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value-types) (například `decimal`, `int`, `float`, a `DateTime`) jsou ze své podstaty povinné a nemusíte `Required` atribut.
* `RegularExpression` Atribut omezuje znaky, které může uživatel zadat. V předchozím kódu `Genre` musí začínat jeden nebo více písmeny a postupujte podle s nula nebo více písmeny, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky. `Rating` musí začínat jeden nebo více písmeny a postupujte podle se nula nebo více písmena, čísla, jednoduché nebo dvojité uvozovky, prázdné znaky nebo spojovníky.
* `Range` Atribut omezuje hodnotu do zadaného rozsahu.
* `StringLength` Atribut Nastaví maximální délku řetězce a volitelně minimální délku. 

Ověřovací pravidla automaticky vynucuje sada ASP.NET Core s pomáhá vytvářet aplikace robustnější. Automatické ověření u modelů pomáhá chránit aplikace, protože nebudou muset pamatovat jejich použití, když se přidá nový kód.

### <a name="validation-error-ui-in-razor-pages"></a>Chyba ověřování uživatelské rozhraní pro stránky Razor

Spusťte aplikaci a přejděte na stránky/filmy.

Vyberte **vytvořit nový** odkaz. Vyplňte formulář s některé neplatné hodnoty. Při ověřování jQuery na straně klienta zjistí chyby, zobrazí chybovou zprávu.

![Video zobrazit formulář kvůli několika chybám ověření na straně klienta jQuery](validation/_static/val.png)

> [!NOTE]
> Není možné zadávat desetinné tečky a čárky v `Price` pole. Pro podporu [k ověřování jQuery](https://jqueryvalidation.org/) v jiných jazycích než angličtině, které používají čárkou (",") pro desetinné čárky a USA retweetovat neanglické formáty kalendářního data, je nutné provést kroky aplikaci poslali do světa. Zobrazit [další prostředky](#additional-resources) Další informace. Teď zadejte celá čísla, jako je 10.

Všimněte si, jak formuláře se automaticky vykreslen chybovou zprávu ověření v každé pole, který obsahuje neplatnou hodnotu. Chyby se vynucují straně klienta (pomocí jazyků JavaScript a jQuery) a na straně serveru (Pokud má uživatel zakázán jazyk JavaScript).

Významné výhodou je, že **žádné** změny kódu byly nezbytné ve vytvoření nebo úprava stránky. Jakmile DataAnnotations byly použité pro model, bylo povoleno ověření uživatelského rozhraní. Stránky Razor, v tomto kurzu automaticky vytvoří nenačítají ověřovacích pravidel (pomocí atributů ověření na vlastnosti `Movie` třída modelu). Test ověření pomocí stránky pro úpravu stejného ověření se aplikuje.

Formulář dat není odeslána na server, dokud nejsou žádné chyby ověřování na straně klienta. Zkontrolujte formulář, který není data zveřejnil(a) jeden nebo více z následujících postupů:

* Vložit přerušení `OnPostAsync` metody. Odeslání formuláře (vyberte **vytvořit** nebo **Uložit**). Nikdy dosáhnete bodu přerušení.
* Použití [nástroj Fiddler](http://www.telerik.com/fiddler).
* Monitorování síťového provozu pomocí vývojářských nástrojů prohlížeče.

### <a name="server-side-validation"></a>Ověřování na straně serveru

Pokud JavaScript je zakázaný v prohlížeči, odeslání formuláře s chybami bude účtovat na server.

Volitelný parametr, test ověření na straně serveru:

* Zakážete jazyka JavaScript v prohlížeči. Pokud nelze zakázat jazyka JavaScript v prohlížeči, zkuste jiný prohlížeč.
* Nastavte zarážky `OnPostAsync` metoda vytvoření nebo úprava stránky.
* Odeslání formuláře se chyby ověření.
* Ověřte, že stav modelu, který je neplatný:

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```

Následující kód ukazuje část *Create.cshtml* stránka, která automaticky dříve v tomto kurzu. Používá se stránkami vytvořit a upravit počáteční formulář pro zobrazení a znovu zobrazit formulář v případě chyby.

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

[Pomocné rutiny značky vstup](xref:mvc/views/working-with-forms) používá [DataAnnotations](https://docs.microsoft.com/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) atributy a vytváří atributy HTML, které jsou potřeba pro architekturu jQuery ověření na straně klienta. [Pomocné rutiny značky ověření](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zobrazí chyby ověření. Zobrazit [ověření](xref:mvc/models/validation) Další informace.

Vytvoření a úprava stránky mít žádné ověřovací pravidla v nich. Ověřovací pravidla a chybové řetězce se zadávají pouze v `Movie` třídy. Tato pravidla ověřování jsou automaticky použita pro stránky Razor, upravovat `Movie` modelu.

Když je potřeba změnit logiku ověřování, se provádí pouze v modelu. Ověření je konzistentní v rámci aplikace (logika ověřování je definována na jednom místě). Ověřování na jednom místě udržet čistý kód a usnadňuje spravují a aktualizují.

## <a name="using-datatype-attributes"></a>Pomocí atributů datový typ

Zkontrolujte `Movie` třídy. `System.ComponentModel.DataAnnotations` Obor názvů obsahuje atributy formátování kromě integrovaná sada atributů ověření. `DataType` Atribut aplikován `ReleaseDate` a `Price` vlastnosti.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

`DataType` Atributy pouze poskytnout nápovědu pro modul zobrazení pro zobrazení dat (a poskytuje atributy, jako `<a>` pro adresy URL a `<a href="mailto:EmailAddress.com">` e-mailu). Použití `RegularExpression` atribut pro ověření formátu data. `DataType` Atribut se používá k určení datový typ, který je specifičtější než vnitřní typ databáze. `DataType` atributy nejsou atributů ověření. V ukázkové aplikaci se zobrazí pouze datum bez času.

`DataType` Výčet poskytuje pro mnoho typů dat, jako je například datum, čas, telefonní číslo, Měna, EmailAddress a další. `DataType` Atribut můžete také povolit aplikace a zajistit tak automaticky specifické pro typ funkce. Například `mailto:` propojení lze vytvořit pro `DataType.EmailAddress`. Výběr data lze zadat pro `DataType.Date` v prohlížečích podporujících HTML5. `DataType` Atributy vysílá HTML 5 `data-` (čteno data dash) atributy, které využívají prohlížeče HTML 5. `DataType` Proveďte atributy **není** žádné ověřování.

`DataType.Date` neurčuje formátu, který se zobrazí datum. Ve výchozím nastavení, zobrazí se pole data podle výchozí formát založený na serveru `CultureInfo`.

::: moniker range=">= aspnetcore-2.1"
`[Column(TypeName = "decimal(18, 2)")]` Anotace dat se vyžaduje, aby správně můžete mapovat Entity Framework Core `Price` měnu v databázi. Další informace najdete v tématu [datové typy](/ef/core/modeling/relational/data-types).
::: moniker-end

`DisplayFormat` Atribut se používá s ohledem na formát data:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

`ApplyFormatInEditMode` Nastavení určuje, že pokud je hodnota zobrazena pro úpravy, měl být použit formátování. Toto chování není vhodné pro některá pole. Například v hodnot měny, pravděpodobně nechcete symbol měny v režimu úpravy uživatelského rozhraní.

`DisplayFormat` Atribut lze použít samostatně, ale je obecně vhodné použít `DataType` atribut. `DataType` Atribut přenáší sémantiku dat na rozdíl od vykreslování na obrazovce a nabízí následující výhody, které vám s DisplayFormat:

* Prohlížeči můžete povolit funkce HTML5 (pro příklad, který znázorňuje ovládacího prvku kalendář, symbol měny odpovídající národní prostředí, odkazy na e-mailu, atd.)
* Ve výchozím prohlížeči bude vykreslovat data ve správném formátu podle vašeho národního prostředí.
* `DataType` Atribut můžete povolit rozhraní ASP.NET Core pro výběr šablony pravé pole vykreslit data. `DisplayFormat` Pokud používá sama používá šablony řetězce.

Poznámka: k ověřování jQuery nefunguje při využití `Range` atribut a `DateTime`. Například následující kód se vždy zobrazí chybu ověřování na straně klienta i v případě, že datum je v zadaném rozsahu:

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

Není obvykle vhodné pro kompilaci pevného data ve vašich modelů použít `Range` atribut a `DateTime` se nedoporučuje.

Následující kód ukazuje kombinace atributů na jednom řádku:

::: moniker range="= aspnetcore-2.0"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"
[!code-csharp[](razor-pages-start/sample/RazorPagesMovie21/Models/MovieDateRatingDAmult.cs?name=snippet1)]

::: moniker-end

[Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) ukazuje pokročilé operace EF Core se stránkami Razor.

### <a name="publish-to-azure"></a>Publikování do Azure

Zobrazit [publikovat webovou aplikaci ASP.NET Core do služby Azure App Service pomocí sady Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) pokyny o tom, jak tuto aplikaci můžete publikovat do Azure.

Děkujeme vám za dokončení tohoto úvodu do stránky Razor. Děkujeme za zpětnou vazbu. [Začínáme s Razor Pages a EF Core](xref:data/ef-rp/intro) je vynikající postupujte až v tomto kurzu.

## <a name="additional-resources"></a>Další zdroje

* [Práce s formuláři](xref:mvc/views/working-with-forms)
* [Globalizace a lokalizace](xref:fundamentals/localization)
* [Úvod do pomocné rutiny značek](xref:mvc/views/tag-helpers/intro)
* [Autor pomocných rutin značek](xref:mvc/views/tag-helpers/authoring)

> [!div class="step-by-step"]
> [Předchozí: Přidání nového pole](xref:tutorials/razor-pages/new-field)
