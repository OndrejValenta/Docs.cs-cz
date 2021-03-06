---
title: Implementace serveru webové kestrel v ASP.NET Core
author: rick-anderson
description: Další informace o Kestrel, napříč platformami webový server pro ASP.NET Core.
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/02/2018
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 797fce6273eba29d19e640c301d2f4d2059370cc
ms.sourcegitcommit: 927e510d68f269d8335b5a7c8592621219a90965
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39342195"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>Implementace serveru webové kestrel v ASP.NET Core

Podle [Petr Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a [Stephen Halter](https://twitter.com/halter73)

Kestrel je platformově univerzální [webového serveru pro ASP.NET Core](xref:fundamentals/servers/index). Kestrel je webový server, který je obsažen ve výchozím nastavení v šablonách projektů ASP.NET Core.

Kestrel podporuje následující funkce:

* HTTPS
* Neprůhledný upgrade nepoužívá k zapnutí [WebSockets](https://github.com/aspnet/websockets)
* Soketů systému UNIX pro vysoký výkon za serveru Nginx

Kestrel se podporuje na všech platformách a verze, které podporuje .NET Core.

[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:tutorials/index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Kdy použít Kestrel pomocí reverzního proxy serveru

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Kestrel můžete použít samostatně nebo se *reverzní proxy server*, jako je například Apache, IIS nebo Nginx. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel po některé předběžného zpracování.

![Kestrel komunikuje přímo s Internetu bez reverzní proxy server](kestrel/_static/kestrel-to-internet2.png)

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Buď konfiguraci&mdash;s nebo bez něj reverzní proxy server&mdash;je platný a podporované konfigurace pro hostování pro ASP.NET Core 2.0 nebo novější.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Pokud aplikace přijímá požadavky jenom z interní sítě, Kestrel lze použít přímo jako server aplikace.

![Kestrel komunikuje přímo s vaší interní sítě](kestrel/_static/kestrel-to-internal.png)

Pokud je zveřejnit aplikaci k Internetu, použít službu IIS, serveru Nginx nebo Apache jako *reverzní proxy server*. Reverzní proxy server přijímá požadavky HTTP z Internetu a předává je na Kestrel po některé předběžného zpracování.

![Kestrel nepřímo komunikuje přes Internet prostřednictvím reverzního proxy serveru, jako je například Apache, IIS nebo Nginx](kestrel/_static/kestrel-to-internet.png)

Reverzní proxy je vyžadován pro nasazení hraniční (vystavené pro provoz z Internetu) z bezpečnostních důvodů. Verze 1.x Kestrel nemají úplný doplněk obranu před útoky, jako je například omezení počtu souběžných připojení, odpovídající časové limity a omezení velikosti.

---

Scénář reverzního proxy serveru existuje, pokud existuje víc aplikací, které sdílejí stejné IP adresy a portu, které běží na jednom serveru. Kestrel tento scénář nepodporuje, protože Kestrel nepodporuje sdílení stejné IP adresy a portu mezi více procesy. Když Kestrel je nakonfigurovaná k naslouchání na portu, zpracovává Kestrel veškerý síťový provoz na tento port bez ohledu na to, požadavky se hlavička hostitele. Reverzní proxy server, který můžete sdílet porty má schopnost Kestrel na jedinečné IP adresy a portu směrování žádostí.

I v případě reverzního proxy serveru není povinné, pomocí reverzního proxy serveru může být dobrou volbou:

* Může být omezena vystavené veřejné útoku na aplikace, které hostuje.
* Poskytuje další úroveň ochrany a konfigurace.
* Integrace může lépe se stávající infrastrukturou.
* Zjednodušuje Vyrovnávání zatížení a konfigurace protokolu SSL. Reverzní proxy server vyžaduje certifikát SSL, a tento server může komunikovat s aplikačních serverů v interní síti přes standardní HTTP.

> [!WARNING]
> Pokud nepoužíváte reverzního proxy serveru s hostitelem filtrování povolena, [hostitele filtrování](#host-filtering) musí být povolené.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>Jak používat Kestrel v aplikacích ASP.NET Core

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) balíčku je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all] (xref:fundamentals / Microsoft.aspnetcore.all app) (ASP.NET Core 2.1 nebo novější).

Šablony projektů ASP.NET Core pomocí Kestrel ve výchozím nastavení. V *Program.cs*, kód volání šablony [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), který volá [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) na pozadí.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

Nainstalujte [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) balíček NuGet.

Volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) rozšiřující metody na [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder?view=aspnetcore-1.1) v `Main` metodu, zadáte některý [Kestrel možnosti](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions?view=aspnetcore-1.1) vyžaduje, jak je znázorněno v následující části.

[!code-csharp[](kestrel/samples/1.x/KestrelSample/Program.cs?name=snippet_Main&highlight=13-19)]

---

### <a name="kestrel-options"></a>Možnosti kestrel

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

Webový server Kestrel má omezení možnosti konfigurace, které jsou obzvláště užitečné v nasazeních s přístupem k Internetu. Pár důležitých omezení, které se dají přizpůsobit:

* Maximální počet klientských připojení
* Velikost textu maximální požadavku
* Minimální požadavek tělo přenosová rychlost

Nastavte na tyto a další omezení [omezení](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.limits) vlastnost [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) třídy. `Limits` Vlastnost obsahuje instanci [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits) třídy.

**Maximální počet klientských připojení**

[MaxConcurrentConnections](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentconnections)  
[MaxConcurrentUpgradedConnections](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxconcurrentupgradedconnections)

Lze nastavit maximální počet souběžných otevřená připojení TCP pro celou aplikaci s následujícím kódem:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

Neexistuje samostatné limit pro připojení, která se upgradovaly z protokolu HTTP nebo HTTPS na jiné protokol (například v požadavku Websocket). Po dokončení upgradu spojení se započítává `MaxConcurrentConnections` limit.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maximální počet připojení je neomezený počet (null) ve výchozím nastavení.

**Velikost textu maximální požadavku**

[MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)

Výchozí velikost těla maximální požadavku je 30,000,000 bajtů, což je přibližně 28.6 MB.

Doporučený postup, chcete-li přepsat omezení v aplikaci ASP.NET Core MVC je použít [RequestSizeLimit](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) atributu na metodu akce:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikace u každého požadavku:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Můžete přepsat nastavení pro konkrétní žádost a v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Pokud se pokusíte nakonfigurovat limit na vyžádání po spuštění aplikace k přečtení požadavku, je vyvolána výjimka. Je `IsReadOnly` vlastnost, která označuje, zda `MaxRequestBodySize` vlastnost je ve stavu jen pro čtení, což znamená, je příliš pozdě Konfigurace limitu.

**Minimální požadavek tělo přenosová rychlost**

[MinRequestBodyDataRate](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minrequestbodydatarate)  
[MinResponseDataRate](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.minresponsedatarate)

Kestrel kontroluje každou sekundu, pokud je dat přicházejících u určenou míru v bajtech/sekundu. Pokud míra klesne pod minimální, vypršení časového limitu připojení. Období odkladu je množství času, aby Kestrel poskytuje klienta ke zvýšení jeho míra odesílání z až Toto minimum, frekvence není kontrolován během této doby. Období odkladu pomáhá předejít, vyřadit připojení, která původně odesílají data s nízkou rychlostí kvůli zpomalit TCP-start.

Výchozí minimální rychlost je 240 bajtů za sekundu s období odkladu 5 sekund.

Minimální sazba platí také pro odpověď. Kód pro nastavení limitu požadavků a omezení odpovědi je stejná s výjimkou s `RequestBody` nebo `Response` v názvech vlastností a interface.

Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové sazby v *Program.cs*:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-7)]

