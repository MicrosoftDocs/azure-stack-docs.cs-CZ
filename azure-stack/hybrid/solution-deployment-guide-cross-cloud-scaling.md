---
title: Nasazení aplikace, která škáluje více cloudů pomocí Azure a centra Azure Stack
description: Naučte se, jak nasadit aplikaci, která škáluje více cloudů pomocí Azure a centra Azure Stack.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 54d79742ab39a2732a774401a1765b7a264e1fab
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638450"
---
# <a name="deploy-an-app-that-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>Nasazení aplikace, která škáluje více cloudů pomocí Azure a centra Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack centrum pro vývoj*

Naučte se vytvářet řešení pro více cloudů, aby bylo možné ručně aktivovaný proces přepnutí z webové aplikace hostovaného centra Azure Stack do hostované webové aplikace Azure pomocí automatického škálování prostřednictvím Traffic Manageru. Tento proces zajišťuje flexibilní a škálovatelný cloudový nástroj při zatížení.

V tomto modelu nemusí být váš tenant připravený na spuštění vaší aplikace ve veřejném cloudu. Nemusí ale být hospodářsky proveditelné, aby společnost udržovala kapacitu potřebnou v místním prostředí, aby mohla zpracovávat špičky v poptávce pro aplikaci. Váš tenant může využít pružnost veřejného cloudu s místním řešením.

V tomto řešení sestavíte ukázkové prostředí pro:

> [!div class="checklist"]
> - Vytvořte si webovou aplikaci s více uzly.
> - Nakonfigurujte a spravujte proces průběžného nasazování (CD).
> - Publikujte webovou aplikaci do centra Azure Stack.
> - Vytvořte verzi.
> - Naučte se monitorovat a sledovat vaše nasazení.

> [!Tip]  
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="prerequisites"></a>Předpoklady

-   Předplatné Azure. V případě potřeby vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Azure Stack integrovaným systémem nebo nasazením centra Azure Stack hub Development Kit.
    - Pokyny k instalaci centra Azure Stack najdete v tématu [instalace vývojové sady Azure Stack hub](../asdk/asdk-install.md).
    - ASDK skript pro automatizaci po nasazení přejdete na: [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) . 
    - Dokončení této instalace může trvat několik hodin.

-   Nasaďte [App Service](../operator/azure-stack-app-service-deploy.md) služby PaaS do centra Azure Stack.

-   [Vytvořte plán nebo nabídky](../operator/service-plan-offer-subscription-overview.md) v prostředí Azure Stack hub.

-   [Vytvořte předplatné tenanta](../operator/azure-stack-subscribe-plan-provision-vm.md) v prostředí Azure Stack hub.

-   Vytvořte webovou aplikaci v rámci předplatného tenanta. Poznamenejte si novou adresu URL webové aplikace, abyste ji mohli později použít.

-   Nasaďte Azure Pipelines virtuální počítač (VM) v rámci předplatného tenanta.

-   Vyžaduje se virtuální počítač se systémem Windows Server 2016 s rozhraním .NET 3,5. Tento virtuální počítač bude sestaven v rámci předplatného tenanta na Azure Stack hub jako privátní agent sestavení.

