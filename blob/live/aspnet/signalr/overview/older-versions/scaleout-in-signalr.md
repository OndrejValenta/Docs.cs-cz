---
uid: signalr/overview/older-versions/scaleout-in-signalr
title: "Úvod do škálování v systému SignalR 1.x | Microsoft Docs"
author: MikeWasson
description: 
ms.author: aspnetcontent
manager: wpickett
ms.date: 04/29/2013
ms.topic: article
ms.assetid: 3fd9f11c-799b-4001-bd60-1e70cfc61c19
ms.technology: dotnet-signalr
ms.prod: .net-framework
msc.legacyurl: /signalr/overview/older-versions/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: e6230d4d65adb8c9a064545ad761898ca53562bf
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="introduction-to-scaleout-in-signalr-1x"></a><span data-ttu-id="4e267-102">Úvod do škálování v systému SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="4e267-102">Introduction to Scaleout in SignalR 1.x</span></span>
====================
<span data-ttu-id="4e267-103">podle [Karel Wasson](https://github.com/MikeWasson), [Patrik Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="4e267-103">by [Mike Wasson](https://github.com/MikeWasson), [Patrick Fletcher](https://github.com/pfletcher)</span></span>

<span data-ttu-id="4e267-104">Obecně platí, existují dva způsoby škálování webové aplikace: *škálovat* a *škálovat*.</span><span class="sxs-lookup"><span data-stu-id="4e267-104">In general, there are two ways to scale a web application: *scale up* and *scale out*.</span></span>

- <span data-ttu-id="4e267-105">Škálování znamená větší serveru (nebo na větší virtuální počítač) pomocí více paměti RAM, procesory atd.</span><span class="sxs-lookup"><span data-stu-id="4e267-105">Scale up means using a larger server (or a larger VM) with more RAM, CPUs, etc.</span></span>
- <span data-ttu-id="4e267-106">Horizontální navýšení kapacity znamená přidávat další servery pro zpracování zátěže.</span><span class="sxs-lookup"><span data-stu-id="4e267-106">Scale out means adding more servers to handle the load.</span></span>

<span data-ttu-id="4e267-107">Problém s vertikálním navýšení kapacity je rychle dosáhl limitu velikost počítače.</span><span class="sxs-lookup"><span data-stu-id="4e267-107">The problem with scaling up is that you quickly hit a limit on the size of the machine.</span></span> <span data-ttu-id="4e267-108">Kromě toho budete muset škálovat. Však při škálovat, klienti mohou získat směrovány na jiné servery.</span><span class="sxs-lookup"><span data-stu-id="4e267-108">Beyond that, you need to scale out. However, when you scale out, clients can get routed to different servers.</span></span> <span data-ttu-id="4e267-109">Klient, který je připojený k jednomu serveru nebude příjem zpráv odeslaných z jiného serveru.</span><span class="sxs-lookup"><span data-stu-id="4e267-109">A client that is connected to one server will not receive messages sent from another server.</span></span>

![](scaleout-in-signalr/_static/image1.png)

<span data-ttu-id="4e267-110">Jedním z řešení je k předávání zpráv mezi servery pomocí komponenty s názvem *propojovacího rozhraní*.</span><span class="sxs-lookup"><span data-stu-id="4e267-110">One solution is to forward messages between servers, using a component called a *backplane*.</span></span> <span data-ttu-id="4e267-111">S propojovacího rozhraní povoleno každá instance aplikace odesílá zprávy do propojovacího rozhraní a předává je propojovacího rozhraní dalších instancí aplikace.</span><span class="sxs-lookup"><span data-stu-id="4e267-111">With a backplane enabled, each application instance sends messages to the backplane, and the backplane forwards them to the other application instances.</span></span> <span data-ttu-id="4e267-112">(V elektronické součástky, propojovací rozhraní je skupina paralelní konektorů.</span><span class="sxs-lookup"><span data-stu-id="4e267-112">(In electronics, a backplane is a group of parallel connectors.</span></span> <span data-ttu-id="4e267-113">Obdobně propojovací rozhraní SignalR připojí více serverů.)</span><span class="sxs-lookup"><span data-stu-id="4e267-113">By analogy, a SignalR backplane connects multiple servers.)</span></span>

![](scaleout-in-signalr/_static/image2.png)

<span data-ttu-id="4e267-114">Funkce SignalR aktuálně poskytuje tři backplanes:</span><span class="sxs-lookup"><span data-stu-id="4e267-114">SignalR currently provides three backplanes:</span></span>

- <span data-ttu-id="4e267-115">**Azure Service Bus**.</span><span class="sxs-lookup"><span data-stu-id="4e267-115">**Azure Service Bus**.</span></span> <span data-ttu-id="4e267-116">Service Bus je zasílání zpráv infrastrukturu, která umožňuje součásti odesílat zprávy volně párované způsobem.</span><span class="sxs-lookup"><span data-stu-id="4e267-116">Service Bus is a messaging infrastructure that allows components to send messages in a loosely coupled way.</span></span>
- <span data-ttu-id="4e267-117">**Redis**.</span><span class="sxs-lookup"><span data-stu-id="4e267-117">**Redis**.</span></span> <span data-ttu-id="4e267-118">Redis je úložišti klíč hodnota v paměti.</span><span class="sxs-lookup"><span data-stu-id="4e267-118">Redis is an in-memory key-value store.</span></span> <span data-ttu-id="4e267-119">Redis podporuje vzor publikovat/odebírat ("pub nebo sub") pro odesílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="4e267-119">Redis supports a publish/subscribe ("pub/sub") pattern for sending messages.</span></span>
- <span data-ttu-id="4e267-120">**Systému SQL Server**.</span><span class="sxs-lookup"><span data-stu-id="4e267-120">**SQL Server**.</span></span> <span data-ttu-id="4e267-121">Propojovací rozhraní systému SQL Server zapíše zprávy do tabulky SQL.</span><span class="sxs-lookup"><span data-stu-id="4e267-121">The SQL Server backplane writes messages to SQL tables.</span></span> <span data-ttu-id="4e267-122">Propojovacího rozhraní používá službu Service Broker pro efektivní zasílání zpráv.</span><span class="sxs-lookup"><span data-stu-id="4e267-122">The backplane uses Service Broker for efficient messaging.</span></span> <span data-ttu-id="4e267-123">Ale spolupracuje také pokud Service Broker není povolena.</span><span class="sxs-lookup"><span data-stu-id="4e267-123">However, it also works if Service Broker is not enabled.</span></span>

<span data-ttu-id="4e267-124">Pokud nasazujete aplikaci na platformě Azure, zvažte použití propojovacího rozhraní Azure Service Bus.</span><span class="sxs-lookup"><span data-stu-id="4e267-124">If you deploy your application on Azure, consider using the Azure Service Bus backplane.</span></span> <span data-ttu-id="4e267-125">Pokud nasazujete do serverové farmy, zvažte systému SQL Server nebo Redis backplanes.</span><span class="sxs-lookup"><span data-stu-id="4e267-125">If you are deploying to your own server farm, consider the SQL Server or Redis backplanes.</span></span>

<span data-ttu-id="4e267-126">Následující témata obsahují podrobné kurzy pro každý propojovacího rozhraní:</span><span class="sxs-lookup"><span data-stu-id="4e267-126">The following topics contain step-by-step tutorials for each backplane:</span></span>

- [<span data-ttu-id="4e267-127">Škálování SignalR službou Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="4e267-127">SignalR Scaleout with Azure Service Bus</span></span>](scaleout-with-windows-azure-service-bus.md)
- [<span data-ttu-id="4e267-128">Škálování SignalR s Redisem</span><span class="sxs-lookup"><span data-stu-id="4e267-128">SignalR Scaleout with Redis</span></span>](scaleout-with-redis.md)
- [<span data-ttu-id="4e267-129">Škálování SignalR s SQL serverem</span><span class="sxs-lookup"><span data-stu-id="4e267-129">SignalR Scaleout with SQL Server</span></span>](scaleout-with-sql-server.md)

## <a name="implementation"></a><span data-ttu-id="4e267-130">Implementace</span><span class="sxs-lookup"><span data-stu-id="4e267-130">Implementation</span></span>

<span data-ttu-id="4e267-131">V systému SignalR každá zpráva se budou odesílat prostřednictvím sběrnice zpráv.</span><span class="sxs-lookup"><span data-stu-id="4e267-131">In SignalR, every message is sent through a message bus.</span></span> <span data-ttu-id="4e267-132">Implementuje sběrnice zpráv [IMessageBus](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) rozhraní, která poskytuje abstrakci se publikování a přihlášení k odběru.</span><span class="sxs-lookup"><span data-stu-id="4e267-132">A message bus implements the [IMessageBus](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx) interface, which provides a publish/subscribe abstraction.</span></span> <span data-ttu-id="4e267-133">Backplanes fungovat tak, že nahradíte výchozí **IMessageBus** se sběrnicí určené pro tento propojovacího rozhraní.</span><span class="sxs-lookup"><span data-stu-id="4e267-133">The backplanes work by replacing the default **IMessageBus** with a bus designed for that backplane.</span></span> <span data-ttu-id="4e267-134">Je například sběrnici zpráv pro Redis [RedisMessageBus](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx), a používá Redis [pub nebo sub](http://redis.io/topics/pubsub) mechanismus odesílat a přijímat zprávy.</span><span class="sxs-lookup"><span data-stu-id="4e267-134">For example, the message bus for Redis is [RedisMessageBus](https://msdn.microsoft.com/en-us/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx), and it uses the Redis [pub/sub](http://redis.io/topics/pubsub) mechanism to send and receive messages.</span></span>

<span data-ttu-id="4e267-135">Každá instance serveru se připojí k propojovacího rozhraní přes sběrnici.</span><span class="sxs-lookup"><span data-stu-id="4e267-135">Each server instance connects to the backplane through the bus.</span></span> <span data-ttu-id="4e267-136">Pokud je odeslána zpráva, přejdete do propojovacího rozhraní a propojovacího rozhraní odešle ji do každého serveru.</span><span class="sxs-lookup"><span data-stu-id="4e267-136">When a message is sent, it goes to the backplane, and the backplane sends it to every server.</span></span> <span data-ttu-id="4e267-137">Když server získá zprávu z propojovacího rozhraní, uloží zprávu v místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="4e267-137">When a server gets a message from the backplane, it puts the message in its local cache.</span></span> <span data-ttu-id="4e267-138">Server pak přináší zprávy pro klienty z místní mezipaměti.</span><span class="sxs-lookup"><span data-stu-id="4e267-138">The server then delivers messages to clients from its local cache.</span></span>

<span data-ttu-id="4e267-139">Pro každé připojení klienta je sledovat průběh klienta při čtení datový proud zpráv pomocí kurzoru.</span><span class="sxs-lookup"><span data-stu-id="4e267-139">For each client connection, the client's progress in reading the message stream is tracked using a cursor.</span></span> <span data-ttu-id="4e267-140">(Kurzoru představuje pozici v datovém proudu zpráv.) Pokud se klient odpojí a potom se znovu připojí, zobrazí dotaz, sběrnici pro všechny zprávy, které byly přijaty po hodnotu kurzoru klienta.</span><span class="sxs-lookup"><span data-stu-id="4e267-140">(A cursor represents a position in the message stream.) If a client disconnects and then reconnects, it asks the bus for any messages that arrived after the client's cursor value.</span></span> <span data-ttu-id="4e267-141">Samé se stane, když připojení používá [dlouhé dotazování](../getting-started/introduction-to-signalr.md#transports).</span><span class="sxs-lookup"><span data-stu-id="4e267-141">The same thing happens when a connection uses [long polling](../getting-started/introduction-to-signalr.md#transports).</span></span> <span data-ttu-id="4e267-142">Po dokončení žádosti dlouhé dotazování klienta otevře nové připojení a požádá o zprávy, které byly přijaty po kurzor.</span><span class="sxs-lookup"><span data-stu-id="4e267-142">After a long poll request completes, the client opens a new connection and asks for messages that arrived after the cursor.</span></span>

<span data-ttu-id="4e267-143">Funguje mechanismus kurzoru i v případě, že klient se směruje na jiný server na znovu.</span><span class="sxs-lookup"><span data-stu-id="4e267-143">The cursor mechanism works even if a client is routed to a different server on reconnect.</span></span> <span data-ttu-id="4e267-144">Propojovacího rozhraní si je vědoma ve všech serverech a nezávisle na tom, který server pro připojení klienta.</span><span class="sxs-lookup"><span data-stu-id="4e267-144">The backplane is aware of all the servers, and it doesn't matter which server a client connects to.</span></span>

## <a name="limitations"></a><span data-ttu-id="4e267-145">Omezení</span><span class="sxs-lookup"><span data-stu-id="4e267-145">Limitations</span></span>

<span data-ttu-id="4e267-146">Pomocí propojovací rozhraní, maximální propustnost je nižší, než je v případě, že klienti komunikují přímo s uzlem jeden server.</span><span class="sxs-lookup"><span data-stu-id="4e267-146">Using a backplane, the maximum message throughput is lower than it is when clients talk directly to a single server node.</span></span> <span data-ttu-id="4e267-147">Je to způsobeno propojovacího rozhraní předává každou zprávu pro každý uzel, takže propojovacího rozhraní se může stát úzkým místem.</span><span class="sxs-lookup"><span data-stu-id="4e267-147">That's because the backplane forwards every message to every node, so the backplane can become a bottleneck.</span></span> <span data-ttu-id="4e267-148">Zda je toto omezení problém závisí na aplikaci.</span><span class="sxs-lookup"><span data-stu-id="4e267-148">Whether this limitation is a problem depends on the application.</span></span> <span data-ttu-id="4e267-149">Tady jsou například některé typické scénáře SignalR:</span><span class="sxs-lookup"><span data-stu-id="4e267-149">For example, here are some typical SignalR scenarios:</span></span>

- <span data-ttu-id="4e267-150">[Všesměrové vysílání server](tutorial-server-broadcast-with-aspnet-signalr.md) (například běžícími): Backplanes fungovat i pro tento scénář, protože serveru ovládá rychlost, jakou jsou odesílány zprávy.</span><span class="sxs-lookup"><span data-stu-id="4e267-150">[Server broadcast](tutorial-server-broadcast-with-aspnet-signalr.md) (e.g., stock ticker): Backplanes work well for this scenario, because the server controls the rate at which messages are sent.</span></span>
- <span data-ttu-id="4e267-151">[Klient klient](tutorial-getting-started-with-signalr.md) (například chat): V tomto scénáři můžou propojovacího rozhraní problémové místo, pokud počet zpráv škáluje s počtem klientů; to znamená, pokud počet zpráv zvětšování úměrně Další klienti se připojují.</span><span class="sxs-lookup"><span data-stu-id="4e267-151">[Client-to-client](tutorial-getting-started-with-signalr.md) (e.g., chat): In this scenario, the backplane might be a bottleneck if the number of messages scales with the number of clients; that is, if the rate of messages grows proportionally as more clients join.</span></span>
- <span data-ttu-id="4e267-152">[Vysoká frekvence v reálném čase](tutorial-high-frequency-realtime-with-signalr.md) (např. v reálném čase hry): propojovací rozhraní se nedoporučuje pro tento scénář.</span><span class="sxs-lookup"><span data-stu-id="4e267-152">[High-frequency realtime](tutorial-high-frequency-realtime-with-signalr.md) (e.g., real-time games): A backplane is not recommended for this scenario.</span></span>

## <a name="enabling-tracing-for-signalr-scaleout"></a><span data-ttu-id="4e267-153">Povolení trasování pro škálování SignalR</span><span class="sxs-lookup"><span data-stu-id="4e267-153">Enabling Tracing For SignalR Scaleout</span></span>

<span data-ttu-id="4e267-154">Pokud chcete povolit trasování pro backplanes, přidat následující části v souboru web.config, v kořenové **konfigurace** element:</span><span class="sxs-lookup"><span data-stu-id="4e267-154">To enable tracing for the backplanes, add the following sections to the web.config file, under the root **configuration** element:</span></span>

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]