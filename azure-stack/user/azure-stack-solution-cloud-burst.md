---
title: Vytvoření řešení aplikací pro škálování cloudu s Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit řešení škálování cloudu s Azure.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: scenario
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1bbe22bdc78f28aa172c90770cd5d2857878d2c3
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286920"
---
# <a name="create-cross-cloud-scaling-app-solutions-with-azure-and-azure-stack"></a>Vytvoření řešení aplikací pro škálování cloudu s Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak různé cloudové řešení, které najdete postup, ručně aktivované pro přepnutí z webové aplikace Azure Stack hostované na Azure hostované webové aplikace s automatickým Škálováním pomocí traffic Manageru. Tento proces zajišťuje flexibilní a škálovatelná Cloudová nástroj při zátěži.

V tomto modelu možná není připravený ke spuštění vaší aplikace ve veřejném cloudu vašeho tenanta. Ale nemusí být ekonomicky vhodná pro firmy a udržovat kapacita požadovaná pro zpracování accurately špiček v poptávce aplikace v jejich v místním prostředí. Svého tenanta mohli využít elasticitu služby veřejného cloudu s vlastním řešením v místním.

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření několika uzly webové aplikace.
> - Konfigurovat a spravovat proces průběžného nasazování (CD).
> - Publikování webové aplikace do služby Azure Stack.
> - Vytvoření vydané verze.
> - Zjistěte, jak monitorovat a sledovat vaše nasazení.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

-   Předplatné Azure. V případě potřeby vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před zahájením.

- Integrovaný systém Azure Stack nebo nasazení Azure Stack Development Kit.
    - Pokyny k instalaci služby Azure Stack najdete v tématu [instalace sady Azure Stack Development Kit](../asdk/asdk-install.md).
    - Pro skript po nasazení služby automation ASDK přejděte na: [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) 
    - Tato instalace může vyžadovat několik hodin.

-   Nasazení [služby App Service](../operator/azure-stack-app-service-deploy.md) služeb PaaS do služby Azure Stack.

-   [Vytvoření plánu a nabídky](../operator/azure-stack-plan-offer-quota-overview.md) v rámci prostředí Azure Stack.

-   [Vytvoření tenanta předplatného](../operator/azure-stack-subscribe-plan-provision-vm.md) v rámci prostředí Azure Stack.

-   Vytvoření webové aplikace v rámci předplatného tenanta. Poznamenejte si nový URL webové aplikace pro později použít.

-   Nasazení Azure kanály virtuálního počítače (VM) v rámci předplatného tenanta.

-   Vyžaduje se virtuální počítač s Windows serverem 2016 pomocí rozhraní .NET 3.5. Tento virtuální počítač bude vytvořen v rámci předplatného tenanta ve službě Azure Stack jako privátní sestavovacího agenta.

-   [Windows Server 2016 s Image virtuálního počítače SQL 2017](../operator/azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) je dostupný v Tržišti Azure Stack. Pokud se tento obrázek není k dispozici, fungují s Azure Stack obsluze Ujistěte se, že se přidá do prostředí.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

### <a name="scalability"></a>Škálovatelnost

Klíčovou součástí škálování cloudu je schopnost poskytovat okamžitý a podle potřeby škálování mezi veřejné a místní infrastrukturu cloudu, poskytuje konzistentní a spolehlivé služby.

### <a name="availability"></a>Dostupnost

Zajištění místně nasazených aplikací jsou nakonfigurované pro vysokou dostupnost v místním hardwaru konfigurací a nasazování softwaru.

### <a name="manageability"></a>Možnosti správy

Řešení cloudu zajišťuje, že Bezproblémová Správa a známému rozhraní mezi prostředími. Prostředí PowerShell se doporučuje pro různé platformy správy.

## <a name="cross-cloud-scaling"></a>Škálování cloudu

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Získat vlastní doménu a konfigurace DNS