Sazby za žádost můžete nakonfigurovat v middlewaru:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=5-8)]

Informace o dalších možnostech Kestrel a omezení najdete tady:

* [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions)
* [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits)
* [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

Informace o možnostech Kestrel a omezení najdete tady:

* [Třída KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserveroptions?view=aspnetcore-1.1)
* [KestrelServerLimits](/dotnet/api/microsoft.aspnetcore.server.kestrel.kestrelserverlimits?view=aspnetcore-1.1)

---

### <a name="endpoint-configuration"></a>Konfigurace koncového bodu

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

::: moniker range="= aspnetcore-2.0"
Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`. Volání [naslouchání](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) nebo [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) metody [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) konfigurace předpony adres URL a portů pro Kestrel. `UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` proměnnou prostředí také pracovní ale mají omezení, později uvedené v této části.

`urls` Konfigurační klíč hostitele musí pocházet z konfigurace hostitele, není konfigurace aplikace. Přidávání `urls` klíče a hodnoty *appsettings.json* konfigurace hostitele nemá vliv, protože hostitel je zcela inicializován době konfigurace je pro čtení, z konfiguračního souboru. Ale `urls` klíče v *appsettings.json* jde použít s [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) na tvůrce hostitele a nakonfigurujte hostitele:

```csharp
var config = new ConfigurationBuilder()
    .SetBasePath(Directory.GetCurrentDirectory())
    .AddJsonFile("appSettings.json", optional: true, reloadOnChange: true)
    .Build();

var host = new WebHostBuilder()
    .UseKestrel()
    .UseConfiguration(config)
    .UseContentRoot(Directory.GetCurrentDirectory())
    .UseStartup<Startup>()
    .Build();
```

::: moniker-end
::: moniker range=">= aspnetcore-2.1"

Ve výchozím nastavení ASP.NET Core váže na:

* `http://localhost:5000`
* `https://localhost:5001` (Pokud je místní vývojový certifikát k dispozici)

Certifikát pro vývoj se vytvoří:

* Když [.NET Core SDK](/dotnet/core/sdk) je nainstalována.
* [Nástroji dev-certs](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.

Některé prohlížeče vyžadují, abyste udělili explicitní oprávnění pro prohlížeč důvěřovat certifikátům místní vývoj.

ASP.NET Core 2.1 a vyšší šablony projektů konfigurace aplikací ve výchozím nastavení spouští na protokol HTTPS a zahrnout [přesměrování protokolu HTTPS a HSTS podporují](xref:security/enforcing-ssl).

Volání [naslouchání](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) nebo [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) metody [KestrelServerOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions) konfigurace předpony adres URL a portů pro Kestrel.

`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` proměnnou prostředí také pracovní ale mají omezení, později uvedené v této části (výchozí certifikát musí být k dispozici pro koncový bod HTTPS Konfigurace).

ASP.NET Core 2.1 `KestrelServerOptions` konfigurace:

**ConfigureEndpointDefaults (akce&lt;ListenOptions&gt;)**  
Určuje konfiguraci `Action` pro spuštění v každý zadaný koncový bod. Volání `ConfigureEndpointDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.

**ConfigureHttpsDefaults (akce&lt;HttpsConnectionAdapterOptions&gt;)**  
Určuje konfiguraci `Action` ke spuštění pro každý koncový bod HTTPS. Volání `ConfigureHttpsDefaults` více než jednou nahradí předchozí `Action`s poslední `Action` zadané.

**Configure(IConfiguration)**  
Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá [parametry IConfiguration](/dotnet/api/microsoft.extensions.configuration.iconfiguration) jako vstup. Konfigurace musí být určená ke konfiguračnímu oddílu Kestrel.

**ListenOptions.UseHttps**  
Nakonfigurujte Kestrel k používání HTTPS.

`ListenOptions.UseHttps` rozšíření:

* `UseHttps` &ndash; Nakonfigurujte Kestrel k používání HTTPS pomocí certifikátu výchozí. Vyvolá výjimku, pokud je nakonfigurovaný žádný výchozí certifikát.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps` Parametry:

* `filename` je název a cesta k souboru soubor certifikátu, relativní k adresáři, který obsahuje soubory obsahu aplikace.
* `password` je heslo pro přístup k datům certifikátu X.509.
* `configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`. Vrátí `ListenOptions`.
* `storeName` je do úložiště certifikátů, ze kterého se má načíst certifikát.
* `subject` je název subjektu certifikátu.
* `allowInvalid` Označuje, pokud neplatné certifikáty by měl být, jako jsou certifikáty podepsané svým držitelem.
* `location` je umístění úložiště pro načtení certifikátu.
* `serverCertificate` je certifikát X.509.

V produkčním prostředí musí být explicitně nakonfigurován protokol HTTPS. Minimálně je třeba zadat výchozího certifikátu.

Podporované konfigurace je popsáno dále:

* Žádná konfigurace
* Nahraďte výchozí certifikát z konfigurace
* Změnit výchozí nastavení v kódu

*Žádná konfigurace*

Naslouchá kestrel `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí cert).

Určení adres URL pomocí:

* `ASPNETCORE_URLS` proměnné prostředí.
* `--urls` argument příkazového řádku.
* `urls` Konfigurační klíč hostitele.
* `UseUrls` metody rozšíření.

Další informace najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace přepisování](xref:fundamentals/host/web-host#override-configuration).

Hodnota zadaná pomocí těchto přístupů může být jeden nebo více HTTP a HTTPS koncové body (HTTPS Pokud je k dispozici výchozí cert). Nakonfigurujte tuto hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"`).

*Nahraďte výchozí certifikát z konfigurace*

[WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) volání `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` ve výchozím nastavení se načíst konfiguraci Kestrel. Schéma konfigurace k nastavení výchozí HTTPS aplikace je k dispozici pro Kestrel. Konfigurovat několik koncových bodů, včetně adresy URL a certifikáty, které chcete použít, ze souboru na disku nebo z úložiště certifikátů.

V následujícím *appsettings.json* příkladu:

* Nastavte **AllowInvalid** k `true` tak, aby povolovala použití neplatné certifikáty (například certifikáty podepsané svým držitelem).
* Libovolný koncový bod HTTPS, který nemá určenou certifikát (**HttpsDefaultCert** v následujícím příkladu) spadne zpět na cert definované v části **certifikáty** > **výchozí**  nebo certifikát pro vývoj.

```json
{
"Kestrel": {
  "EndPoints": {
    "Http": {
      "Url": "http://localhost:5000"
    },

    "HttpsInlineCertFile": {
      "Url": "https://localhost:5001",
      "Certificate": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    },

    "HttpsInlineCertStore": {
      "Url": "https://localhost:5002",
      "Certificate": {
        "Subject": "<subject; required>",
        "Store": "<certificate store; defaults to My>",
        "Location": "<location; defaults to CurrentUser>",
        "AllowInvalid": "<true or false; defaults to false>"
      }
    },

    "HttpsDefaultCert": {
      "Url": "https://localhost:5003"
    },

    "Https": {
      "Url": "https://*:5004",
      "Certificate": {
      "Path": "<path to .pfx file>",
      "Password": "<certificate password>"
      }
    }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

O alternativu k použití **cesta** a **heslo** pro jakýkoliv certifikát je uzel a určete certifikát, pomocí polí úložiště certifikátů. Například **certifikáty** > **výchozí** certifikát lze zadat jako:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; defaults to My>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Schéma poznámek:

* Koncové body názvy jsou malá a velká písmena. Například `HTTPS` a `Https` jsou platné.
* `Url` Parametr je povinný pro každý koncový bod. Formát pro tento parametr je stejné jako na nejvyšší úrovni `Urls` parametr konfigurace s výjimkou, že je omezena na jednu hodnotu.
* Nahraďte tyto koncové body jsou definované na nejvyšší úrovni `Urls` místo přidávání k nim. Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní se koncové body definované v konfiguračním oddílu.
* `Certificate` Část je nepovinná. Pokud `Certificate` oddílu není zadán, budou použity výchozí hodnoty definované v předchozích případech. Pokud nejsou k dispozici žádné výchozí hodnoty, vyvolá výjimku, server a nepodaří spustit.
* `Certificate` Části podporuje obě **cesta**&ndash;**heslo** a **subjektu**&ndash;**Store** certifikáty.
* Tímto způsobem lze definovat libovolný počet koncových bodů tak dlouho, dokud není způsobují konflikty portu.
* `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` Vrátí `KestrelConfigurationLoader` s `.Endpoint(string name, options => { })` metodu, která slouží k doplnění nastavení konfigurovaný koncový bod:

  ```csharp
  serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  Můžete také přímý přístup k `KestrelServerOptions.ConfigurationLoader` chcete zachovat iterace na existující zavaděč, jako je ta poskytuje [WebHost.CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder).

* Oddíl konfigurace pro každý koncový bod je k dispozici na možnosti v `Endpoint` metodu tak, aby mohou číst vlastní nastavení.
* Více konfigurací může být načten voláním `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` znovu s jinou část. Pouze poslední konfigurace se použije, pokud `Load` je explicitně zavolán v předchozích instancí. Microsoft.aspnetcore.all nevolá `Load` tak, aby jeho výchozí konfigurační oddíl může být nahrazen.
* `KestrelConfigurationLoader` zrcadlení `Listen` rozhraní API z řady `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfiguračním může být nakonfigurována na stejném místě. Tato přetížení nepoužívejte názvy a využívat pouze výchozí nastavení z konfigurace.

*Změnit výchozí nastavení v kódu*

`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` můžete použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepisuje výchozí certifikát uvedený v předchozím scénáři. `ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měla být volána před všechny koncové body se konfigurují.

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

*Podpora kestrel SNI*

[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu. Pro SNI na funkci klient odešle název hostitele pro zabezpečenou relaci na server během provádění metody handshake TLS server může poskytnout správný certifikát. Klient použije certifikát zařízená šifrovanou komunikaci se serverem během zabezpečené relace, který následuje TLS handshake.

Podporuje SNI přes kestrel `ServerCertificateSelector` zpětného volání. Zpětné volání je vyvolat jednou za připojení, aby mohla aplikace ke kontrole názvu hostitele a vyberte příslušný certifikát.

Podpora SNI vyžaduje:

* Běží na rozhraní .NET framework `netcoreapp2.1`. Na `netcoreapp2.0` a `net461`, zpětné volání vyvolat ale `name` je vždy `null`. `name` Je také `null` Pokud klienta neobsahuje hostitelský název parametru v TLS handshake.
* Spustit všechny weby na stejnou instanci Kestrel. Kestrel nepodporuje sdílení IP adresu a port ve více instancích bez reverzního proxy serveru.

```csharp
WebHost.CreateDefaultBuilder()
    .UseKestrel((context, options) =>
    {
        options.ListenAnyIP(5005, listenOptions =>
        {
            listenOptions.UseHttps(httpsOptions =>
            {
                var localhostCert = CertificateLoader.LoadFromStoreCert(
                    "localhost", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var exampleCert = CertificateLoader.LoadFromStoreCert(
                    "example.com", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var subExampleCert = CertificateLoader.LoadFromStoreCert(
                    "sub.example.com", "My", StoreLocation.CurrentUser, 
                    allowInvalid: true);
                var certs = new Dictionary<string, X509Certificate2>(
                    StringComparer.OrdinalIgnoreCase);
                certs["localhost"] = localhostCert;
                certs["example.com"] = exampleCert;
                certs["sub.example.com"] = subExampleCert;

                httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                {
                    if (name != null && certs.TryGetValue(name, out var cert))
                    {
                        return cert;
                    }

                    return exampleCert;
                };
            });
        });
    });
```

::: moniker-end

**Vytvoření vazby na soket TCP**

[Naslouchání](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listen) metoda vytvoří vazbu na soket TCP a umožňuje lambda s možností konfigurace certifikátu SSL:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Tento příklad konfiguruje SSL pro koncový bod s [ListenOptions](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.listenoptions). Nakonfigurujte další nastavení Kestrel pro konkrétní koncové body pomocí stejného rozhraní API.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

**Vytvoření vazby k Unixovému soketu**

Naslouchání soketu Unix s [ListenUnixSocket](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserveroptions.listenunixsocket) dosáhnout tak vyššího výkonu se serverem Nginx, jak je znázorněno v tomto příkladu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

**Port 0**

Když číslo portu `0` určena Kestrel dynamicky váže dostupný port. Následující příklad ukazuje, jak určit port, který Kestrel vázán skutečně za běhu:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Při spuštění aplikace Určuje výstup okna konzoly dynamický port, kde se dá kontaktovat aplikace:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

**Konfigurační klíč a omezení proměnnou prostředí ASPNETCORE_URLS hostitele UseUrls argument příkazového řádku--adresy URL, adresy URL**

Konfigurace koncových bodů pomocí následujících postupů:

* [UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
* `--urls` argument příkazového řádku
* `urls` Konfigurační klíč hostitele
* `ASPNETCORE_URLS` Proměnná prostředí

Tyto metody jsou užitečné pro provádění kódu práce se servery než Kestrel. Mějte ale na tato omezení:

* Protokol SSL nelze použít s těchto přístupů, pokud výchozí certifikát je součástí konfigurace koncového bodu HTTPS (například pomocí `KestrelServerOptions` konfigurace nebo konfiguračního souboru, jak je znázorněno výše v tomto tématu).
* Při i `Listen` a `UseUrls` přístupy se využívat současně, `Listen` přepsat koncových bodů `UseUrls` koncových bodů.

**Konfigurace koncového bodu služby IIS**

Při použití služby IIS, adresa URL vazeb pro služby IIS změnit vazby jsou nastaveny buď `Listen` nebo `UseUrls`. Další informace najdete v tématu [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) tématu.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

Ve výchozím nastavení, ASP.NET Core váže k `http://localhost:5000`. Konfigurace předpony adres URL a portů pro Kestrel pomocí:

* [UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls?view=aspnetcore-1.1) – metoda rozšíření
* `--urls` argument příkazového řádku
* `urls` Konfigurační klíč hostitele
* ASP.NET Core systém konfigurace, včetně `ASPNETCORE_URLS` proměnné prostředí

Další informace o těchto metodách, naleznete v tématu [Hosting](xref:fundamentals/host/index).

**Konfigurace koncového bodu služby IIS**

Při použití služby IIS, adresa URL vazby pro službu IIS přepsat vazby nastavil `UseUrls`. Další informace najdete v tématu [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) tématu.

---

::: moniker range=">= aspnetcore-2.1"

## <a name="transport-configuration"></a>Konfigurace přenosu

Verze technologie ASP.NET Core 2.1 Kestrel pro výchozí přenos je již podle Libuv ale místo toho podle spravované sokety. Toto je zásadní změny pro aplikace ASP.NET Core 2.0 upgrade na verzi 2.1, které volají [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv) a závisí na jednu z následujících balíčků:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Pro ASP.NET Core 2.1 nebo novější projekty, které používají [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) a vyžadují použití Libuv:

* Přidat závislost [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) balíček do souboru projektu vaší aplikace:

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv" 
                      Version="2.1.0" />
    ```

* Volání [WebHostBuilderLibuvExtensions.UseLibuv](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderlibuvextensions.uselibuv):

    ```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            CreateWebHostBuilder(args).Build().Run();
        }

        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseLibuv()
                .UseStartup<Startup>();
    }
    ```

::: moniker-end

### <a name="url-prefixes"></a>Předpony adres URL

Při použití `UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele, nebo `ASPNETCORE_URLS` proměnné prostředí, předpony adres URL může být v některém z následujících formátů.

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x/)

