---
uid: single-page-application/overview/templates/breezeknockout-template
title: "Šablony uloženy/Knockout | Microsoft Docs"
author: madskristensen
description: "Šablona uloženy/Knockout jedné stránky aplikací"
ms.author: aspnetcontent
manager: wpickett
ms.date: 01/30/2013
ms.topic: article
ms.assetid: 3bd94827-3c59-448f-abc3-36e6df4858db
ms.technology: 
ms.prod: .net-framework
msc.legacyurl: /single-page-application/overview/templates/breezeknockout-template
msc.type: authoredcontent
ms.openlocfilehash: 07ec099a0381458fe42c1972a2554f76fd34638c
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2017
---
<a name="breezeknockout-template"></a><span data-ttu-id="bb015-103">Uloženy/Knockout šablony</span><span class="sxs-lookup"><span data-stu-id="bb015-103">Breeze/Knockout template</span></span>
====================
<span data-ttu-id="bb015-104">podle [Mads Kristensen](https://github.com/madskristensen)</span><span class="sxs-lookup"><span data-stu-id="bb015-104">by [Mads Kristensen](https://github.com/madskristensen)</span></span>

> <span data-ttu-id="bb015-105">Šablony MVC uloženy/Knockout napsal Warda zvonku</span><span class="sxs-lookup"><span data-stu-id="bb015-105">The Breeze/Knockout MVC Template was written by Ward Bell</span></span>
> 
> [<span data-ttu-id="bb015-106">Stažení šablony MVC uloženy/Knockout</span><span class="sxs-lookup"><span data-stu-id="bb015-106">Download the Breeze/Knockout MVC Template</span></span>](https://go.microsoft.com/fwlink/?LinkId=282649)


<span data-ttu-id="bb015-107">Jste slyšeli o "jednostránkové aplikace" (SPA) zajímalo, co je.</span><span class="sxs-lookup"><span data-stu-id="bb015-107">You've heard of "single page application" (SPA) and wondered what it is.</span></span> <span data-ttu-id="bb015-108">Při čtení může o tom, které by místo prostředí pro sami.</span><span class="sxs-lookup"><span data-stu-id="bb015-108">While you could read about it, you'd rather experience it for yourself.</span></span> <span data-ttu-id="bb015-109">Ale který má čas Stáhnout ukázku?</span><span class="sxs-lookup"><span data-stu-id="bb015-109">But who has time to download a sample?</span></span> <span data-ttu-id="bb015-110">Pokud máte k dispozici sady Visual Studio, budete mít příklad SPA a spuštěnou v menší než 60 sekund s architekturou ASP.NET MVC 4 šablony "Uloženy/Knockout jedné stránky aplikace".</span><span class="sxs-lookup"><span data-stu-id="bb015-110">If you've got Visual Studio, you'll have an example SPA up and running in less than 60 seconds with the ASP.NET MVC 4 "Breeze/Knockout Single Page Application" template.</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrRunning.png)

## <a name="what-is-the-breezeknockout-spa-template"></a><span data-ttu-id="bb015-111">Co je šablona SPA uloženy/Knockout?</span><span class="sxs-lookup"><span data-stu-id="bb015-111">What is the Breeze/Knockout SPA Template?</span></span>

<span data-ttu-id="bb015-112">Většina šablony projektů generovat kostru aplikace.</span><span class="sxs-lookup"><span data-stu-id="bb015-112">Most project templates generate an application skeleton.</span></span> <span data-ttu-id="bb015-113">Vložit dužina na těchto kosti přidáním kódu a nakonec poskytovat funkční aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb015-113">You put flesh on those bones by adding your code and eventually deliver a working application.</span></span> <span data-ttu-id="bb015-114">Šablony uloženy/Knockout SPA se liší.</span><span class="sxs-lookup"><span data-stu-id="bb015-114">The Breeze/Knockout SPA template is different.</span></span> <span data-ttu-id="bb015-115">Vygeneruje ukázková aplikace pro vás zkoumat.</span><span class="sxs-lookup"><span data-stu-id="bb015-115">It generates a sample application for you to study.</span></span> <span data-ttu-id="bb015-116">Ukazuje SPA návrh aplikace a řadu techniky pro vytváření SPA.</span><span class="sxs-lookup"><span data-stu-id="bb015-116">It demonstrates a SPA application design and many of the techniques for building a SPA.</span></span>

<span data-ttu-id="bb015-117">Šablona uloženy/Knockout je variace na [kódem KnockoutJS SPA šablony](../introduction/knockoutjs-template.md) součástí technologie ASP.NET a webové nástroje 2012.2 aktualizace.</span><span class="sxs-lookup"><span data-stu-id="bb015-117">The Breeze/Knockout template is a variation on the [KnockoutJS SPA template](../introduction/knockoutjs-template.md) included in the ASP.NET and Web Tools 2012.2 Update.</span></span> <span data-ttu-id="bb015-118">Šablona uloženy SPA generuje aplikace pomocí stejné prostředí pro uživatele, ale má jinou implementaci, pomocí uloženy dat pro správu.</span><span class="sxs-lookup"><span data-stu-id="bb015-118">The Breeze SPA template generates an application with the same user experience, but it has a different implementation, using Breeze for data management.</span></span>

<span data-ttu-id="bb015-119">Šablony kódem KnockoutJS SPA vytváří žádosti o službu s nezpracovaná jQuery AJAX, který je dostačující pro jednoduchou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="bb015-119">The KnockoutJS SPA template makes service requests with raw jQuery AJAX, which is adequate for a simple application.</span></span> <span data-ttu-id="bb015-120">Ale sofistikovanější aplikací mít náročnějšími požadavky na správu data.</span><span class="sxs-lookup"><span data-stu-id="bb015-120">But more sophisticated apps have more demanding data management requirements.</span></span> <span data-ttu-id="bb015-121">Například většina aplikací:</span><span class="sxs-lookup"><span data-stu-id="bb015-121">For example, most applications:</span></span>

- <span data-ttu-id="bb015-122">Dotaz a znovu zadat dotaz na server během relace rozšířených uživatele.</span><span class="sxs-lookup"><span data-stu-id="bb015-122">Query and re-query the server during an extended user session.</span></span>
- <span data-ttu-id="bb015-123">Přidání dotazu filtrů, řazení a stránkování.</span><span class="sxs-lookup"><span data-stu-id="bb015-123">Add query filters, sorting, and paging.</span></span>
- <span data-ttu-id="bb015-124">Stejná data sdílet mezi více obrazovky.</span><span class="sxs-lookup"><span data-stu-id="bb015-124">Share the same data across multiple screens.</span></span>
- <span data-ttu-id="bb015-125">Accumulate změny mnoho objektů a potom je uložit jako jediná transakce.</span><span class="sxs-lookup"><span data-stu-id="bb015-125">Accumulate changes to many objects, then save them as a single transaction.</span></span>
- <span data-ttu-id="bb015-126">Ověřte změny na straně klienta, takže uživatel může opravit chyby před potvrzením změny do databáze.</span><span class="sxs-lookup"><span data-stu-id="bb015-126">Validate changes on the client, so the user can correct mistakes before committing changes to the database.</span></span>

<span data-ttu-id="bb015-127">Knihovna BreezeJS zpracovává tyto chores pro vás, uvolnění můžete vyvíjet aplikace logiky a uživatelské prostředí, které vás zajímají.</span><span class="sxs-lookup"><span data-stu-id="bb015-127">The BreezeJS library handles these chores for you, freeing you to develop the application logic and user experience that matter most.</span></span>

<span data-ttu-id="bb015-128">[**Uloženy** ](http://www.breezejs.com/?utm_source=ms-spa) otevřeným zdrojem knihovnu pro vytváření bohaté data aplikací v jazyce JavaScript a HTML, typy aplikací, které byly obsaženy v minulosti jako samostatné aplikací klasické pracovní plochy.</span><span class="sxs-lookup"><span data-stu-id="bb015-128">[**Breeze**](http://www.breezejs.com/?utm_source=ms-spa) is an open source library for building rich data applications in JavaScript and HTML, the kinds of apps that have historically been delivered as stand-alone desktop applications.</span></span>

<span data-ttu-id="bb015-129">Šablony uloženy/Knockout pomáhá vám tento první zásadní krok k robustnější infrastrukturou pro správu dat.</span><span class="sxs-lookup"><span data-stu-id="bb015-129">The Breeze/Knockout template helps you take that first crucial step toward a more robust data management infrastructure.</span></span> <span data-ttu-id="bb015-130">Vyvolá ukázkové aplikace Todo, která je vně totožný s kódem KnockoutJS SPA šablony.</span><span class="sxs-lookup"><span data-stu-id="bb015-130">It produces a sample Todo application that is outwardly identical to the KnockoutJS SPA template.</span></span> <span data-ttu-id="bb015-131">Uvnitř nahradí Datová vrstva AJAX uloženy, takže je můžete porovnat dva blíží vedle sebe.</span><span class="sxs-lookup"><span data-stu-id="bb015-131">On the inside, it replaces the AJAX data layer with Breeze, so you can compare the two approaches side-by-side.</span></span> <span data-ttu-id="bb015-132">Samozřejmě sotva dotykem potenciál aplikace uloženy.</span><span class="sxs-lookup"><span data-stu-id="bb015-132">Of course, it barely touches the potential of a Breeze application.</span></span> <span data-ttu-id="bb015-133">Ale uvidíte, jak uloženy funguje a jak trochu je nutná ke správnému tento přechod.</span><span class="sxs-lookup"><span data-stu-id="bb015-133">But you'll see how Breeze works and how little is required to make that transition.</span></span>

<span data-ttu-id="bb015-134">Můžeme začít.</span><span class="sxs-lookup"><span data-stu-id="bb015-134">Let's get started.</span></span>

## <a name="create-a-breezeknockout-template-project"></a><span data-ttu-id="bb015-135">Vytvoření projektu šablony uloženy/Knockout</span><span class="sxs-lookup"><span data-stu-id="bb015-135">Create a Breeze/Knockout Template Project</span></span>

<span data-ttu-id="bb015-136">Stáhněte a nainstalujte šablony kliknutím na výše uvedené tlačítko Stáhnout.</span><span class="sxs-lookup"><span data-stu-id="bb015-136">Download and install the template by clicking the Download button above.</span></span> <span data-ttu-id="bb015-137">Šablona je zabalené jako soubor rozšíření Visual Studio (VSIX).</span><span class="sxs-lookup"><span data-stu-id="bb015-137">The template is packaged as a Visual Studio Extension (VSIX) file.</span></span> <span data-ttu-id="bb015-138">Možná budete muset restartovat Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bb015-138">You might need to restart Visual Studio.</span></span>

<span data-ttu-id="bb015-139">V **šablony** podokně, vyberte **nainstalovaných šablonách** a rozbalte **Visual C#** uzlu.</span><span class="sxs-lookup"><span data-stu-id="bb015-139">In the **Templates** pane, select **Installed Templates** and expand the **Visual C#** node.</span></span> <span data-ttu-id="bb015-140">V části **Visual C#**, vyberte **webové**.</span><span class="sxs-lookup"><span data-stu-id="bb015-140">Under **Visual C#**, select **Web**.</span></span> <span data-ttu-id="bb015-141">V seznamu šablon projektu, vyberte **webové aplikace ASP.NET MVC 4**.</span><span class="sxs-lookup"><span data-stu-id="bb015-141">In the list of project templates, select **ASP.NET MVC 4 Web Application**.</span></span> <span data-ttu-id="bb015-142">Název projektu a klikněte na tlačítko **OK**.</span><span class="sxs-lookup"><span data-stu-id="bb015-142">Name the project and click **OK**.</span></span>

<span data-ttu-id="bb015-143">V **nový projekt** průvodci vyberte **uloženy Knockout SPA**.</span><span class="sxs-lookup"><span data-stu-id="bb015-143">In the **New Project** wizard, select **Breeze Knockout SPA**.</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/SelectBreezeKOSpaTemplate.png)

<span data-ttu-id="bb015-144">Stisknutím Ctrl + F5 sestavení a spuštění aplikace bez ladění nebo stisknutím klávesy F5 spusťte s laděním.</span><span class="sxs-lookup"><span data-stu-id="bb015-144">Press Ctrl-F5 to build and run the application without debugging, or press F5 to run with debugging.</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/ZephyrRunning.png)