-   [Windows Server 2016 s IMAGÍ SQL 2017 VM](../operator/azure-stack-add-vm-image.md#add-a-vm-image-as-an-azure-stack-operator-using-the-portal) je k dispozici v tržišti Azure Stack hub. Pokud tento obrázek není k dispozici, pracujte s operátorem centra Azure Stack, abyste se ujistili, že je přidaný do prostředí.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

### <a name="scalability"></a>Škálovatelnost

Klíčovou součástí škálování mezi cloudy je schopnost doručovat okamžitou a spolehlivou škálu mezi veřejnou a místní cloudovou infrastrukturou a poskytovat tak konzistentní a spolehlivé služby.

### <a name="availability"></a>Dostupnost

Zajistěte, aby lokálně nasazené aplikace byly nakonfigurované pro vysokou dostupnost prostřednictvím místní konfigurace hardwaru a nasazení softwaru.

### <a name="manageability"></a>Možnosti správy

Řešení mezi cloudy zajišťuje bezproblémové řízení a známé rozhraní mezi prostředími. PowerShell se doporučuje pro správu různých platforem.

## <a name="cross-cloud-scaling"></a>Škálování mezi různými cloudy

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Získání vlastní domény a konfigurace DNS

Aktualizujte soubor zóny DNS pro doménu. Azure AD ověří vlastnictví vlastního názvu domény. Použijte [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) pro Azure/externí záznamy DNS v Azure, nebo přidejte položku DNS v [jiném registrátoru DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Zaregistrujte vlastní doménu s veřejným registrátorem.

2.  Přihlaste se k registrátorovi názvu domény. K provedení aktualizací DNS může být nutný schválený správce.

3.  Aktualizujte soubor zóny DNS pro doménu tak, že přidáte položku DNS, kterou poskytuje Azure AD. (Položka DNS nebude mít vliv na směrování pošty nebo chování webového hostování.)

### <a name="create-a-default-multi-node-web-app-in-azure-stack-hub"></a>Vytvoření výchozí webové aplikace s více uzly v centru Azure Stack

Nastavte hybridní průběžnou integraci a průběžné nasazování (CI/CD), abyste nasadili webové aplikace do Azure a Azure Stack hub a mohli do obou cloudů doručovat změny.

> [!Note]  
> Azure Stack centrum se správnými obrázky publikovanými pro spuštění (Windows Server a SQL) a vyžaduje se nasazení App Service. Další informace najdete v dokumentaci App Service [před zahájením práce s App Service v centru Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

### <a name="add-code-to-azure-repos"></a>Přidat kód pro Azure Repos

Azure Repos

1. Přihlaste se k Azure Repos pomocí účtu, který má práva na vytvoření projektu na Azure Repos.

    Hybridní CI/CD může platit pro kód aplikace i kód infrastruktury. Použijte [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pro vývoj privátního i hostovaného cloudu.

    ![Připojit se k projektu na Azure Repos](media/solution-deployment-guide-cross-cloud-scaling/image1.JPG)

2. **Naklonujte úložiště** vytvořením a otevřením výchozí webové aplikace.

    ![Klonování úložiště ve službě Azure Web App](media/solution-deployment-guide-cross-cloud-scaling/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření samoobslužného nasazení webové aplikace pro App Services v obou cloudech

1.  Upravte soubor **WebApplication. csproj** . Vyberte `Runtimeidentifier` a přidejte `win10-x64`. (Viz dokumentace k [samoobslužnému nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) .) 

    ![Upravit soubor projektu webové aplikace](media/solution-deployment-guide-cross-cloud-scaling/image3.png)

2.  Vrácením kódu se změnami Azure Repos používání Team Explorer.

3.  Ověřte, že je kód aplikace zkontrolovaný Azure Repos.

## <a name="create-the-build-definition"></a>Vytvoření definice sestavení

1. Přihlaste se k Azure Pipelines a potvrďte možnost vytvářet definice sestavení.

2. Přidejte kód **-r Win10-x64** . Tento dodatek je nezbytný pro aktivaci samostatného nasazení pomocí .NET Core.

    ![Přidání kódu do webové aplikace](media/solution-deployment-guide-cross-cloud-scaling/image4.png)

3. Spusťte sestavení. Proces [sestavení samostatného nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) bude publikovat artefakty, které běží v Azure a centra Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Použití hostovaného agenta Azure

Použití hostovaného agenta sestavení v Azure Pipelines je pohodlný způsob pro sestavování a nasazování webových aplikací. Údržba a upgrady se provádí automaticky Microsoft Azure, což umožňuje průběžné a nepřerušované vývojové cykly.

### <a name="manage-and-configure-the-cd-process"></a>Správa a konfigurace procesu CD

Azure Pipelines a Azure DevOps Services poskytují vysoce konfigurovatelný a spravovatelný kanál pro vydání do více prostředí, jako jsou vývojové, pracovní, QA a produkční prostředí. zahrnutí požadavku na schválení v určitých fázích.

## <a name="create-release-definition"></a>Vytvořit definici vydané verze

1.  Kliknutím na tlačítko **plus** přidejte novou verzi na kartě **vydání** v části **sestavení a vydání** VSO.

    ![Vytvoření definice verze](media/solution-deployment-guide-cross-cloud-scaling/image5.png)

2. Použijte šablonu nasazení Azure App Service.

   ![Použít šablonu nasazení Azure App Service](meDia/solution-deployment-guide-cross-cloud-scaling/image6.png)

3. V části **Přidat artefakt**přidejte artefakt pro aplikaci Azure Cloud Build.

   ![Přidání artefaktu do cloudového sestavení Azure](media/solution-deployment-guide-cross-cloud-scaling/image7.png)

4. Na kartě kanál vyberte **fáze,** odkaz na úlohu prostředí a nastavte hodnoty cloudového prostředí Azure.

   ![Nastavení hodnot cloudového prostředí Azure](media/solution-deployment-guide-cross-cloud-scaling/image8.png)

5. Nastavte **Název prostředí** a vyberte **předplatné Azure** pro koncový bod cloudu Azure.

      ![Výběr předplatného Azure pro koncový bod cloudu Azure](media/solution-deployment-guide-cross-cloud-scaling/image9.png)

6. V části **název služby App Service**nastavte požadovaný název služby Azure App Service.

      ![Nastavit název služby Azure App Service](media/solution-deployment-guide-cross-cloud-scaling/image10.png)

7. Do pole **fronta agenta** pro hostované cloudové prostředí Azure zadejte "hostované VS2017".

      ![Nastavení fronty agenta pro hostované cloudové prostředí Azure](media/solution-deployment-guide-cross-cloud-scaling/image11.png)

8. V nabídce nasadit Azure App Service vyberte pro prostředí platný **balíček nebo složku** . Vyberte **OK** do **umístění složky**.
  
      ![Vyberte balíček nebo složku pro Azure App Service prostředí.](media/solution-deployment-guide-cross-cloud-scaling/image12.png)

      ![Vyberte balíček nebo složku pro Azure App Service prostředí.](media/solution-deployment-guide-cross-cloud-scaling/image13.png)

9. Uložte všechny změny a vraťte se do **kanálu uvolnění**.

    ![Uložit změny v kanálu vydání](media/solution-deployment-guide-cross-cloud-scaling/image14.png)

10. Přidejte nový artefakt, který vybírá sestavení pro aplikaci Azure Stack hub.
    
    ![Přidat nový artefakt pro aplikaci Azure Stack hub](media/solution-deployment-guide-cross-cloud-scaling/image15.png)


11. Přidejte další prostředí pomocí Azure App Service nasazení.
    
    ![Přidání prostředí do nasazení Azure App Service](media/solution-deployment-guide-cross-cloud-scaling/image16.png)

12. Pojmenujte nové prostředí Azure Stack.
    
    ![Název prostředí při nasazení Azure App Service](media/solution-deployment-guide-cross-cloud-scaling/image17.png)

13. Na kartě **úloha** Najděte Azure Stack prostředí.
    
    ![Azure Stack prostředí](media/solution-deployment-guide-cross-cloud-scaling/image18.png)

14. Vyberte předplatné pro Azure Stack koncový bod.
    
    ![Vyberte předplatné pro Azure Stack koncový bod.](media/solution-deployment-guide-cross-cloud-scaling/image19.png)

15. Jako název služby App Service nastavte Azure Stack název webové aplikace.

    ![Nastavit Azure Stack název webové aplikace](media/solution-deployment-guide-cross-cloud-scaling/image20.png)

16. Vyberte agenta Azure Stack.
    
    ![Vybrat agenta Azure Stack](media/solution-deployment-guide-cross-cloud-scaling/image21.png)

17. V části nasadit Azure App Service vyberte platný **balíček nebo složku** pro prostředí. Vyberte **OK** do umístění složky.

    ![Vyberte složku pro nasazení Azure App Service](media/solution-deployment-guide-cross-cloud-scaling/image22.png)

    ![Vyberte složku pro nasazení Azure App Service](media/solution-deployment-guide-cross-cloud-scaling/image23.png)

18. V části karta proměnné přidejte proměnnou s názvem `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, nastavte její hodnotu na **true**a rozsah Azure Stack.
    
    ![Přidat proměnnou do nasazení aplikace Azure](media/solution-deployment-guide-cross-cloud-scaling/image24.png)

19. V obou artefaktech vyberte ikonu triggeru **průběžného** nasazování a povolte aktivační událost **pokračování** nasazení.
    
    ![Vybrat aktivační událost průběžného nasazování](media/solution-deployment-guide-cross-cloud-scaling/image25.png)

20. Vyberte ikonu podmínky **před nasazením** v prostředí Azure Stack a nastavte Trigger na **po vydání.**
    
    ![Vybrat podmínky předběžného nasazení](media/solution-deployment-guide-cross-cloud-scaling/image26.png)

21. Uložte všechny změny.

> [!Note]  
> Některá nastavení pro úlohy mohla být při vytváření definice verze ze šablony automaticky definována jako [proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) . Tato nastavení se nedají upravit v nastavení úlohy. místo toho je nutné vybrat nadřazenou položku prostředí pro úpravu těchto nastavení.

## <a name="publish-to-azure-stack-hub-via-visual-studio"></a>Publikování do centra Azure Stack pomocí sady Visual Studio

Vytvořením koncových bodů může sestavení Visual Studio Online (VSTO) nasazovat aplikace služby Azure do centra Azure Stack. Azure Pipelines se připojí k agentu sestavení, který se připojí k centru Azure Stack.

1.  Přihlaste se ke službě VSTO a přejděte na stránku nastavení aplikace.

2.  V **Nastavení**vyberte **zabezpečení**.

3.  V **VSTS skupin**vyberte **Tvůrce koncových bodů**.

4.  Na kartě **Členové** vyberte **Přidat**.

5.  V části **Přidat uživatele a skupiny**zadejte uživatelské jméno a vyberte tohoto uživatele ze seznamu uživatelů.

6.  Vyberte **Uložit změny**.

7.  V seznamu **skupiny VSTS** vyberte možnost **Správci koncových bodů**.

8.  Na kartě **Členové** vyberte **Přidat**.

9.  V části **Přidat uživatele a skupiny**zadejte uživatelské jméno a vyberte tohoto uživatele ze seznamu uživatelů.

10. Vyberte **Uložit změny**.

Teď, když existují informace o koncovém bodu, je Azure Pipelines připojení k rozbočovači Azure Stack připraveno k použití. Agent sestavení v centru Azure Stack získá pokyny od Azure Pipelines a potom agent přenáší informace koncového bodu pro komunikaci s centrem Azure Stack.

## <a name="develop-the-application-build"></a>Vývoj sestavení aplikace

> [!Note]  
> Azure Stack centrum se správnými obrázky publikovanými pro spuštění (Windows Server a SQL) a vyžaduje se nasazení App Service. Další informace najdete v dokumentaci App Service [před zahájením práce s App Service v centru Azure Stack](../operator/azure-stack-app-service-before-you-get-started.md).

K nasazení do obou cloudů použijte [Azure Resource Manager šablony](https://azure.microsoft.com/resources/templates/) , jako je kód webové aplikace z Azure Repos.

### <a name="add-code-to-an-azure-repos-project"></a>Přidat kód do projektu Azure Repos

1.  Přihlaste se k Azure Repos pomocí účtu, který má práva pro vytváření projektů v centru Azure Stack. Následující snímek obrazovky ukazuje, jak se připojit k projektu HybridCICD.

2.  **Naklonujte úložiště** vytvořením a otevřením výchozí webové aplikace.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření samoobslužného nasazení webové aplikace pro App Services v obou cloudech

1.  Upravte soubor **WebApplication. csproj** : vyberte `Runtimeidentifier` a pak přidejte `win10-x64`. Další informace najdete v dokumentaci k [samoobslužnému nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) .

2.  Použijte Team Explorer ke kontrole kódu do Azure Repos.

3.  Potvrďte, že kód aplikace byl zkontrolován Azure Repos.

### <a name="create-the-build-definition"></a>Vytvoření definice sestavení

1.  Přihlaste se k Azure Pipelines pomocí účtu, který může vytvořit definici sestavení.

2.  Přejděte na stránku **sestavení webové aplikace** pro projekt.

3.  V **argumentech**přidejte kód **-r Win10-x64** . Tento dodatek je nutný k aktivaci samostatného nasazení pomocí .NET Core.

4.  Spusťte sestavení. Proces [sestavení samostatného nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) bude publikovat artefakty, které se dají spouštět v Azure a centra Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Použití hostovaného agenta sestavení Azure

Použití hostovaného agenta sestavení v Azure Pipelines je pohodlný způsob pro sestavování a nasazování webových aplikací. Údržba a upgrady se provádí automaticky Microsoft Azure, což umožňuje průběžné a nepřerušované vývojové cykly.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurace procesu průběžného nasazování (CD)

Azure Pipelines a Azure DevOps Services poskytují vysoce konfigurovatelný a spravovatelný kanál pro vydání do více prostředí, jako je vývoj, příprava, kontrola kvality (QA) a produkce. Tento proces může zahrnovat vyžadování schválení v určitých fázích životního cyklu aplikace.

#### <a name="create-release-definition"></a>Vytvořit definici vydané verze

Vytvoření definice verze je posledním krokem v procesu sestavování aplikace. Tato definice verze slouží k vytvoření vydání a nasazení sestavení.

1.  Přihlaste se k Azure Pipelines a přejděte k **sestavení a vydání** pro projekt.

2.  Na kartě **vydané verze** vyberte **[+]** a pak vyberte **vytvořit definici vydané verze**.

3.  V nabídce **Vybrat šablonu**zvolte **Azure App Service nasazení**a pak vyberte **použít**.

4.  V části **Přidat artefakt**ze **zdroje (definice sestavení)** vyberte aplikaci Azure Cloud Build.

5.  Na kartě **kanál** vyberte odkaz **1 fáze**, **1 úloha** a **Zobrazte úlohy prostředí**.

6.  Na kartě **úlohy** zadejte Azure jako **Název prostředí** a vyberte AzureCloud Traders – web EP ze seznamu **předplatných Azure** .

7.  Do následujícího snímku obrazovky zadejte **název služby Azure App Service**, který je `northwindtraders`.

8.  V případě fáze agenta vyberte možnost **hostované VS2017** ze seznamu **fronta agenta** .

9.  V části **nasadit Azure App Service**Vyberte platný **balíček nebo složku** pro prostředí.

10. V **oblasti** **Vybrat soubor nebo složku**vyberte **OK** .

11. Uložte všechny změny a vraťte se zpět do **kanálu**.

12. Na kartě **kanál** vyberte **Přidat artefakt**a ze seznamu **zdroj (definice sestavení)** zvolte **plavidlo NorthwindCloud Traders** .

13. V nabídce **Vybrat šablonu**přidejte další prostředí. Vyberte **nasazení Azure App Service** a pak vyberte **použít**.

14. Jako **Název prostředí**zadejte `Azure Stack Hub`.

15. Na kartě **úlohy** vyhledejte a vyberte centrum Azure Stack.

16. V seznamu **předplatné Azure** vyberte **AzureStack Traders – plavidlo EP** pro koncový bod centra Azure Stack.

17. Jako **název služby App Service**zadejte název webové aplikace centra Azure Stack.

18. V části **Výběr agenta**vyberte v seznamu **fronta agenta** **AzureStack-b Douglas jedle** .

19. Pro **Azure App Service nasazení**vyberte pro prostředí platný **balíček nebo složku** . V části **Vybrat soubor nebo složku**vyberte **OK** pro **umístění**složky.

20. Na kartě **Proměnná** Najděte proměnnou s názvem `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Nastavte hodnotu proměnné na **true**a nastavte její obor na **Azure Stack hub**.

21. Na kartě **kanál** vyberte ikonu **triggeru průběžného nasazování** pro NorthwindCloud Traders – webový artefakt a nastavte **aktivační událost průběžného nasazování** na **povoleno**. Totéž udělejte pro **NorthwindCloud obchodníci – artefakt plavidla** .

22. V prostředí Azure Stack hub vyberte ikonu **podmínky před nasazením** nastavit Trigger na **po vydání**.

23. Uložte všechny změny.

> [!Note]  
> Některá nastavení pro úlohy vydaných verzí se při vytváření definice verze ze šablony automaticky definují jako [proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) . Tato nastavení nelze upravovat v nastavení úlohy, ale lze je upravit v položkách nadřazeného prostředí.

## <a name="create-a-release"></a>Vytvoření vydané verze

1.  Na kartě **kanál** otevřete seznam vydaných **verzí** a vyberte **vytvořit vydání**.

2.  Zadejte popis vydané verze, zkontrolujte, že jsou vybrané správné artefakty, a pak vyberte **vytvořit**. Po chvíli se zobrazí banner s oznámením, že se vytvořila nová vydaná verze a že se název verze zobrazuje jako odkaz. Kliknutím na odkaz zobrazíte stránku se souhrnem vydání.

3.  Na stránce souhrnu vydání najdete podrobnosti o vydané verzi. Na následujícím snímku obrazovky "Release-2" v části **prostředí** se zobrazuje **stav nasazení** pro Azure jako probíhající a stav centra Azure Stack je "úspěch". Když se stav nasazení prostředí Azure změní na úspěšné, zobrazí se informační zpráva s oznámením, že verze je připravená ke schválení. Když nasazení čeká na vyřízení nebo se nezdařilo, zobrazí se ikona s modrou **(i)** informacemi. Když najedete myší na ikonu, zobrazí se automaticky otevírané okno, které obsahuje důvod zpoždění nebo chyby.

4.  Další zobrazení, jako je například seznam vydání, zobrazují také ikonu, která indikuje schválení čeká na vyřízení. Automaticky otevírané okno pro tuto ikonu zobrazuje název prostředí a další podrobnosti týkající se nasazení. Správce může snadno zobrazit celkový průběh vydaných verzí a zjistit, které verze čekají na schválení.

## <a name="monitor-and-track-deployments"></a>Monitorování a sledování nasazení

1.  Na stránce Souhrn **vydání 2** vyberte **protokoly**. Během nasazení se na této stránce zobrazuje živý protokol z agenta. V levém podokně se zobrazuje stav každé operace v nasazení pro každé prostředí.

2.  Vyberte ikonu osoby ve sloupci **Akce** pro schválení před nasazením nebo po nasazení, abyste viděli, kdo toto nasazení schválil (nebo zamítl), a zprávu, kterou poskytli.

3.  Po dokončení nasazení se v pravém podokně zobrazí celý soubor protokolu. V levém podokně vyberte libovolný **Krok** , abyste viděli soubor protokolu pro jeden krok, například **inicializovat úlohu**. Možnost zobrazit jednotlivé protokoly usnadňuje trasování a ladění částí celkového nasazení. **Uložte** soubor protokolu pro krok nebo **stáhněte všechny protokoly jako soubor zip**.

4.  Otevřete kartu **Souhrn** , kde najdete obecné informace o vydané verzi. Toto zobrazení obsahuje podrobnosti o sestavení, prostředích, do kterých byla nasazena, stav nasazení a další informace o vydané verzi.

5.  Vyberte odkaz prostředí (**Azure** nebo **centrum Azure Stack**), ve kterém se zobrazí informace o stávajících a nevyřízených nasazeních do konkrétního prostředí. Tato zobrazení slouží jako rychlý způsob, jak ověřit, že stejné sestavení bylo nasazeno do obou prostředí.

6.  Otevřete **nasazenou produkční aplikaci** v prohlížeči. Například pro web Azure App Services otevřete adresu URL `https://[your-app-name\].azurewebsites.net`.

**Integrace Azure a centra Azure Stack přináší škálovatelné řešení mezi cloudy.**

Flexibilní a robustní cloudová služba poskytuje zabezpečení dat, zálohování a redundanci, konzistentní a rychlé dostupnosti, škálovatelné úložiště a distribuci a směrování vyhovující geografickým požadavkům. Tento ručně aktivovaný proces zajišťuje spolehlivé a efektivní přepínání zatížení mezi hostovanými webovými aplikacemi a okamžitou dostupností důležitých dat.

## <a name="next-steps"></a>Další kroky
- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