Platné jsou pouze předpony adres URL protokolu HTTP. Kestrel nepodporuje SSL při konfiguraci adresy URL vazby, které používají `UseUrls`.

* Adresu IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0` je zvláštní případ s vazbou na všechny adresy IPv4.

* Adresa protokolu IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]` je ekvivalentem IPv6 IPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Názvy hostitelů `*`, a `+`, nejsou speciální. Nic není rozpoznán jako platná IP adresa nebo `localhost` vytvoří vazbu pro všechny IP adresy IPv6 a IPv4. Pro vázání názvů jiného hostitele a různé aplikace ASP.NET Core na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je například Apache, IIS nebo Nginx.

  > [!WARNING]
  > Pokud nepoužíváte reverzního proxy serveru s hostitelem filtrování povolené, povolte [hostitele filtrování](#host-filtering).

* Hostitel `localhost` název portu s číslem portu číslo nebo adresu zpětné smyčky IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` určena Kestrel pokusí o připojení k rozhraní zpětné smyčky protokolu IPv4 a IPv6. Pokud požadovaný port je používán jinou službou buď rozhraní zpětné smyčky, Kestrel nepodaří spustit. Pokud je buď rozhraní zpětné smyčky není k dispozici z jiného důvodu (většinu běžně, protože protokol IPv6 není podporován), Kestrel protokoluje upozornění.

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x/)

* Adresu IPv4 s číslem portu

  ```
  http://65.55.39.10:80/
  https://65.55.39.10:443/
  ```

  `0.0.0.0` je zvláštní případ s vazbou na všechny adresy IPv4.

* Adresa protokolu IPv6 s číslem portu

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  https://[0:0:0:0:0:ffff:4137:270a]:443/
  ```

  `[::]` je ekvivalentem IPv6 IPv4 `0.0.0.0`.

* Název hostitele s číslem portu

  ```
  http://contoso.com:80/
  http://*:80/
  https://contoso.com:443/
  https://*:443/
  ```

  Názvy hostitelů `*`, a `+` nejsou speciální. Cokoli, co není rozpoznaný IP adresu nebo `localhost` vytvoří vazbu pro všechny IP adresy IPv6 a IPv4. Pro vázání názvů jiného hostitele a různé aplikace ASP.NET Core na stejném portu, použijte [WebListener](xref:fundamentals/servers/weblistener) nebo reverzní proxy server, jako je například Apache, IIS nebo Nginx.

* Hostitel `localhost` název portu s číslem portu číslo nebo adresu zpětné smyčky IP

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Když `localhost` určena Kestrel pokusí o připojení k rozhraní zpětné smyčky protokolu IPv4 a IPv6. Pokud požadovaný port je používán jinou službou buď rozhraní zpětné smyčky, Kestrel nepodaří spustit. Pokud je buď rozhraní zpětné smyčky není k dispozici z jiného důvodu (většinu běžně, protože protokol IPv6 není podporován), Kestrel protokoluje upozornění.

* Unixovému soketu

  ```
  http://unix:/run/dan-live.sock
  ```

**Port 0**

Pokud je číslo portu `0` určena Kestrel dynamicky váže dostupný port. Vytvoření vazby na port `0` povoleny pro název hostitele nebo IP adresy s výjimkou pro `localhost`.

Při spuštění aplikace Určuje výstup okna konzoly dynamický port, kde se dá kontaktovat aplikace:

```console
Now listening on: http://127.0.0.1:48508
```

**Předpony adres URL pro protokol SSL**

Pokud volání `UseHttps` rozšiřující metoda ji nezapomeňte zahrnout předpony adres URL s `https:`:

```csharp
var host = new WebHostBuilder()
    .UseKestrel(options =>
    {
        options.UseHttps("testCert.pfx", "testPassword");
    })
   .UseUrls("http://localhost:5000", "https://localhost:5001")
   .UseContentRoot(Directory.GetCurrentDirectory())
   .UseStartup<Startup>()
   .Build();
```

> [!NOTE]
> Na stejném portu nemůže být hostovaná protokolů HTTPS a HTTP.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

---

## <a name="host-filtering"></a>Hostitel filtrování

I když Kestrel podporuje konfigurace, například podle předpon `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele. Hostitel `localhost` je zvláštní případ použité pro vazbu na adresu zpětné smyčky adresy. Všechny hostitele, jiné než explicitních IP adresu vytvoří vazbu na všechny veřejné IP adresy. Žádná z těchto informací slouží k ověření požadavku `Host` záhlaví.

::: moniker range="< aspnetcore-2.0"

Alternativním řešením je hostitelem za reverzní proxy server s filtrováním hlavičky hostitele. Toto je jediný podporovaný scénář pro Kestrel v ASP.NET Core 1.x.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Jako alternativní řešení použít k filtrování požadavků podle middlewaru `Host` hlavičky:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Primitives;
using Microsoft.Net.Http.Headers;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

// A normal middleware would provide an options type, config binding, extension methods, etc..
// This intentionally does all of the work inside of the middleware so it can be
// easily copy-pasted into docs and other projects.
public class HostFilteringMiddleware
{
    private readonly RequestDelegate _next;
    private readonly IList<string> _hosts;
    private readonly ILogger<HostFilteringMiddleware> _logger;

    public HostFilteringMiddleware(RequestDelegate next, IConfiguration config, ILogger<HostFilteringMiddleware> logger)
    {
        if (config == null)
        {
            throw new ArgumentNullException(nameof(config));
        }

        _next = next ?? throw new ArgumentNullException(nameof(next));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));

        // A semicolon separated list of host names without the port numbers.
        // IPv6 addresses must use the bounding brackets and be in their normalized form.
        _hosts = config["AllowedHosts"]?.Split(new[] { ';' }, StringSplitOptions.RemoveEmptyEntries);
        if (_hosts == null || _hosts.Count == 0)
        {
            throw new InvalidOperationException("No configuration entry found for AllowedHosts.");
        }
    }

    public Task Invoke(HttpContext context)
    {
        if (!ValidateHost(context))
        {
            context.Response.StatusCode = 400;
            _logger.LogDebug("Request rejected due to incorrect Host header.");
            return Task.CompletedTask;
        }

        return _next(context);
    }

    // This does not duplicate format validations that are expected to be performed by the host.
    private bool ValidateHost(HttpContext context)
    {
        StringSegment host = context.Request.Headers[HeaderNames.Host].ToString().Trim();

        if (StringSegment.IsNullOrEmpty(host))
        {
            // Http/1.0 does not require the Host header.
            // Http/1.1 requires the header but the value may be empty.
            return true;
        }

        // Drop the port

        var colonIndex = host.LastIndexOf(':');

        // IPv6 special case
        if (host.StartsWith("[", StringComparison.Ordinal))
        {
            var endBracketIndex = host.IndexOf(']');
            if (endBracketIndex < 0)
            {
                // Invalid format
                return false;
            }
            if (colonIndex < endBracketIndex)
            {
                // No port, just the IPv6 Host
                colonIndex = -1;
            }
        }

        if (colonIndex > 0)
        {
            host = host.Subsegment(0, colonIndex);
        }

        foreach (var allowedHost in _hosts)
        {
            if (StringSegment.Equals(allowedHost, host, StringComparison.OrdinalIgnoreCase))
            {
                return true;
            }

            // Sub-domain wildcards: *.example.com
            if (allowedHost.StartsWith("*.", StringComparison.Ordinal) && host.Length >= allowedHost.Length)
            {
                // .example.com
                var allowedRoot = new StringSegment(allowedHost, 1, allowedHost.Length - 1);

                var hostRoot = host.Subsegment(host.Length - allowedRoot.Length, allowedRoot.Length);
                if (hostRoot.Equals(allowedRoot, StringComparison.OrdinalIgnoreCase))
                {
                    return true;
                }
            }
        }

        return false;
    }
}
```

Zaregistrujte předchozí `HostFilteringMiddleware` v `Startup.Configure`. Všimněte si, že [řazení zápisu middleware](xref:fundamentals/middleware/index#ordering) je důležité. Registrace se budou objevovat hned po registraci diagnostických Middleware (například `app.UseExceptionHandler`).

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseBrowserLink();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseMiddleware<HostFilteringMiddleware>();

    app.UseMvcWithDefaultRoute();
}
```

Middleware očekává, že `AllowedHosts` klíče v *appsettings.json*/*appsettings.\< EnvironmentName > .json*. Hodnota je středníkem oddělený seznam názvů hostitele bez čísla portů:

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Jako alternativní řešení použijte hostitele filtrování middlewaru. Poskytuje Middleware filtrování hostitele [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) balíček, který je součástí [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo novější). Přidá middleware [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder), který volá [AddHostFiltering](/dotnet/api/microsoft.aspnetcore.builder.hostfilteringservicesextensions.addhostfiltering):

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Middleware filtrování hostitele je ve výchozím nastavení zakázána. Chcete-li povolit middleware, definujte `AllowedHosts` klíče v *appsettings.json*/*appsettings.\< EnvironmentName > .json*. Hodnota je středníkem oddělený seznam názvů hostitele bez čísla portů:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

*appSettings.JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Předané záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také [ForwardedHeadersOptions.AllowedHosts](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions.allowedhosts) možnost. Přesměrovaná záhlaví Middleware a filtrování Middleware hostitele mají podobné funkce pro různé scénáře. Nastavení `AllowedHosts` předané Middleware hlavičky je vhodné při hlavičku hostitele nezachová při předávání žádostí s reverzní proxy server nebo nástroje pro vyrovnávání zatížení. Nastavení `AllowedHosts` s Middlewarem filtrování hostitele je vhodné při Kestrel slouží jako edge server nebo když je hlavička hostitele přímo předán.
>
> Další informace o předávaných Middleware záhlaví, naleznete v tématu [konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer).

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* [Vynucení protokolu HTTPS](xref:security/enforcing-ssl)
* [Kestrel zdrojového kódu](https://github.com/aspnet/KestrelHttpServer)
* [RFC 7230: Syntaxe a směrování zpráv (oddíl 5.4: hostitele)](https://tools.ietf.org/html/rfc7230#section-5.4)
* [Konfigurace ASP.NET Core práci se servery proxy a nástroje pro vyrovnávání zatížení](xref:host-and-deploy/proxy-load-balancer)