<span data-ttu-id="bb015-145">Při prvním spuštění aplikace, zobrazí se přihlašovací obrazovka.</span><span class="sxs-lookup"><span data-stu-id="bb015-145">When the application first runs, it displays a login screen.</span></span> <span data-ttu-id="bb015-146">Klikněte na odkaz "Registrace" a nová stránka glides do zobrazení, kde můžete zadat uživatelské jméno a heslo.</span><span class="sxs-lookup"><span data-stu-id="bb015-146">Click the "Sign up" link and a new page glides into view, where you can enter a username and password.</span></span> <span data-ttu-id="bb015-147">(Přihlášení a registrace stránky jsou vytvořeny pomocí technologie ASP.NET MVC.) Při odesílání registračním formuláři server vygeneruje seznamu úkolů se dvěma položkami pro váš účet.</span><span class="sxs-lookup"><span data-stu-id="bb015-147">(The login and registration pages are built using ASP.NET MVC.) When you submit the registration form, the server generates a TodoList with two items for your account.</span></span> <span data-ttu-id="bb015-148">Potom představuje je pro vás na žlutý Poznámka.</span><span class="sxs-lookup"><span data-stu-id="bb015-148">Then it presents them to you on a yellow note.</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/TodoList.png)

<span data-ttu-id="bb015-149">Nyní jste v krajině SPA.</span><span class="sxs-lookup"><span data-stu-id="bb015-149">Now you are in the land of SPA.</span></span> <span data-ttu-id="bb015-150">Všechno, co jste vidí a jak při manipulaci s Todos je vykreslen a spravovat na straně klienta pomocí Knockout a uloženy.</span><span class="sxs-lookup"><span data-stu-id="bb015-150">Everything you see and experience while manipulating Todos is rendered and managed on the client with the help of Knockout and Breeze.</span></span> <span data-ttu-id="bb015-151">Jako uživatel prozkoumejte aplikace...</span><span class="sxs-lookup"><span data-stu-id="bb015-151">Explore the app as a user …</span></span> <span data-ttu-id="bb015-152">ale s oka pro vývojáře.</span><span class="sxs-lookup"><span data-stu-id="bb015-152">but with a developer's eye.</span></span> <span data-ttu-id="bb015-153">Pomocí nástrojů pro vývojáře v prohlížeči pro zachycení síťového provozu.</span><span class="sxs-lookup"><span data-stu-id="bb015-153">Use the developer tools in your browser to capture the network traffic.</span></span> <span data-ttu-id="bb015-154">(V Internet Exploreru: stiskněte klávesu F12, vyberte **sítě** a klikněte na **spustit zachytávání**.) Teď vyzkoušejte tyto věci:</span><span class="sxs-lookup"><span data-stu-id="bb015-154">(In Internet Explorer: Press F12, select the **Network** tab, and click **Start capturing**.) Now try the following:</span></span>

