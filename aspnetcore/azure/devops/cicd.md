---
title: DevOps s využitím ASP.NET Core a Azure | Průběžná integrace a nasazování
author: CamSoper
description: Průvodce, který poskytuje pokyny k začátku do konce na vytváření procesních toků pro DevOps pro aplikace ASP.NET Core hostované v Azure.
ms.author: casoper
ms.date: 08/07/2018
uid: azure/devops/cicd
ms.openlocfilehash: 9127f26fc4e3f78ec745fa1e342de137228f484e
ms.sourcegitcommit: 29dfe436f54a27fbb4f6494bc639d16c75001fab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "39722615"
---
# <a name="continuous-integration-and-deployment"></a>Průběžná integrace a nasazování

V předchozích kapitol vytvoříte místní úložiště Git pro aplikaci jednoduché Reader informačního kanálu. V této kapitole budete publikovat tento kód do úložiště GitHub a vytvořit kanál DevOps Visual Studio Team Services (VSTS). Kanál umožňuje průběžné vytváření buildů a nasazení aplikace. Každé potvrzení do úložiště GitHub se aktivuje sestavení a nasazení do přípravného slotu webové aplikace Azure.

V této části budete provádět následující úlohy:

* Publikování aplikace kódu na Githubu
* Odpojit místní nasazení přes Git
* Vytvořit účet VSTS
* Vytvořit týmový projekt ve VSTS
* Vytvořte definici sestavení
* Vytvořit kanál pro vydávání verzí
* Potvrzení změn na Githubu a automaticky nasadit do Azure
* Prozkoumejte kanálu VSTS DevOps

## <a name="publish-the-apps-code-to-github"></a>Publikování aplikace kódu na Githubu

1. Otevřete okno prohlížeče a přejděte do `https://github.com`.
1. Klikněte na tlačítko **+** rozevírací seznam v záhlaví a vyberte **nové úložiště**:

    ![Možnost Nový úložiště GitHub](media/cicd/github-new-repo.png)

1. Vyberte svůj účet v **vlastníka** rozevíracího seznamu a zadejte *jednoduchý kanálu čtečky* v **název úložiště** textového pole.
1. Klikněte na tlačítko **vytvoření úložiště** tlačítko.
1. Otevřete příkazové okno místního počítače. Přejděte do adresáře, ve kterém *jednoduchý kanálu čtečky* uložená v úložišti Git.
1. Přejmenovat stávající *původu* do vzdáleného úložiště *nadřazeného*. Spusťte následující příkaz:
    ```console
    git remote rename origin upstream
    ```
1. Přidat nový *původu* vzdálené odkazuje na kopii úložišti na Githubu. Spusťte následující příkaz:
    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```
1. Publikování místního úložiště Git do nově vytvořené úložiště GitHub. Spusťte následující příkaz:
    ```console
    git push -u origin master
    ```
1. Otevřete okno prohlížeče a přejděte do `https://github.com/<GitHub_username>/simple-feed-reader/`. Ověřte, že váš kód se zobrazí v úložišti GitHub.

## <a name="disconnect-local-git-deployment"></a>Odpojit místní nasazení přes Git

Odeberte místní nasazení přes Git pomocí následujícího postupu. VSTS nahradí a argumentech, které tuto funkci.