Aktualizace souboru zóny DNS pro doménu. Azure AD ověří vlastnictví vlastního názvu domény. Použití [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) Azure nebo Office 365/externí záznamů DNS v rámci Azure, nebo přidejte položku DNS na [různých registrátora DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Zaregistrujte vlastní doménu veřejného registru.

2.  Přihlaste se k registrátorovi názvu domény. Schválené správcem, může být nutné provést aktualizace DNS.

3.  Aktualizace souboru zóny DNS pro doménu tak, že přidáte položku DNS poskytuje Azure AD. (Položka DNS neovlivní směrování pošty nebo webhosting chování.)

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Vytvořit výchozí několikauzlovými webovou aplikaci ve službě Azure Stack

Nastavení hybridní průběžné integrace a průběžného nasazování (CI/CD) nasazení webové aplikace Azure a Azure Stack a automatické nabízené oznámení změny pro oba cloudy.

> [!Note]  
> Azure Stack pomocí správné imagí syndikovat do běhu (Windows Server a SQL) a nasazení služby App Service jsou povinné. Další informace najdete v dokumentaci k App Service [před zahájením práce s App Service ve službě Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

### <a name="add-code-to-azure-repos"></a>Přidání kódu do úložiště Azure

Azure Repos

1. Přihlaste se k úložišti Azure pomocí účtu, který má práva k vytvoření projektu na úložiště Azure.

    Hybridní CI/CD můžete použít kód aplikace a kódu infrastruktury. Použití [šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/) i privátní a prostředí pro vývoj pro cloud.

    ![Připojte se k projektu na úložiště Azure](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

    ![Naklonujte úložiště ve službě Azure web app](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření nasazení samostatné webové aplikace pro App Service v oba cloudy

1.  Upravit **WebApplication.csproj** souboru. Vyberte `Runtimeidentifier` a přidejte `win10-x64`. (Viz [Self-contained nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentaci.) 

    ![Upravit soubor projektu webové aplikace](media/azure-stack-solution-cloud-burst/image3.png)

2.  Vrátit se změnami kódu do úložiště Azure pomocí Team Exploreru.

3.  Potvrďte, že po kontrole kódu aplikace do úložiště Azure.

## <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1. Přihlaste se k Azure kanály potvrďte schopnost vytvářet definice sestavení.

2. Přidat **- r win10-x64** kódu. Toto přidání je potřeba aktivovat samostatná nasazení s .NET Core.

    ![Přidání kódu do webové aplikace](media/azure-stack-solution-cloud-burst/image4.png)

3. Spuštění sestavení. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) procesu budete publikovat artefakty, které běží na Azure a Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Použití Azure hostovaných agentů

Pomocí agenta sestavení hostované v kanálech Azure je vhodná možnost vytvářet a nasazovat webové aplikace. Údržba a upgrady se provádějí automaticky na Microsoft Azure, takže průběžné a bez přerušení vývojového cyklu.

### <a name="manage-and-configure-the-cd-process"></a>Spravovat a konfigurovat proces průběžného Doručování

Azure kanály a Azure DevOps Server poskytují vysoce konfigurovatelné a spravovatelné kanálu pro vydané verze do více prostředí, jako je vývoj, Fázování importu, dotazů a odpovědí a produkční prostředí; včetně, která vyžadují schválení určitým fázím.

## <a name="create-release-definition"></a>Vytvořte definici vydané verze

1.  Vyberte **plus** tlačítko pro přidání nové vydané verze v části **verze** kartu **sestavení a vydání** část VSO.

    ![Vytvoření definice verze](media/azure-stack-solution-cloud-burst/image5.png)

2. Použijte šablonu nasazení služby Azure App Service.

   ![Použít šablonu nasazení služby Azure App Service](meDia/azure-stack-solution-cloud-burst/image6.png)

3. V části **přidání artefaktu**, přidejte artefakt sestavení aplikace cloudu Azure.

   ![Přidání artefaktu sestavení cloudu Azure](media/azure-stack-solution-cloud-burst/image7.png)

4. Na kartě kanálu, vyberte **fáze, úloha** odkaz prostředí a nastavte hodnoty prostředí cloudu Azure.

   ![Nastavte hodnoty prostředí cloudu Azure](media/azure-stack-solution-cloud-burst/image8.png)

5. Nastavte **název prostředí** a vyberte **předplatného Azure** pro koncový bod cloudu Azure.

      ![Vyberte předplatné Azure pro koncový bod v cloudu Azure](media/azure-stack-solution-cloud-burst/image9.png)

6. V části **název služby App service**, nastavte název požadované Azure app service.

      ![Název sady Azure app service](media/azure-stack-solution-cloud-burst/image10.png)

7. Zadejte "Hostované VS2017" v části **frontu agenta** pro prostředí Azure hostované v cloudu.

      ![Nastavit frontu agenta pro prostředí Azure hostované v cloudu](media/azure-stack-solution-cloud-burst/image11.png)

8. V nabídce nasazení služby Azure App Service, vyberte platnými **balíčku nebo složky** pro prostředí. Vyberte **OK** k **umístění složky**.
  
      ![Vyberte balíček nebo složky pro Azure App Service environment](media/azure-stack-solution-cloud-burst/image12.png)

      ![Vyberte balíček nebo složky pro Azure App Service environment](media/azure-stack-solution-cloud-burst/image13.png)

9. Uložte všechny změny a vraťte se do **kanál pro vydávání verzí**.

    ![Uložení změn v kanálu pro vydávání verzí](media/azure-stack-solution-cloud-burst/image14.png)

10. Přidání nové artefaktu výběr sestavení pro aplikaci služby Azure Stack.
    
    ![Přidat nový artefaktů pro aplikaci služby Azure Stack](media/azure-stack-solution-cloud-burst/image15.png)


11. Přidejte jeden další prostředí s použitím nasazení služby Azure App Service.
    
    ![Přidání prostředí do nasazení služby Azure App Service](media/azure-stack-solution-cloud-burst/image16.png)

12. Název nového prostředí Azure Stack.
    
    ![Název prostředí v nasazení služby Azure App Service](media/azure-stack-solution-cloud-burst/image17.png)

13. Najít prostředí Azure Stack v rámci **úloh** kartu.
    
    ![Prostředí Azure Stack](media/azure-stack-solution-cloud-burst/image18.png)

14. Vyberte předplatné, pro koncový bod služby Azure Stack.
    
    ![Vyberte předplatné, pro koncový bod služby Azure Stack](media/azure-stack-solution-cloud-burst/image19.png)

15. Nastavte název webové aplikace služby Azure Stack jako název služby App service.

    ![Název webové aplikace pomocí sady Azure Stack](media/azure-stack-solution-cloud-burst/image20.png)

16. Vyberte agenta služby Azure Stack.
    
    ![Vyberte agenta služby Azure Stack](media/azure-stack-solution-cloud-burst/image21.png)

17. V části nasazení služby Azure App Service vyberte platnými **balíčku nebo složky** pro prostředí. Vyberte **OK** do umístění složky.

    ![Vyberte složku pro nasazení služby Azure App Service](media/azure-stack-solution-cloud-burst/image22.png)

    ![Vyberte složku pro nasazení služby Azure App Service](media/azure-stack-solution-cloud-burst/image23.png)

18. Na kartě proměnné přidejte proměnnou `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, nastavte ho na hodnotu jako **true**a obor do služby Azure Stack.
    
    ![Přidejte proměnnou k nasazení aplikace Azure](media/azure-stack-solution-cloud-burst/image24.png)

19. Vyberte **průběžné** ikona aktivační události nasazení v artefakty a povolit **Continues** aktivační události nasazení.
    
    ![Vyberte trigger průběžného nasazování](media/azure-stack-solution-cloud-burst/image25.png)

20. Vyberte **před nasazením** ikonu podmínky v prostředí Azure Stack a nastavte aktivační události na **po vydání.**
    
    ![Vybrat podmínky před nasazením](media/azure-stack-solution-cloud-burst/image26.png)

21. Uložte všechny změny.

> [!Note]  
> Některá nastavení pro úlohy může automaticky definovaná jako [proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) při vytváření definice vydané verze ze šablony. Tato nastavení nelze změnit v nastavení úkolu; Místo toho musí být vybrána položka prostředí nadřazené upravit tato nastavení.

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publikování do služby Azure Stack prostřednictvím sady Visual Studio

Tím, že vytvoříte koncové body, Visual Studio Online (VSTO) build aplikace Azure Service nasadit do služby Azure Stack. Kanály Azure připojuje k agenta sestavení, který se připojuje ke službě Azure Stack.

1.  Přihlaste se k VSTO a přejděte na stránku nastavení aplikací.

2.  Na **nastavení**vyberte **zabezpečení**.

3.  V **skupiny VSTS**vyberte **koncový bod Creators**.

4.  Na **členy** kartu, vyberte možnost **přidat**.

5.  V **přidávat uživatele a skupiny**, zadejte uživatelské jméno a vyberte uživatele ze seznamu uživatelů.

6.  Vyberte **uložit změny**.

7.  V **skupiny VSTS** seznamu vyberte **koncový bod správci**.

8.  Na **členy** kartu, vyberte možnost **přidat**.

9.  V **přidávat uživatele a skupiny**, zadejte uživatelské jméno a vyberte uživatele ze seznamu uživatelů.

10. Vyberte **uložit změny**.

Teď, když existuje informace o koncovém bodu Azure kanály pro připojení služby Azure Stack je připravený k použití. Agent sestavení ve službě Azure Stack získá pokyny z kanály Azure a pak agenta přenáší informace o koncovém bodu pro komunikaci pomocí služby Azure Stack.

## <a name="develop-the-application-build"></a>Vývoj aplikace sestavení

> [!Note]  
> Azure Stack pomocí správné imagí syndikovat do běhu (Windows Server a SQL) a nasazení služby App Service jsou povinné. Další informace najdete v dokumentaci k App Service [před zahájením práce s App Service ve službě Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

Použití [šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/) kód webové aplikace z úložiště Azure k nasazení pro oba cloudy, jako je.

### <a name="add-code-to-an-azure-repos-project"></a>Přidejte kód do úložiště Azure projektu

1.  Přihlaste se k úložišti Azure pomocí účtu, který má práva k vytvoření projektu ve službě Azure Stack. Následující snímek obrazovky ukazuje, jak se připojit k projektu HybridCICD.

2.  **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření nasazení samostatné webové aplikace pro App Service v oba cloudy

1.  Upravit **WebApplication.csproj** souboru: Vyberte `Runtimeidentifier` a pak přidejte `win10-x64`. Další informace najdete v tématu [samostatná nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentaci.

2.  Zkontrolujte kód do úložiště Azure pomocí Team Exploreru.

3.  Potvrďte, že kód aplikace se změnami do úložiště Azure.

### <a name="create-the-build-definition"></a>Vytvořte definici sestavení

1.  Přihlaste se k Azure kanály pomocí účtu, který můžete vytvořit definici sestavení.

2.  Přejděte **sestavit webovou aplikaci** stránky pro projekt.

3.  V **argumenty**, přidejte **- r win10-x64** kódu. Toto přidání je potřeba aktivovat samostatná nasazení s .NET Core.

4.  Spuštění sestavení. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) procesu budete publikovat artefakty, které lze spustit v Azure a Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Použití Azure hostovaný agent sestavení

Pomocí agenta sestavení hostované v kanálech Azure je vhodná možnost vytvářet a nasazovat webové aplikace. Údržba a upgrady se provádějí automaticky na Microsoft Azure, takže průběžné a bez přerušení vývojového cyklu.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurace procesu průběžného nasazování (CD)

Azure kanály a Azure DevOps Server poskytují vysoce konfigurovatelné a spravovatelné kanálu pro vydané verze do více prostředí, jako je vývoj, pracovní, kontrola kvality (dotazů a odpovědí) a provoz. Tento proces může obsahovat, která vyžadují schválení určitým fázím životního cyklu aplikací.

#### <a name="create-release-definition"></a>Vytvořte definici vydané verze

Vytvoření definice vydané verze je posledním krokem v aplikaci procesu sestavení. Tato definice vydané verze se používá k vytvoření vydané verze a nasazení sestavení.

1.  Přihlaste se k Azure kanály a přejděte do **sestavení a vydání** pro projekt.

2.  Na **verze** kartu, vyberte možnost **[+]** a potom si vyberte **definice vydané verze vytvořit**.

3.  Na **vyberte šablonu**, zvolte **nasazení služby Azure App Service**a pak vyberte **použít**.

4.  Na **přidání artefaktu**, z **zdroj (definice sestavení)** , vyberte aplikaci sestavení cloudu Azure.

5.  Na **kanálu** kartu, vyberte možnost **1 fáze**, **1 úloha** propojit **zobrazit úlohy prostředí**.

6.  Na **úlohy** kartu, zadejte jako Azure **název prostředí** a vyberte EP Traders webové AzureCloud z **předplatného Azure** seznamu.

7.  Zadejte **název služby Azure app service**, což je `northwindtraders` dalšího snímku obrazovky.

8.  Fáze agenta vyberte **hostované VS2017** z **frontu agenta** seznamu.

9.  V **nasazení služby Azure App Service**, vyberte platnými **balíčku nebo složky** pro prostředí.

10. V **vybrat soubor nebo složku**vyberte **OK** k **umístění**.

11. Uložte všechny změny a vraťte se do **kanálu**.

12. Na **kanálu** kartu, vyberte možnost **přidání artefaktu**a zvolte **NorthwindCloud Traders-lodi** z **zdroj (definice sestavení)** seznamu.

13. Na **vyberte šablonu**, přidat jiné prostředí. Vyberte si **nasazení služby Azure App Service** a pak vyberte **použít**.

14. Zadejte `Azure Stack` jako **název prostředí**.

15. Na **úlohy** kartu, vyhledejte a vyberte Azure Stack.

16. Z **předplatného Azure** seznamu vyberte **AzureStack Traders lodi EP** pro koncový bod služby Azure Stack.

17. Zadejte název webové aplikace služby Azure Stack jako **název služby App service**.

18. V části **Výběr agenta**, vyberte **AzureStack -b Douglas Fir** z **frontu agenta** seznamu.

19. Pro **nasazení služby Azure App Service**, vyberte platnými **balíčku nebo složky** pro prostředí. Na **vybrat soubor nebo složku**vyberte **OK** složky **umístění**.

20. Na **proměnnou** kartu, vyhledejte proměnnou s názvem `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Nastavte hodnotu proměnné **true**a nastavte jeho rozsah **Azure Stack**.

21. Na **kanálu** kartu, vyberte možnost **trigger průběžného nasazování** ikonu pro artefakt NorthwindCloud Traders – Web a nastavte **trigger průběžného nasazování** do **Povolené**. Stejnou věc udělat **NorthwindCloud Traders-lodi** artefaktů.

22. Prostředí Azure Stack, vyberte **podmínky před nasazením** ikony nastavte aktivační události na **po vydání**.

23. Uložte všechny změny.

> [!Note]  
> Některá nastavení pro uvolnění úloh jsou automaticky definována jako [proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) při vytváření definice vydané verze ze šablony. Tato nastavení nelze změnit v nastavení úloh, ale lze upravit v nadřazených položek prostředí.

## <a name="create-a-release"></a>Vytvoření vydané verze

1.  Na **kanálu** otevřenou kartou **Release** seznam a vyberte **vytvořit vydání**.

2.  Zadejte popis pro vydání, zkontrolujte, zda jsou vybrány správné artefakty a pak vyberte **vytvořit**. Po chvíli se zobrazí banner s označující, že byla vytvořena nová verze a verze název se zobrazí jako odkaz. Vyberte na odkaz zobrazíte na stránce souhrnu vydání.

3.  Na stránce souhrnu vydání ukazuje podrobnosti o verzi. Na následujícím snímku obrazovky pro "Release-2" **prostředí** části ukazuje **stav nasazení** pro Azure jako "Probíhající" a stav pro službu Azure Stack je "bylo DOKONČENO". Kdy se stav nasazení pro prostředí Azure změní na "ÚSPĚCH", zobrazí se banner označující, že verze je připravené ke schválení. Při nasazení čeká na vyřízení nebo se nezdařila, modrý **(i)** informační ikona, která se zobrazí. Najeďte myší na ikonu si zobrazíte automaticky otevírané okno, které obsahuje důvodem zpoždění nebo selhání.

4.  Jiných zobrazení, jako je například seznam verzí, také zobrazit ikonu, která označuje schválení čeká na vyřízení. Automaticky otevírané okno pro tato ikona zobrazuje název prostředí a další podrobnosti související s nasazením. Je snadné správce naleznete v části celkový průběh vydaných verzí a zjistěte, která verze se čeká na schválení.

## <a name="monitor-and-track-deployments"></a>Monitorování a sledování nasazení

1.  Na **vydaná verze 2** souhrnné stránce **protokoly**. Během nasazení Tato stránka zobrazuje v za provozu protokolu z agenta. V levém podokně se zobrazí stav jednotlivých operací v nasazení pro každé prostředí.

2.  Vyberte ikonu osoba **akce** sloupec o schválení před nasazením nebo po nasazení a podívat se, kdo schválit (ani odmítnout) nasazení zprávy jsou k dispozici.

3.  Po dokončení nasazení se v pravém podokně zobrazí celý soubor protokolu. Vyberte některou **krok** v levém podokně naleznete v souboru protokolu pro jeden krok, jako například **úlohy inicializace**. Možnost zobrazit jednotlivé protokoly usnadňuje trasování a ladění součástí celkové nasazení. **Uložit** soubor protokolu pro krok, nebo **stáhnout všechny protokoly jako soubor zip**.

4.  Otevřít **Souhrn** kartu a zobrazí se obecné informace o verzi. Toto zobrazení ukazuje údaje o sestavení, prostředí, který byl nasazen na, stav nasazení a další informace o verzi.

5.  Vyberte odkaz na prostředí (**Azure** nebo **Azure Stack**) zobrazíte informace o existujících a čeká se na nasazení do konkrétního prostředí. Pomocí těchto zobrazení jako rychlý způsob, jak ověřit, že stejný build nasadila do obou prostředích.

6.  Otevřít **nasadili aplikaci v produkčním prostředí** v prohlížeči. Například otevřít adresu URL pro web Azure App Services `https://[your-app-name\].azurewebsites.net`.

**Integrace Azure a Azure Stack nabízí škálovatelné řešení v cloudu**

Flexibilní a robustní více cloudové služby poskytuje zabezpečení dat, zpět nahoru a redundance, konzistentní a rychlé dostupnosti, škálovatelné úložiště a distribuci a CLS geografické směrování. Tento proces ručně aktivované zajišťuje spolehlivé a efektivní zatížení přepínání mezi hostované webové aplikace a okamžitou dostupnost důležitá data.

## <a name="next-steps"></a>Další postup
- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