- <span data-ttu-id="bb015-155">Přidáte novou položku úkolů.</span><span class="sxs-lookup"><span data-stu-id="bb015-155">Add a new Todo item.</span></span>
- <span data-ttu-id="bb015-156">Klepněte na popisek a upravit nadpis položky Todo</span><span class="sxs-lookup"><span data-stu-id="bb015-156">Click the label and edit the Todo item title</span></span>
- <span data-ttu-id="bb015-157">Zaškrtněte políčko k označení položky Hotovo.</span><span class="sxs-lookup"><span data-stu-id="bb015-157">Check a checkbox to mark the item done.</span></span> <span data-ttu-id="bb015-158">Všimněte si, že textové pole je zakázaná, tak název již nelze upravovat.</span><span class="sxs-lookup"><span data-stu-id="bb015-158">Notice that the textbox is disabled, so the title is no longer editable.</span></span>
- <span data-ttu-id="bb015-159">Klikněte na tlačítko 'x' vpravo popisku.</span><span class="sxs-lookup"><span data-stu-id="bb015-159">Click the ‘x' to the right of the label.</span></span> <span data-ttu-id="bb015-160">Položka zmizí, se odstraní z databáze.</span><span class="sxs-lookup"><span data-stu-id="bb015-160">The item disappears and is deleted from the database.</span></span>
- <span data-ttu-id="bb015-161">Vyberte jinou položku a zrušit její název.</span><span class="sxs-lookup"><span data-stu-id="bb015-161">Pick another item and clear its title.</span></span> <span data-ttu-id="bb015-162">Získáte chybu ověření, že název je povinný.</span><span class="sxs-lookup"><span data-stu-id="bb015-162">You'll get a validation error that the title is required.</span></span> <span data-ttu-id="bb015-163">Po krátkou pozastaví je obnoven předchozí název.</span><span class="sxs-lookup"><span data-stu-id="bb015-163">After a brief pause, the previous title is restored.</span></span>
- <span data-ttu-id="bb015-164">Zadejte ridiculously dlouhý název.</span><span class="sxs-lookup"><span data-stu-id="bb015-164">Type in a ridiculously long title.</span></span> <span data-ttu-id="bb015-165">Získáte chyba různých ověření, že název je příliš dlouhý.</span><span class="sxs-lookup"><span data-stu-id="bb015-165">You'll get a different validation error that the title is too long.</span></span>
- <span data-ttu-id="bb015-166">Klikněte na tlačítko "Přidat seznam úkolů".</span><span class="sxs-lookup"><span data-stu-id="bb015-166">Click the "Add Todo List" button.</span></span> <span data-ttu-id="bb015-167">Nového seznamu se zobrazí vlevo od předchozího seznamu.</span><span class="sxs-lookup"><span data-stu-id="bb015-167">A new list appears to the left of the previous list.</span></span>
- <span data-ttu-id="bb015-168">Přehrání se název seznamu úkolů, aktivuje vyžaduje a délka ověření.</span><span class="sxs-lookup"><span data-stu-id="bb015-168">Play with the TodoList title, triggering its required and length validations.</span></span>
- <span data-ttu-id="bb015-169">Klikněte do textového pole Název zrušte chybovou zprávu.</span><span class="sxs-lookup"><span data-stu-id="bb015-169">Click in the title textbox to clear the error message.</span></span>
- <span data-ttu-id="bb015-170">Klikněte na symbol "x" na zobrazený v pravém horním rohu seznam úkolů a jeho todos odstraníte.</span><span class="sxs-lookup"><span data-stu-id="bb015-170">Click the "x" in the circle in the upper right corner to delete the TodoList and its todos.</span></span>