1. Otevřít [webu Azure portal](https://portal.azure.com/)a přejděte *pracovní (mywebapp\<unique_number\>/pracovní)* webové aplikace. Webové aplikace můžete rychle umístěný tak, že zadáte *pracovní* vyhledávacího pole na portálu:

    ![pracovní webové aplikace hledaný termín](media/cicd/portal-search-box.png)

1. Klikněte na tlačítko **možnosti nasazení**. Otevře se nový panel. Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat. Operace odstranění potvrďte kliknutím **Ano** tlačítko.
1. Přejděte *mywebapp < unique_number >* služby App Service. Připomínáme je možné k rychlému vyhledání služby App Service na portálu vyhledávacího pole.
1. Klikněte na tlačítko **možnosti nasazení**. Otevře se nový panel. Klikněte na tlačítko **odpojit** místní Git konfigurace správy zdrojového kódu, který byl přidán v předchozích kapitol odebrat. Operace odstranění potvrďte kliknutím **Ano** tlačítko.

## <a name="create-a-vsts-account"></a>Vytvořit účet VSTS

1. Otevřete prohlížeč a přejděte [stránce pro vytvoření účtu VSTS](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Zadejte jedinečný název do **vyberte si snadno zapamatovatelné jméno** textového pole a vytvoří adresu URL pro přístup k účtu VSTS.
1. Vyberte **Git** přepínač, protože je kód hostovaný v úložišti GitHub.
1. Klikněte na tlačítko **pokračovat** tlačítko. Po krátkém čekání, účet a týmový projekt s názvem *MyFirstProject*, jsou vytvořeny.

    ![Stránka pro vytvoření účtu VSTS](media/cicd/vsts-account-creation.png)

1. Otevřete potvrzení e-mailu označující, že účet VSTS a projektu jsou připravené k použití. Klikněte na tlačítko **začněte svůj projekt** tlačítka:

    ![Váš projekt tlačítko Start](media/cicd/vsts-start-project.png)

1. V prohlížeči se otevře  *\<account_name\>. visualstudio.com*. Klikněte na tlačítko *MyFirstProject* odkaz se začne konfigurace projektu kanálu DevOps.

## <a name="configure-the-devops-pipeline"></a>Konfigurace kanálu DevOps

Existují tři samostatné kroky k dokončení. Dokončením kroků v následujících třech částech vede provozní kanál DevOps.

### <a name="grant-vsts-access-to-the-github-repository"></a>VSTS udělit přístup k úložišti GitHub

1. Rozbalte **nebo vytváření kódu z externího úložiště** prvku typu accordion. Klikněte na tlačítko **nastavení sestavení** tlačítka:

    ![Tlačítko Nastavení sestavení](media/cicd/vsts-setup-build.png)

1. Vyberte **Githubu** možnost **vyberte zdroj** části:

    ![Vyberte zdroje – GitHub](media/cicd/vsts-select-source.png)

1. Vyžaduje se autorizace, než VSTS můžete získat přístup k úložišti GitHub. Zadejte *< GitHub_username > Githubu připojení* v **název připojení** textového pole. Příklad:

    ![Název připojení Githubu](media/cicd/vsts-repo-authz.png)

1. Pokud na vašem účtu GitHub je povoleno dvoufaktorové ověřování, osobní přístupový token je povinný. V takovém případě klikněte na tlačítko **autorizovat pomocí osobního přístupového tokenu Githubu** odkaz. Zobrazit [oficiální pokyny k vytvoření tokenu pat Githubu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) nápovědu. Pouze *úložiště* obor oprávnění je potřeba. V opačném případě klikněte na tlačítko **autorizovat pomocí OAuth** tlačítko.
1. Po zobrazení výzvy, přihlaste se k vašemu účtu GitHub. Vyberte Authorize udělit přístup ke svému účtu VSTS. V případě úspěchu, je vytvořen nový koncový bod služby.
1. Klikněte na tlačítko se třemi tečkami vedle **úložiště** tlačítko. Vyberte *< GitHub_username > / jednoduchý kanálu čtečky* úložiště ze seznamu. Klikněte na tlačítko **vyberte** tlačítko.
1. Vyberte *hlavní* vytvářet větve z **výchozí větev pro ruční a plánovaná sestavení** rozevíracího seznamu. Klikněte na tlačítko **pokračovat** tlačítko. Zobrazí se stránka pro výběr šablony.

### <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1. Na stránce Výběr šablony zadejte *ASP.NET Core* do vyhledávacího pole:

    ![ASP.NET Core hledání na stránce šablony](media/cicd/vsts-template-selection.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší **ASP.NET Core** šablony a kliknutím **použít** tlačítko.
1. **Úlohy** se zobrazí karta definice sestavení. Klikněte na tlačítko **triggery** kartu.
1. Zkontrolujte, **aktivovat nepřetržitou integraci** pole. V části **filtry větví** části, ujistěte se, že **typ** rozevíracího seznamu je nastavena na *zahrnout*. Nastavte **větev specifikace** rozevíracího seznamu *hlavní*.

    ![Povolit nastavení průběžné integrace](media/cicd/vsts-enable-ci.png)

    Toto nastavení způsobí sestavení aktivovat při každé změně se vloží do *hlavní* větev úložiště GitHub. Nepřetržitá integrace je testován v [potvrzení změn na Githubu a automaticky nasadit do Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) oddílu.

1. Klikněte na tlačítko **Uložit & frontu** tlačítko a vyberte **Uložit** možnost:

    ![Tlačítko Uložit](media/cicd/vsts-save-build.png)

1. Zobrazí se následující modální dialogové okno:

    ![Modální dialogové okno Uložit definici sestavení-](media/cicd/vsts-save-modal.png)

    Použít výchozí složky *\\*a klikněte na tlačítko **Uložit** tlačítko.

### <a name="create-the-release-pipeline"></a>Vytvořit kanál pro vydávání verzí

1. Klikněte na tlačítko **verze** kartu týmového projektu. Klikněte na tlačítko **nový kanál** tlačítko.

    ![Karta – tlačítko Nová definice verze](media/cicd/vsts-new-release-definition.png)

    Otevře se podokno výběr šablony.

1. Na stránce Výběr šablony zadejte *služby App Service* do vyhledávacího pole:

    ![Verze kanálu šablony vyhledávacího pole](media/cicd/vsts-release-template-search.png)

1. Šablona výsledky hledání zobrazeny. Najeďte myší **nasazení služby Azure App Service se slotem** šablony a kliknutím **použít** tlačítko. **Kanálu** se zobrazí karta kanál pro vydávání verzí.

    ![Kanál pro vydávání verzí kartu kanálu](media/cicd/vsts-release-definition-pipeline.png)

1. Klikněte na tlačítko **přidat** tlačítko **artefakty** pole. **Přidání artefaktu** panelu se zobrazí:

    ![Kanál pro vydávání verzí – přidání artefaktu panelu](media/cicd/vsts-release-add-artifact.png)

1. Vyberte **sestavení** dlaždici z **typ zdroje** oddílu. Tento typ umožňuje nastavit odkazy kanál pro vydávání verzí této definici sestavení.
1. Vyberte *MyFirstProject* z **projektu** rozevíracího seznamu.
1. Název definice sestavení vyberte *MyFirstProject ASP.NET Core-CI*, z **zdroj (definice sestavení)** rozevíracího seznamu.
1. Vyberte *nejnovější* z **výchozí verze** rozevíracího seznamu. Tato možnost sestavení artefakty vytvořené spuštěním nejnovější definice sestavení.
1. Nahradit text **alias zdroje** textové pole s *vyřadit*.
1. Klikněte na tlačítko **přidat** tlačítko. **Artefakty** části aktualizací zobrazíte změny.
1. Klikněte na ikonu blesku povolit nepřetržité nasazení:

    ![Kanál pro vydávání verzí artefakty – ikona blesku](media/cicd/vsts-artifacts-lightning-bolt.png)

    Tato možnost povolená dojde k nasazení pokaždé, když je k dispozici nové sestavení.
1. A **trigger průběžného nasazování** panelu se zobrazí na pravé straně. Klikněte na přepínací tlačítko k povolení této funkce. Není nutná pro povolení **triggeru žádosti o přijetí změn**.
1. Klikněte na tlačítko **přidat** rozevírací seznam v **vytvářet filtry větví** oddílu. Zvolte **Build Definition výchozí větev** možnost. Tento filtr způsobí, že verze aktivovat pouze pro sestavení z úložiště GitHub *hlavní* větve.
1. Klikněte na tlačítko **Uložit** tlačítko. Klikněte na tlačítko **OK** tlačítko ve výsledné **Uložit** modální dialogové okno.
1. Klikněte na tlačítko **prostředí 1** pole. **Prostředí** panelu se zobrazí na pravé straně. Změnit *prostředí 1* textu v **název prostředí** testovém poli *produkční*.

   ![Kanál pro vydávání verzí – textové pole pro název prostředí](media/cicd/vsts-environment-name-textbox.png)

1. Klikněte na tlačítko **fáze 1, 2 úlohy** odkaz v **produkční** pole:

    ![Kanál pro vydávání verzí - link.png produkční prostředí](media/cicd/vsts-production-link.png)

    **Úlohy** se zobrazí karta prostředí.
1. Klikněte na tlačítko **nasazení služby Azure App Service do slotu** úloh. Nastavení se zobrazí v panelu napravo.
1. Vyberte předplatné Azure spojené s App Service z **předplatného Azure** rozevíracího seznamu. Po výběru, klikněte na tlačítko **Authorize** tlačítko.
1. Vyberte *webovou aplikaci* z **typ aplikace** rozevíracího seznamu.
1. Vyberte *mywebapp / < unique_number / >* z **název služby App service** rozevíracího seznamu.
1. Vyberte *AzureTutorial* z **skupiny prostředků** rozevíracího seznamu.
1. Vyberte *pracovní* z **slotu** rozevíracího seznamu.
1. Klikněte na tlačítko **Uložit** tlačítko.
1. Najeďte myší výchozí název kanálu vydané verze. Klikněte na ikonu tužky a upravte ho. Použití *MyFirstProject ASP.NET Core-CD* jako název.

    ![Název kanálu vydané verze](media/cicd/vsts-release-definition-name.png)

1. Klikněte na tlačítko **Uložit** tlačítko.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Potvrzení změn na Githubu a automaticky nasadit do Azure

1. Otevřít *SimpleFeedReader.sln* v sadě Visual Studio.
1. V Průzkumníku řešení otevřete *Pages\Index.cshtml*. Změna `<h2>Simple Feed Reader - V3</h2>` k `<h2>Simple Feed Reader - V4</h2>`.
1. Stisknutím klávesy **Ctrl**+**Shift**+**B** k sestavení aplikace.
1. Potvrďte souboru do úložiště GitHub. Použijte buď **změny** stránky v sadě Visual Studio *Team Exploreru* kartu, nebo spuštěním následujících pomocí příkazového prostředí služby místním počítači:

    ```console
    git commit -a -m "upgraded to V4"
    ```
1. Nahrát změnu *hlavní* větvit do *původu* vzdálené úložiště GitHub:

    ```console
    git push origin master
    ```

    Zobrazí se potvrzení změn v úložišti GitHub *hlavní* větev:

    ![Potvrzení Githubu v hlavní větvi](media/cicd/github-commit.png)

    Sestavení se aktivuje, protože v definici sestavení je povolená Nepřetržitá integrace **triggery** kartu:

    ![Povolit průběžnou integraci](media/cicd/enable-ci.png)

1. Přejděte **zařazeno do fronty** karty **sestavení a vydání** > **sestavení** stránky ve VSTS. Sestavení zařazené do fronty ukazuje větve a potvrzení změn, které aktivuje sestavení:

    ![sestavení zařazené do fronty](media/cicd/build-queued.png)

1. Po úspěšném sestavení, dojde k nasazení do Azure. Přejděte do aplikace v prohlížeči. Všimněte si, že se zobrazí text "V4" v nadpisu:

    ![aktualizovaná aplikace](media/cicd/updated-app-v4.png)

## <a name="examine-the-vsts-devops-pipeline"></a>Prozkoumejte kanálu VSTS DevOps

### <a name="build-definition"></a>Definice sestavení

Definice sestavení byla vytvořena s názvem *MyFirstProject ASP.NET Core-CI*. Po dokončení sestavení vytváří *ZIP* souboru, včetně prostředků má být publikován. Kanál pro vydávání verzí nasadí tyto prostředky do Azure.

Definice sestavení **úlohy** karta obsahuje seznam jednotlivých kroků, které se používají. Existuje pět úloh sestavení.

![definice úlohy sestavení](media/cicd/build-definition-tasks.png)

1. **Obnovení** &mdash; Executes `dotnet restore` příkaz k obnovení balíčků NuGet aplikace. Výchozí balíček informační kanál používá je nuget.org.
1. **Sestavení** &mdash; Executes `dotnet build --configuration Release` příkaz pro kompilaci kódu aplikace. To `--configuration` možnost se používá k vytvoření optimalizované verzi kódu, který je vhodný pro nasazení do produkčního prostředí. Upravit *BuildConfiguration* proměnné na definici sestavení **proměnné** kartu podle potřeby, například konfigurace ladění je.
1. **Test** &mdash; Executes `dotnet test --configuration Release` příkaz pro spuštění testů jednotek aplikace. Pokud selže některý z testů, sestavení selže a není nasazený.

    > [!NOTE]
    > Ověření jednotky jsou testy správně funguje, upravte *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* záměrně přerušení jeden z testů, například tak, že změníte `Assert.True(result.Count > 0);` k `Assert.False(result.Count > 0);` v `Returns_News_Stories_Given_Valid_Uri()` Metoda. Potvrďte a odešlete změny. Sestavení se nezdaří a stav kanálu sestavení se změní na **nepovedlo**. Vrácení změn, potvrzení a nabízených oznámení znovu a sestavení úspěšné.
1. **Publikování** &mdash; Executes `dotnet publish --configuration Release --output <local_path_on_build_agent>` příkazu *ZIP* soubor s artefakty, které mají být nasazeny. `--output` Určuje umístění pro publikování aplikace *ZIP* souboru. Zda je zadáno umístění předáním [předdefinované proměnné](https://docs.microsoft.com/vsts/pipelines/build/variables) s názvem `$(build.artifactstagingdirectory)`. Tato proměnná rozšíří na místní cestu, například *c:\agent\_work\1\a*, agenta sestavení.
1. **Publikování artefaktů** &mdash; Publishes *ZIP* vytvářených souborů **publikovat** úloh. Úloha přijímá *ZIP* umístění jako parametr, což je předdefinovaná proměnná souboru `$(build.artifactstagingdirectory)`. *ZIP* soubor je publikován jako složku s názvem *vyřadit*.

Klikněte na definici sestavení **Souhrn** odkaz k zobrazení historie sestavení s definicí:

![v historii definic sestavení](media/cicd/build-definition-summary.png)

Na stránce výsledný kliknutím na odkaz odpovídající číslu jedinečný sestavení:

![Stránka souhrnu definice sestavení](media/cicd/build-definition-completed.png)

Zobrazí se přehled tohoto konkrétního sestavení. Klikněte na tlačítko **artefakty** kartu a Všimněte si, že *vyřadit* vytvořený sestavením složka se zobrazí:

![definice artefaktů - odkládací složky sestavení](media/cicd/build-definition-artifacts.png)

Použití **Stáhnout** a **prozkoumat** odkazů ke kontrole publikované artefakty.

### <a name="release-pipeline"></a>Kanál pro vydávání verzí

Kanál pro vydávání verzí byl vytvořen s názvem *MyFirstProject ASP.NET Core-CD*:

![Přehled profilace vydaných verzí](media/cicd/release-definition-overview.png)

Jsou dvě hlavní součásti procesu vydávání verzí **artefakty** a **prostředí**. Kliknutím na pole v **artefakty** odhalí panelu následující části:

![kanál artefaktům vydané verze](media/cicd/release-definition-artifacts.png)

**Zdroj (definice sestavení)** hodnota představuje definici sestavení, se kterým je spojen tento kanál pro vydávání verzí. *ZIP* soubor vytvořený úspěšného spuštění definice sestavení se poskytuje *produkční* prostředí pro nasazení do Azure. Klikněte na tlačítko *fáze 1, 2 úlohy* odkaz v *produkční* pole prostředí zobrazíte uvolnění úloh kanálu:

![úkoly uvolnění kanálu](media/cicd/release-definition-tasks.png)

Kanál pro vydávání verzí se skládá ze dvou úloh: *nasazení služby Azure App Service do slotu* a *Správa služby Azure App Service – Prohodit Slot*. Kliknutím na první úkol zobrazí následující konfigurace úlohy:

![Úloha nasazení kanálu pro vydávání verzí](media/cicd/release-definition-task1.png)

Předplatné Azure, typ služby, název webové aplikace, skupiny prostředků a slot pro nasazení jsou definovány v úlohu nasazení. **Balíčku nebo složky** obsahuje textové pole *ZIP* cesta k souboru extrahována a nasazené do *pracovní* pozici *mywebapp\<jedinečný sez_namu posledních použitých\>*  webové aplikace.

Klepnutím na úkol, slot swap, zobrazí se následující konfigurace úlohy:

![verze kanálu slotu prohození úloh](media/cicd/release-definition-task2.png)

Předplatné, skupinu prostředků, typ služby, název webové aplikace a podrobnosti o slot nasazení jsou k dispozici. **Prohodit s produkčním** je zaškrtnuté políčko. V důsledku toho nasazené bity *pracovní* do produkčního prostředí se Prohodit slot.

## <a name="additional-reading"></a>Další čtení

* [Sestavení aplikace ASP.NET Core](https://docs.microsoft.com/vsts/build-release/apps/aspnet/build-aspnet-core)
* [Vytvoření a nasazení do webové aplikace Azure](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp)
* [Definovat proces sestavení CI pro vaše úložiště GitHub](https://docs.microsoft.com/vsts/pipelines/build/ci-build-github)
