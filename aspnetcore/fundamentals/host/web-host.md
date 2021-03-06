---
title: Webového hostitele ASP.NET Core
author: guardrex
description: Další informace o webového hostitele v ASP.NET Core, který je zodpovědný za spouštění a životního cyklu správy aplikací.
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2018
uid: fundamentals/host/web-host
ms.openlocfilehash: 476795645b0430962b61f7a61de29d5d1819602b
ms.sourcegitcommit: d99a8554c91f626cf5e466911cf504dcbff0e02e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39356711"
---
# <a name="aspnet-core-web-host"></a>Webového hostitele ASP.NET Core

Podle [Luke Latham](https://github.com/guardrex)

Konfigurace aplikace ASP.NET Core a spouštění *hostitele*. Hostitel je zodpovědný za spouštění a životního cyklu správy aplikací. Minimálně hostitele nakonfiguruje server a kanálu zpracování požadavků. Toto téma popisuje webového hostitele ASP.NET Core ([IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)), což je užitečné pro hostování webových aplikací. Pro pokrytí obecný hostitele .NET ([IHostBuilder](/dotnet/api/microsoft.extensions.hosting.ihostbuilder)), najdete v článku <xref:fundamentals/host/generic-host>.

## <a name="set-up-a-host"></a>Nastavení hostitele

::: moniker range=">= aspnetcore-2.0"

Vytvoření hostitele pomocí instance [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). To se obvykle provádí v vstupní bod aplikace, `Main` metody. V šablonách projektů `Main` se nachází v *Program.cs*. Typické *Program.cs* volání [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) spustit nastavení hostitele:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

`CreateDefaultBuilder` provádí následující úlohy:

* Nakonfiguruje [Kestrel](xref:fundamentals/servers/kestrel) webového serveru a nakonfiguruje server pomocí aplikace poskytovatelů konfigurace. Výchozí možnosti Kestrel najdete v tématu <xref:fundamentals/servers/kestrel#kestrel-options>.
* Nastaví obsahu kořenovou cesta vrácená procedurou [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).
* Načtení [konfigurace hostitele](#host-configuration-values) od:
  * Proměnné prostředí s předponou `ASPNETCORE_` (například `ASPNETCORE_ENVIRONMENT`).
  * Argumenty příkazového řádku.
* Konfigurace aplikace načte z:
  * *appsettings.json*.
  * *appsettings.{Environment}.json*.
  * [Tajné klíče uživatelů](xref:security/app-secrets) při spuštění aplikace `Development` prostředí s využitím vstupní sestavení.
  * Proměnné prostředí.
  * Argumenty příkazového řádku.
* Nakonfiguruje [protokolování](xref:fundamentals/logging/index) pro výstup konzoly a ladění. Protokolování zahrnuje [filtrování protokolu](xref:fundamentals/logging/index#log-filtering) pravidel specifikovaných v části Konfigurace protokolování *appsettings.json* nebo *appsettings. { Prostředí} .json* souboru.
* Při spuštění za služby IIS umožňuje [integrace služby IIS](xref:host-and-deploy/iis/index). Nastaví základní cesta a portu server naslouchá na při použití [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module). Modul vytváří reverzní proxy server mezi službou IIS a Kestrel. Také nakonfiguruje aplikaci [zachycení chyb při spuštění](#capture-startup-errors). Výchozí možnosti služby IIS najdete v tématu <xref:host-and-deploy/iis/index#iis-options>.
* Nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) k `true` jestli vývojové prostředí aplikace. Další informace najdete v tématu [oboru ověření](#scope-validation).

Konfigurace určené `CreateDefaultBuilder` můžete přepsat a rozšířen o [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging)a jiné metody a metody rozšíření [ IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder). Následuje několik příkladů:

* [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) slouží k určení dalších `IConfiguration` pro aplikaci. Následující `ConfigureAppConfiguration` volání přidá delegáta a nezahrnují konfiguraci aplikace v *appsettings.xml* souboru. `ConfigureAppConfiguration` může být volána více než jednou. Všimněte si, že tato konfigurace se nevztahuje na hostitele (například adresy URL serveru nebo prostředí). Najdete v článku [hostovat konfigurační hodnoty](#host-configuration-values) oddílu.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* Následující `ConfigureLogging` volání přidá delegáta konfigurace minimální protokolování úrovně ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) k [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel). Toto nastavení přepíše nastavení v *appsettings. Development.JSON* (`LogLevel.Debug`) a *appsettings. Production.JSON* (`LogLevel.Error`) nakonfiguroval `CreateDefaultBuilder`. `ConfigureLogging` může být volána více než jednou.

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

* Následující volání [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) přepíše výchozí [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) 30,000,000 bajtů navázat, když Kestrel nakonfigurovaly `CreateDefaultBuilder`:

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
        ...
    ```

*Obsahu kořenové* Určuje, kde hostitele vyhledává soubory obsahu, jako jsou například soubory zobrazení MVC. Při spuštění aplikace z kořenové složky projektu, kořenové složky projektu slouží jako kořenový adresář obsahu. Toto je výchozí hodnotu použitou v [sady Visual Studio](https://www.visualstudio.com/) a [nové šablony pro dotnet](/dotnet/core/tools/dotnet-new).

Další informace o konfiguraci aplikací najdete v tématu <xref:fundamentals/configuration/index>.

> [!NOTE]
> Jako alternativu k použití statické `CreateDefaultBuilder` metoda vytvoření hostitele z [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) je podporované přístup pomocí ASP.NET Core 2.x. Další informace najdete v tématu na kartě ASP.NET Core 1.x.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Vytvoření hostitele pomocí instance [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder). Vytvoření hostitele se obvykle provádí v vstupní bod aplikace, `Main` metody. V šablonách projektů `Main` se nachází v *Program.cs*:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        BuildWebHost(args).Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .Build();
}
```

`WebHostBuilder` vyžaduje [serveru, který implementuje IServer](xref:fundamentals/servers/index). Integrované servery jsou [Kestrel](xref:fundamentals/servers/kestrel) a [HTTP.sys](xref:fundamentals/servers/httpsys) (před verzí technologie ASP.NET Core 2.0, byla volána HTTP.sys [WebListener](xref:fundamentals/servers/weblistener)). V tomto příkladu [UseKestrel rozšiřující metoda](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) určuje Kestrel server.

*Obsahu kořenové* Určuje, kde hostitele vyhledává soubory obsahu, jako jsou například soubory zobrazení MVC. Výchozí kořen obsahu je získána pro `UseContentRoot` podle [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory?view=netcore-1.1). Při spuštění aplikace z kořenové složky projektu, kořenové složky projektu slouží jako kořenový adresář obsahu. Toto je výchozí hodnotu použitou v [sady Visual Studio](https://www.visualstudio.com/) a [nové šablony pro dotnet](/dotnet/core/tools/dotnet-new).

Chcete-li použít jako reverzní proxy server služby IIS, zavolejte [UseIISIntegration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderiisextensions) jako součást vytváření hostitele. `UseIISIntegration` neprovede konfiguraci *server*, třeba [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel?view=aspnetcore-1.1) nepodporuje. `UseIISIntegration` Nastaví základní cesta a portu server naslouchá na při použití [modul ASP.NET Core](xref:fundamentals/servers/aspnet-core-module) vytvořit reverzního proxy serveru mezi Kestrel a služby IIS. Použití služby IIS s ASP.NET Core, `UseKestrel` a `UseIISIntegration` musí být zadán. `UseIISIntegration` aktivuje pouze při spuštění za služby IIS nebo IIS Express. Další informace naleznete v tématu <xref:fundamentals/servers/aspnet-core-module> a <xref:host-and-deploy/aspnet-core-module>.

Minimální implementaci, který konfiguruje hostitele (a aplikace ASP.NET Core) součástí serveru a na konfiguraci aplikace požadavku kanálu:

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .Configure(app =>
    {
        app.Run(context => context.Response.WriteAsync("Hello World!"));
    })
    .Build();

host.Run();
```

::: moniker-end

Při nastavování hostitele, [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure?view=aspnetcore-1.1) a [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices?view=aspnetcore-1.1) metody lze zadat. Pokud `Startup` Zadaná třída, musíte definovat `Configure` metoda. Další informace naleznete v tématu <xref:fundamentals/startup>. Více volání `ConfigureServices` připojit k sobě navzájem. Více volání `Configure` nebo `UseStartup` na `WebHostBuilder` nahradit předchozí nastavení.

## <a name="host-configuration-values"></a>Hodnoty konfigurace hostitele

[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) závisí na následujících dvou přístupů k nastavení hodnoty konfigurace hostitele:

* Tvůrce konfiguraci hostitele, která zahrnuje proměnné prostředí s formátem `ASPNETCORE_{configurationKey}`. Například `ASPNETCORE_ENVIRONMENT`.
* Rozšíření, jako [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) a [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (viz [přepsání konfigurace](#override-configuration) části).
* [UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) a přidružený klíč. Při nastavování hodnoty s `UseSetting`, je hodnota nastavena jako řetězec bez ohledu na typ.

Hostitel používá kterékoli z těchto možností nastaví hodnotu poslední. Další informace najdete v tématu [konfigurace přepisování](#override-configuration) v další části.

### <a name="application-key-name"></a>Klíč aplikace (název)

[IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) vlastnost je nastavena automaticky při [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) nebo [konfigurovat](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) je volána v průběhu vytváření hostitele. Je hodnota nastavena na název sestavení obsahující vstupní bod aplikace. Pokud chcete explicitně nastavit hodnotu, použijte [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):

**Klíč**: applicationName  
**Typ**: *řetězec*  
**Výchozí**: název sestavení obsahující vstupní bod aplikace.  
**Sada s použitím**: `UseSetting`  
**Proměnná prostředí**: `ASPNETCORE_APPLICATIONKEY`

::: moniker range=">= aspnetcore-2.1"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```csharp
var host = new WebHostBuilder()
    .UseSetting("applicationName", "CustomApplicationName")
```

::: moniker-end

### <a name="capture-startup-errors"></a>Zachycení chyb při spuštění

Toto nastavení řídí zaznamenávání chyb při spuštění.

**Klíč**: captureStartupErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: výchozí hodnota je `false` Pokud aplikace běží s Kestrel za služby IIS, kde je jako výchozí `true`.  
**Sada s použitím**: `CaptureStartupErrors`  
**Proměnná prostředí**: `ASPNETCORE_CAPTURESTARTUPERRORS`

Když `false`, chyby během spuštění výsledku na hostiteli. operace bude ukončena. Když `true`, zachytí výjimky během spouštění hostitele a se pokusí spustit na serveru.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .CaptureStartupErrors(true)
```

::: moniker-end

### <a name="content-root"></a>Obsah kořenové

Toto nastavení určuje, kde ASP.NET Core začne vyhledávat soubory obsahu, například zobrazení MVC. 

**Klíč**: contentRoot  
**Typ**: *řetězec*  
**Výchozí**: výchozí hodnota je složka, ve které se nachází sestavení aplikace.  
**Sada s použitím**: `UseContentRoot`  
**Proměnná prostředí**: `ASPNETCORE_CONTENTROOT`

Obsah kořenové slouží také jako základní cesta pro [kořenový adresář webové nastavení](#web-root). Pokud cesta neexistuje, hostitel se nepodaří spustit.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseContentRoot("c:\\<content-root>")
```

::: moniker-end

### <a name="detailed-errors"></a>Podrobné chyby

Určuje, zda podrobné chyby mají být zaznamenány.

**Klíč**: detailedErrors  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Sada s použitím**: `UseSetting`  
**Proměnná prostředí**: `ASPNETCORE_DETAILEDERRORS`

Při povolené (nebo když <a href="#environment">prostředí</a> je nastavena na `Development`), aplikace zaznamenává podrobné výjimky.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

::: moniker-end

### <a name="environment"></a>Prostředí

Nastaví prostředí aplikace.

**Klíč**: prostředí  
**Typ**: *řetězec*  
**Výchozí**: produkčního prostředí  
**Sada s použitím**: `UseEnvironment`  
**Proměnná prostředí**: `ASPNETCORE_ENVIRONMENT`

Prostředí můžete nastavit na libovolnou hodnotu. Hodnoty definované v rámci rozhraní zahrnují `Development`, `Staging`, a `Production`. Hodnoty se velká a malá písmena. Ve výchozím nastavení *prostředí* číst z `ASPNETCORE_ENVIRONMENT` proměnné prostředí. Při použití [sady Visual Studio](https://www.visualstudio.com/), lze nastavit proměnné prostředí *launchSettings.json* souboru. Další informace naleznete v tématu <xref:fundamentals/environments>.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseEnvironment(EnvironmentName.Development)
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

### <a name="hosting-startup-assemblies"></a>Hostování při spuštění sestavení

Nastaví hostování sestavení po spuštění aplikace.

**Klíč**: hostingStartupAssemblies  
**Typ**: *řetězec*  
**Výchozí**: prázdný řetězec  
**Sada s použitím**: `UseSetting`  
**Proměnná prostředí**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`

Středníkem oddělený řetězec hostování načíst při spuštění po spuštění sestavení.

I když výchozí hodnota konfigurace je prázdný řetězec, hostování při spuštění sestavení vždy zahrnovat sestavení aplikace. Při hostování při spuštění sestavení jsou k dispozici, se přidají do sestavení aplikace pro načtení, když aplikace je sestavena své běžné služby během spouštění.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

### <a name="https-port"></a>HTTPS Port

Nastavení přesměrování portu HTTPS. Použít v [vynucování HTTPS](xref:security/enforcing-ssl).

**Klíč**: https_port **typ**: *řetězec*
**výchozí**: výchozí hodnota není nastavená.
**Sada s použitím**: `UseSetting` 
 **proměnnou prostředí**: `ASPNETCORE_HTTPS_PORT`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

### <a name="prefer-hosting-urls"></a>Dáváte přednost, který je hostitelem adresy URL

Určuje, zda hostitel naslouchat požadavkům na adresy URL nakonfigurované `WebHostBuilder` místo nastavení nakonfigurovaného pomocí `IServer` implementace.

**Klíč**: preferHostingUrls  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: true  
**Sada s použitím**: `PreferHostingUrls`  
**Proměnná prostředí**: `ASPNETCORE_PREFERHOSTINGURLS`

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

### <a name="prevent-hosting-startup"></a>Zabránit spuštění hostování

Brání automatické načítání hostování při spuštění sestavení, včetně hostování při spuštění sestavení nakonfiguroval sestavení aplikace. Další informace naleznete v tématu <xref:fundamentals/configuration/platform-specific-configuration>.

**Klíč**: preventHostingStartup  
**Typ**: *bool* (`true` nebo `1`)  
**Výchozí**: false  
**Sada s použitím**: `UseSetting`  
**Proměnná prostředí**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

::: moniker-end

### <a name="server-urls"></a>Serverové adresy URL

Určuje IP adresy nebo adresy hostitele s porty a protokoly, které server naslouchat požadavkům na požadavky.

**Klíč**: adresy URL  
**Typ**: *řetězec*  
**Výchozí**: http://localhost:5000  
**Sada s použitím**: `UseUrls`  
**Proměnná prostředí**: `ASPNETCORE_URLS`

Nastavte na oddělený středníkem (;) na server by měl odpovídat předpony adres seznam adresy URL. Například `http://localhost:123`. Použití "\*" k označení, že by měl server naslouchat požadavkům na IP adresu nebo název hostitele zadaný port a protokol (například `http://*:5000`). Protokol (`http://` nebo `https://`) musí být součástí jednotlivé adresy URL. Podporované formáty lišit mezi servery.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

Kestrel má svůj vlastní konfigurace koncového bodu rozhraní API. Další informace naleznete v tématu <xref:fundamentals/servers/kestrel#endpoint-configuration>.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

### <a name="shutdown-timeout"></a>Časový limit vypnutí

Určuje dobu čekání webového hostitele pro vypnutí.

**Klíč**: shutdownTimeoutSeconds  
**Typ**: *int*  
**Výchozí**: 5  
**Sada s použitím**: `UseShutdownTimeout`  
**Proměnná prostředí**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`

I když se přijme klíč *int* s `UseSetting` (například `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) rozšiřující metoda přijímá [časový interval](/dotnet/api/system.timespan).

Během časového limitu, který je hostitelem:

* Aktivační události [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).
* Pokusy o zastavení hostovaných služeb, protokolování všech chyb pro služby, které se nepodařilo zastavit.

Pokud časový limit vyprší před všechny zarážky hostovaných služeb, zastaví se všechny zbývající služby active při ukončení aplikace. Zastavení služeb i v případě, že se nedokončilo zpracování. Pokud služby vyžadují další čas ukončení, zvýšit časový limit.

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

::: moniker-end

### <a name="startup-assembly"></a>Po spuštění sestavení

Určuje sestavení, které chcete vyhledat `Startup` třídy.

**Klíč**: startupAssembly  
**Typ**: *řetězec*  
**Výchozí**: sestavení aplikace  
**Sada s použitím**: `UseStartup`  
**Proměnná prostředí**: `ASPNETCORE_STARTUPASSEMBLY`

Sestavení podle názvu (`string`) nebo typ (`TStartup`) může být odkazováno. Pokud je položek víc `UseStartup` metody jsou volány, má přednost před poslední z nich.

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseStartup("StartupAssemblyName")
```

```csharp
var host = new WebHostBuilder()
    .UseStartup<TStartup>()
```

::: moniker-end

### <a name="web-root"></a>Kořenový adresář webové

Nastaví relativní cestu k statických prostředků aplikace.

**Klíč**: webroot  
**Typ**: *řetězec*  
**Výchozí**: Pokud se nezadá, výchozí hodnota je "(Content Root)/wwwroot", pokud cesta existuje. Pokud cesta neexistuje, no-op soubor zprostředkovatele slouží.  
**Sada s použitím**: `UseWebRoot`  
**Proměnná prostředí**: `ASPNETCORE_WEBROOT`

::: moniker range=">= aspnetcore-2.0"

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
var host = new WebHostBuilder()
    .UseWebRoot("public")
```

::: moniker-end

## <a name="override-configuration"></a>Přepsání konfigurace

Použití [konfigurace](xref:fundamentals/configuration/index) ke konfiguraci webového hostitele. V následujícím příkladu je konfigurace hostitele volitelně podle *hostsettings.json* souboru. Všechny konfigurace načtena z *hostsettings.json* soubor lze přepsat pomocí argumentů příkazového řádku. Konfigurace sestavení (v `config`) se používá ke konfiguraci hostitele s [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration). `IWebHostBuilder` konfigurace je přidaný na konfiguraci aplikace, ale neplatí první&mdash; `ConfigureAppConfiguration` nemá vliv `IWebHostBuilder` konfigurace.

::: moniker range=">= aspnetcore-2.0"

Přepsání konfigurace poskytované `UseUrls` s *hostsettings.json* config argument příkazového řádku, první config druhý:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();
    }
}
```

*hostsettings.JSON*:

```json
{
    urls: "http://*:5005"
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Přepsání konfigurace poskytované `UseUrls` s *hostsettings.json* config argument příkazového řádku, první config druhý:

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        var host = new WebHostBuilder()
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .UseKestrel()
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            })
            .Build();

        host.Run();
    }
}
```

*hostsettings.JSON*:

```json
{
    urls: "http://*:5005"
}
```

::: moniker-end

> [!NOTE]
> [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) rozšiřující metoda není aktuálně podporuje při analýze oddílu konfigurace vrácený `GetSection` (například `.UseConfiguration(Configuration.GetSection("section"))`. `GetSection` Metoda filtry konfigurační klíče do části Požadovaná, ale ponechá název oddílu, který na klíče (například `section:urls`, `section:environment`). `UseConfiguration` Metoda očekává klíčů tak, aby odpovídaly `WebHostBuilder` klíče (například `urls`, `environment`). Přítomnost název oddílu, který klíčů brání hodnoty v části Konfigurace hostitele. Tento problém bude vyřešen v příští verzi. Další informace a řešení najdete v tématu [předávání konfigurační oddíl do WebHostBuilder.UseConfiguration využívá úplnou klíčů](https://github.com/aspnet/Hosting/issues/839).
>
> `UseConfiguration` zkopíruje jen klíče ze zadaného `IConfiguration` Tvůrce konfiguraci hostitele. Proto nastavení `reloadOnChange: true` soubory XML, JSON a INI nastavení nemá žádný vliv.

K určení hostitele spouštět na určitou adresu URL, na požadovanou hodnotu lze předat v příkazovém řádku při provádění [dotnet spustit](/dotnet/core/tools/dotnet-run). Argument příkazového řádku přepíše `urls` hodnotu *hostsettings.json* souboru a server naslouchá na portu 8080:

```console
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a>Spravovat hostitele

::: moniker range=">= aspnetcore-2.0"

**Spuštění**

`Run` Metoda spustí webovou aplikaci a blokuje volající vlákno, dokud nebude ukončen hostitele:

```csharp
host.Run();
```

**Start**

Spustí hostitele způsobem neblokující zavoláním jeho `Start` metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Pokud je předán seznam adres URL `Start` metody naslouchá na zadaných adresách URL:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

Aplikaci můžete inicializaci a spuštění nového hostitele pomocí předem nakonfigurovaných výchozích nastavení `CreateDefaultBuilder` pomocí pohodlí statické metody. Tyto metody spuštění serveru bez výstup na konzole a s [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) počkejte přerušení (Ctrl-C/SIGINT nebo SIGTERM):

**Spuštění (RequestDelegate aplikace)**

Začněte `RequestDelegate`:

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Vytvořit žádost o v prohlížeči `http://localhost:5000` trvá příjem odpovědi "Hello World!" `WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.

**Spustit (řetězec adresu url, RequestDelegate aplikace)**

Začněte s adresou URL a `RequestDelegate`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **Start (RequestDelegate aplikace)**, s výjimkou aplikace reaguje na `http://localhost:8080`.

**Spustit (akce&lt;IRouteBuilder&gt; routeBuilder)**

Použijte instanci `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) používat middleware směrování:

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

Použijte následující požadavky na prohlížeč s příkladem:

| Požadavek                                    | Odpověď                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | Dobrý den, Martin!                           |
| `http://localhost:5000/buenosdias/Catrina` | Buenos dias, Catrina!                    |
| `http://localhost:5000/throw/ooops!`       | Vyvolá výjimku s řetězcem "ooops!" |
| `http://localhost:5000/throw`              | Vyvolá výjimku s řetězcem "Uh ale!" |
| `http://localhost:5000/Sante/Kevin`        | Santé, Kevin!                            |
| `http://localhost:5000`                    | Ahoj světe!                             |

`WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.

**Spustit (řetězec adresy url, akce&lt;IRouteBuilder&gt; routeBuilder)**

Použijte adresu URL a instance `IRouteBuilder`:

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **spuštění (akce&lt;IRouteBuilder&gt; routeBuilder)**, s výjimkou aplikace reaguje na `http://localhost:8080`.

**StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**

Zadejte delegát pro konfiguraci `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Vytvořit žádost o v prohlížeči `http://localhost:5000` trvá příjem odpovědi "Hello World!" `WaitForShutdown` blokuje, dokud se vydává přerušení (Ctrl-C/SIGINT nebo SIGTERM). Aplikace zobrazí `Console.WriteLine` zprávu a čeká na stisknutí klávesy ukončete.

**StartWith (řetězec adresy url, akce&lt;IApplicationBuilder&gt; aplikace)**

Zadejte adresu URL a delegát pro konfiguraci `IApplicationBuilder`:

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

Vytvoří stejný výsledek jako **StartWith (akce&lt;IApplicationBuilder&gt; aplikace)**, s výjimkou aplikace reaguje na `http://localhost:8080`.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

**Spuštění**

`Run` Metoda spustí webovou aplikaci a blokuje volající vlákno, dokud nebude ukončen hostitele:

```csharp
host.Run();
```

**Start**

Spustí hostitele způsobem neblokující zavoláním jeho `Start` metody:

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

Pokud je předán seznam adres URL `Start` metody naslouchá na zadaných adresách URL:

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

::: moniker-end

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment rozhraní

[IHostingEnvironment rozhraní](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) poskytuje informace o hostování prostředí webové aplikace. Použít [konstruktor vkládání](xref:fundamentals/dependency-injection) získat `IHostingEnvironment` Chcete-li použít její vlastnosti a metody rozšíření:

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

A [přístup podle úmluvy](xref:fundamentals/environments#environment-based-startup-class-and-methods) slouží ke konfiguraci aplikace při spuštění na základě prostředí. Můžete také vložit `IHostingEnvironment` do `Startup` konstruktoru pro použití v `ConfigureServices`:

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> Kromě `IsDevelopment` rozšiřující metoda `IHostingEnvironment` nabízí `IsStaging`, `IsProduction`, a `IsEnvironment(string environmentName)` metody. Další informace naleznete v tématu <xref:fundamentals/environments>.

`IHostingEnvironment` Service můžete také vloženy přímo do `Configure` metodu pro vytvoření kanálu zpracování:

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the developer exception page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

`IHostingEnvironment` mohou být vloženy do `Invoke` metoda při vytváření vlastní [middleware](xref:fundamentals/middleware/index#writing-middleware):

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime rozhraní

[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) umožňuje po spuštění a vypnutí aktivity. Tři vlastnosti v rozhraní jsou tokeny zrušení použije k registraci `Action` metody, které definují události spuštění a vypnutí.

| Token zrušení    | Při aktivaci&#8230; |
| --------------------- | --------------------- |
| [ApplicationStarted](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | Hostitel plně byla spuštěna. |
| [ApplicationStopped](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | Hostitel se dokončuje řádné vypnutí. By měl zpracovat všechny požadavky. Vypnutí blokuje, dokud se tato událost se dokončí. |
| [ApplicationStopping](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | Hostitel provádí řádné vypnutí. Žádosti se možná ještě zpracovávají. Vypnutí blokuje, dokud se tato událost se dokončí. |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) požaduje ukončení aplikace. Následující třídy používá `StopApplication` řádně vypnout aplikaci při třídy `Shutdown` volání metody:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="scope-validation"></a>Rozsah ověřování

::: moniker range=">= aspnetcore-2.0"

[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) nastaví [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) k `true` jestli vývojové prostředí aplikace.

::: moniker-end

Když `ValidateScopes` je nastavena na `true`, poskytovatele služeb výchozí provádí kontroly pro ověření, že:

* Vymezené služby nejsou přímo nebo nepřímo vyřešit z kořenové poskytovatele služeb.
* Vymezené služby nejsou přímo nebo nepřímo vloženy do jednotlivých prvků.

Poskytovatel služeb root je vytvořen, když [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) je volána. Doba života poskytovatele služeb kořenový odpovídá životnost aplikace/serveru při zprostředkovatel začíná aplikace a je uvolněna při ukončení aplikace.

Vymezené služby jsou uvolněna pomocí kontejneru, který je vytvořil. Pokud vymezené služby se vytvoří v kořenovém kontejneru, životnost služby je efektivně povýšen na jednotlivý prvek, protože to je jenom uvolněn pomocí Kořenový kontejner při vypnutí serveru/aplikace. Ověřování služby Obory zachytí tyto situace při `BuildServiceProvider` je volána.

Vždy ověřovaly obory, včetně produkčního prostředí, nakonfigurujte [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) s [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) na tvůrce hostitele:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

::: moniker range="= aspnetcore-2.0"

## <a name="troubleshooting-systemargumentexception"></a>Řešení potíží s System.ArgumentException

**Toto platí jenom pro aplikace ASP.NET Core 2.0 při aplikaci nevolá `UseStartup` nebo `Configure`.**

Hostitel může být sestavena v vkládá `IStartup` přímo do kontejneru pro vkládání závislostí místo volání `UseStartup` nebo `Configure`:

```csharp
services.AddSingleton<IStartup, Startup>();
```

Pokud hostitel je vytvořena tímto způsobem, může dojít k následující chybě:

```
Unhandled Exception: System.ArgumentException: A valid non-empty application name must be provided.
```

K tomu dochází, název aplikace (název aktuální sestavení) je nutná ke kontrole pro `HostingStartupAttributes`. Pokud aplikaci ručně vkládá `IStartup` do kontejneru pro vkládání závislostí, přidejte následující volání `WebHostBuilder` pomocí zadaného názvu sestavení:

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("applicationName", "AssemblyName")
```

Můžete také přidat fiktivní `Configure` k `WebHostBuilder`, který automaticky nastaví název aplikace:

```csharp
WebHost.CreateDefaultBuilder(args)
    .Configure(_ => { })
```

Další informace najdete v tématu [oznámení: Microsoft.Extensions.PlatformAbstractions byla odebrána (komentář)](https://github.com/aspnet/Announcements/issues/237#issuecomment-323786938) a [StartupInjection ukázka](https://github.com/aspnet/Hosting/blob/8377d226f1e6e1a97dabdb6769a845eeccc829ed/samples/SampleStartups/StartupInjection.cs).

::: moniker-end

## <a name="additional-resources"></a>Další zdroje

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