<span data-ttu-id="bb015-171">Logiku ověření se provádí straně klienta podle uloženy.</span><span class="sxs-lookup"><span data-stu-id="bb015-171">The validation logic is performed client-side by Breeze.</span></span> <span data-ttu-id="bb015-172">Atributy ověřování na třídy modelu serveru jsou rozšíří do klienta a jsou prováděny automaticky předtím, než se klient připojí k serveru.</span><span class="sxs-lookup"><span data-stu-id="bb015-172">Validation attributes on the server model classes are propagated to the client and executed automatically before the client contacts the server.</span></span>

<span data-ttu-id="bb015-173">Zkontrolujte síťový provoz.</span><span class="sxs-lookup"><span data-stu-id="bb015-173">Review the network traffic.</span></span> <span data-ttu-id="bb015-174">Všimněte si, že neexistují žádná volání na server při uloženy došlo k chybě.</span><span class="sxs-lookup"><span data-stu-id="bb015-174">Notice that there were no calls to the server when Breeze detected an error.</span></span> <span data-ttu-id="bb015-175">Každé změně platný výsledkem požadavek POST na "/ api/Todo/SaveChanges".</span><span class="sxs-lookup"><span data-stu-id="bb015-175">Each valid change resulted in a POST request to "/api/Todo/SaveChanges".</span></span> <span data-ttu-id="bb015-176">Uloženy obsahuje ureitou změny a odešle je společně jako jeden požadavek na kontroleru webového rozhraní API `SaveChanges` metoda.</span><span class="sxs-lookup"><span data-stu-id="bb015-176">Breeze bundles the changes and sends them together as a single request to the Web API controller's `SaveChanges` method.</span></span> <span data-ttu-id="bb015-177">Která se liší od KockoutJS SPA šablony, která umožňuje PUT, POST a odstranit požadavky pro každou položku jednotlivě.</span><span class="sxs-lookup"><span data-stu-id="bb015-177">That's different from KockoutJS SPA template, which makes PUT, POST, and DELETE requests for each item individually.</span></span>

## <a name="peek-inside"></a><span data-ttu-id="bb015-178">Prohlížení uvnitř</span><span class="sxs-lookup"><span data-stu-id="bb015-178">Peek inside</span></span>

<span data-ttu-id="bb015-179">Tato aplikace má straně klienta a straně serveru.</span><span class="sxs-lookup"><span data-stu-id="bb015-179">This application has a client side and a server side.</span></span> <span data-ttu-id="bb015-180">Na straně klienta zásobníku se skládá z trochu HTML a kombinaci modulů JavaScript aplikace (ve složce "aplikace") plus knihovny JavaScript třetích stran (ve složce "Skripty").</span><span class="sxs-lookup"><span data-stu-id="bb015-180">The client-side stack consists of a little HTML and a combination of application JavaScript modules (in the "app" folder) plus third-party JavaScript libraries (in the "Scripts" folder).</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/ClientArchitecture.png)

<span data-ttu-id="bb015-181">Pokud jste prozkoumat šabloně kódem KnockoutJS SPA, to velmi povědomé.</span><span class="sxs-lookup"><span data-stu-id="bb015-181">If you've investigated the KnockoutJS SPA template, this should look very familiar.</span></span> <span data-ttu-id="bb015-182">Soustřeďte se na blue polí.</span><span class="sxs-lookup"><span data-stu-id="bb015-182">Focus on the blue boxes.</span></span> <span data-ttu-id="bb015-183">Architektura uživatelského rozhraní je Model-View-ViewModel (modelem MVVM), ve které jsou pomůcky HTML zobrazení řádně odděleny z podpůrné kódu prezentace v zobrazení modelu.</span><span class="sxs-lookup"><span data-stu-id="bb015-183">The UI architecture is Model-View-ViewModel (MVVM), in which the HTML widgets of the view are cleanly separated from the supporting presentation code in the view-model.</span></span> <span data-ttu-id="bb015-184">Systém vazby dat (v tomto případě Knockout) koordinuje zobrazení a modelu zobrazení tak, aby každý můžete provést úlohy bez dokonalou znalosti o dalších.</span><span class="sxs-lookup"><span data-stu-id="bb015-184">A data binding system (Knockout in this case) coordinates the view and view-model so that each can do its job without intimate knowledge of the other.</span></span>

<span data-ttu-id="bb015-185">Model zapouzdří data úkolů.</span><span class="sxs-lookup"><span data-stu-id="bb015-185">The model encapsulates the Todo data.</span></span> <span data-ttu-id="bb015-186">Entity v modelu se vytvářejí pomocí uloženy pozorovatelné vlastnostmi Knockout, proto mohou být vázány přímo na pomůckách v zobrazení.</span><span class="sxs-lookup"><span data-stu-id="bb015-186">Entities in the model are constructed by Breeze with Knockout observable properties, so they can be bound directly to widgets in the view.</span></span> <span data-ttu-id="bb015-187">Model zobrazení zobrazí data kontext k získání a uložte modelu entity.</span><span class="sxs-lookup"><span data-stu-id="bb015-187">The view-model asks the data context to acquire and save the model entities.</span></span> <span data-ttu-id="bb015-188">Data kontextu deleguje většinu práce, kterou uloženy.</span><span class="sxs-lookup"><span data-stu-id="bb015-188">The data context delegates most of the work to Breeze.</span></span>

<span data-ttu-id="bb015-189">Na straně serveru zásobníku se skládá z nějaký kód developer a tři knihovny .NET princip: webového rozhraní API, rozhraní Entity Framework a Breeze.NET:</span><span class="sxs-lookup"><span data-stu-id="bb015-189">The server-side stack consists of some developer code and three principle .NET libraries: Web API, Entity Framework, and Breeze.NET:</span></span>

![](http://www.breezejs.com/sites/all/images/spa-template/ServerArchitecture.png)

<span data-ttu-id="bb015-190">Základní architektura je stejný jako KockoutJS SPA šablonu.</span><span class="sxs-lookup"><span data-stu-id="bb015-190">The basic architecture is the same as the KockoutJS SPA template.</span></span> <span data-ttu-id="bb015-191">Implementace je však mnohem jednodušší: byly odstraněny DTOs a Breeze.NET delegované většinu podrobnosti Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="bb015-191">However, the implementation is much simpler: The DTOs were deleted, and most of the Entity Framework details have been delegated to Breeze.NET.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bb015-192">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bb015-192">Next Steps</span></span>

<span data-ttu-id="bb015-193">Doporučujeme prozkoumávání kód provést podle [rozsáhlé vysvětlení](http://www.breezejs.com/spa-template?utm_source=ms-spa) klienta a serveru zásobníky na webu uloženy.</span><span class="sxs-lookup"><span data-stu-id="bb015-193">We suggest that you explore the code, guided by the [extensive discussion](http://www.breezejs.com/spa-template?utm_source=ms-spa) of both the client and the server stacks on the Breeze website.</span></span>

<span data-ttu-id="bb015-194">Můžete vyzkoušet přehrávání s dotazem na straně klienta uloženy; Přidejte některé filtrů a řazení.</span><span class="sxs-lookup"><span data-stu-id="bb015-194">You might try playing with Breeze client-side query; add some filters and sorts.</span></span> <span data-ttu-id="bb015-195">Můžete přidat další vlastnosti modelu a dalších entit získat lepší chování pro vývoj SPA začátku do konce.</span><span class="sxs-lookup"><span data-stu-id="bb015-195">You might add more model properties and more entities to get a better feel for end-to-end SPA development.</span></span> <span data-ttu-id="bb015-196">Pokud jste si jisti návrhu, můžete oddělení se funkce úkolů a nahraďte je vlastními.</span><span class="sxs-lookup"><span data-stu-id="bb015-196">When you are confident of the design, you can tear out the Todo features and replace them with your own.</span></span>

<span data-ttu-id="bb015-197">Brzy budete připraveno pro další krok velký: přidání obrazovky na straně klienta a navigace mezi nimi.</span><span class="sxs-lookup"><span data-stu-id="bb015-197">Soon you'll be ready for the next big step: Adding client-side screens and navigating among them.</span></span> <span data-ttu-id="bb015-198">Budete nechte Tato šablona SPA a zapněte k více celý zásobník SPA, jako například [aktivní ručníků Jan Papa](https://github.com/johnpapa/HotTowel#readme "aktivní ručníků"), k kombinace uloženy a Knockout přidává Durandal.</span><span class="sxs-lookup"><span data-stu-id="bb015-198">You'll leave this SPA template behind and turn to a more complete SPA stack, such as [John Papa's Hot Towel](https://github.com/johnpapa/HotTowel#readme "Hot Towel"), which adds Durandal to the Breeze and Knockout mix.</span></span>