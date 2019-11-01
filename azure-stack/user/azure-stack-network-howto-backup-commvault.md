---
title: Jak zálohovat virtuální počítač v Azure Stack s využitím CommVault | Microsoft Docs
description: Naučte se, jak zálohovat virtuální počítač v Azure Stack pomocí CommVault.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: 428a91503c6943ed1dcc2f48ccad09620d650039
ms.sourcegitcommit: cc5c965b13bc3dae9a4f46a899e602f41dc66f78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73236259"
---
# <a name="back-up-your-vm-on-azure-stack-with-commvault"></a>Zálohování virtuálního počítače na Azure Stack pomocí CommVault

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Přehled zálohování virtuálního počítače pomocí CommVault

Tento článek vás provede konfigurací CommVault Live Sync k aktualizaci virtuálního počítače pro obnovení, který se nachází na samostatné jednotce škálování Azure Stack. Tento článek podrobně popisuje, jak nakonfigurovat společné Partnerské řešení pro ochranu a obnovení dat a stavu systému Virtual Machines nasazených v Azure Stack.

Následující diagram ukazuje celkové řešení při použití CommVault k zálohování virtuálních počítačů.

![](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

V tomto článku se dozvíte, jak:

1. Vytvořte virtuální počítač, na kterém běží CommVault software, na instanci zdrojového Azure Stack.

2. Vytvořte účet úložiště v sekundárním umístění. V článku se předpokládá, že vytvoříte kontejner objektů BLOB v účtu úložiště v instanci služby Azure Stack samostatné (cíl) a že cílový Azure Stack je dosažitelný ze zdrojového Azure Stack.

3. Nakonfigurujte CommVault instance zdrojového Azure Stack a přidejte virtuální počítače do zdrojového Azure Stack do skupiny virtuálních počítačů.

4. Nakonfigurujte LifeSync pro CommVault.

Můžete si také stáhnout a nabídnout kompatibilní image virtuálních počítačů, abyste chránili Vaše Azure Stack virtuálních počítačů do cloudu Azure nebo jiného Azure Stack. Tento článek ilustruje ochranu virtuálních počítačů pomocí CommVault Live Sync.

Topologie tohoto přístupu bude vypadat jako v následujícím diagramu:

![](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.png)

## <a name="create-the-commvault-vm-form-the-commvault-marketplace-item"></a>Vytvoření virtuálního počítače s CommVault ve formuláři CommVault Marketplace

1. Otevřete portál Azure Stack User Portal.

2. Vyberte **vytvořit prostředek** > **COMPUTE** > **CommVault**.

    > [!Note]  
    > Pokud vám CommVault k dispozici, obraťte se na svého operátora cloudu.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Konfigurace základního nastavení v části **vytvořit virtuální počítač, 1 základní informace**:

    a. Zadejte **název**.

    b. Vyberte **standardní HHD**.
    
    c. Zadejte **uživatelské jméno**.
    
    d. Zadejte **heslo**.
    
    e. Potvrďte heslo.
    
    f. Vyberte **předplatné** pro zálohu.
    
    g. Vyberte **skupinu prostředků**.
    
    h. Vyberte **umístění** Azure Stack. Pokud používáte ASDK, vyberte **místní**.
    
    i. Vyberte **OK**.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Vyberte velikost virtuálního počítače s CommVault. Velikost virtuálního počítače pro zálohování by měla být aspoň 10 GB paměti RAM a 100 GB úložiště.

    ![](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Vyberte nastavení pro virtuální počítač CommVault.

    a. Nastavte dostupnost na **žádná**.
    
    b. Vyberte **Ano** , pokud chcete používat spravované disky.
    
    c. Vyberte výchozí virtuální síť pro **virtuální síť**.
    
    d. Vyberte výchozí **podsíť**.
    
    e. Vyberte výchozí **veřejnou IP adresu**.
    
    f. Ponechte virtuální počítač v **základní** skupině zabezpečení sítě.
    
    g. Otevřete porty HTTP (80), HTTPS (443), SSH (22) a RDP (3389).
    
    h. Vyberte **žádná rozšíření**.
    
    i. Pro **diagnostiku spouštění**vyberte **povoleno** .
    
    j. Nechte **diagnostiku hostovaného operačního systému** nastavenou na **zakázáno**.
    
    k. Ponechte výchozí **účet úložiště diagnostiky**.
    
    l. Vyberte **OK**.

6. Zkontrolujte souhrn svého virtuálního počítače s CommVault, až se ověření dokončí. Vyberte **OK**.

## <a name="get-your-service-principal"></a>Získání instančního objektu

Budete potřebovat zjistit, jestli je váš správce identit Azure AD nebo AD DFS. Následující tabulka obsahuje informace, které budete potřebovat k nastavení CommVault v Azure Stack.

| Element | Popis | Zdroj |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Adresa URL Azure Resource Manager | Koncový bod Azure Stack Správce prostředků. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1908#the-azure-stack-resource-manager-endpoint |
| Název aplikace |  |  |
| ID aplikace | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |
| ID předplatného | ID předplatného se používá pro přístup k nabídkám v Azure Stack. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-1908#subscriptions |
| ID klienta (ID adresáře) | Vaše ID tenanta Azure Stack. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview?view=azs-1908 |
| Heslo aplikace | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals?view=azs-1908 |

## <a name="configure-backup-using-the-commvault-console"></a>Konfigurace zálohování pomocí konzoly CommVault

1. Otevřete klienta RDP a připojte se k virtuálnímu počítači Commavult ve svém Azure Stack. Zadejte své přihlašovací údaje.

2. Nainstalujte Azure Stack PowerShell a nástroje Azure Stack na virtuálním počítači s CommVault.

    a. Pokyny k instalaci Azure Stack PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Pokyny k instalaci nástrojů Azure Stack najdete v tématu [Stažení nástrojů Azure Stack z GitHubu](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Po instalaci nástroje CommVault na VIRTUÁLNÍm počítači s CommVault otevřete konzolu Commcell. V nabídce Start vyberte **Commvault** > **CommVault Commcell Console**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Nakonfigurujte úložiště záloh tak, aby používalo úložiště mimo Azure Stack v konzole Commcell pro CommVault. V prohlížeči CommCell vyberte prostředky úložiště > fondy úložišť. Klikněte pravým tlačítkem a vyberte **Přidat fond úložiště.** Vyberte **Cloud**.

5. Přidejte název fondu úložiště. Vyberte **Další**.

6. Vyberte **vytvořit** > **cloudové úložiště**.

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Vyberte poskytovatele cloudové služby. V tomto postupu budeme používat druhý Azure Stack v jiném umístění. Vyberte Microsoft Azure Storage.

8. Jako svůj MediaAgent vyberte svůj virtuální počítač s CommVault.

9. Zadejte informace o přístupu pro svůj účet úložiště. Pokyny k nastavení Azure Storage účtu najdete tady. Přístup k informacím:

    -  **Hostitel služby**: Získá název adresy URL z vlastností kontejneru objektů BLOB ve vašem prostředku. Například moje adresa URL byla https://backuptest.blob.westus.stackpoc.com/mybackups a používá se blob.westus.stackpoc.com v hostiteli služby.
    
    -   **Název účtu**: použijte název účtu úložiště. Najdete ho v okně přístupové klíče v prostředku úložiště.
    
    -   **Přístupový klíč**: Získejte přístupový klíč z okna přístupové klíče v prostředku úložiště.
    
    -   **Container**: název kontejneru. V tomto případě mybackups.
    
    -   **Třída úložiště**: ponechte jako výchozí třídu úložiště kontejneru uživatele.

10. Vytvořte klienta Microsoft Azure Stack podle pokynů v [tématu Vytvoření klienta Microsoft Azure Stack](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm)

    ![](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Vyberte virtuální počítače nebo skupiny prostředků pro ochranu a připojení zásady zálohování.

12. Nakonfigurujte plán zálohování tak, aby odpovídal vašim požadavkům na RPO pro obnovení.

13. Proveďte první úplnou zálohu.

## <a name="configure-commvault-live-sync"></a>Konfigurovat živou synchronizaci CommVault 

K dispozici jsou dvě možnosti. Můžete se rozhodnout replikovat změny z primární kopie záloh nebo replikovat změny ze sekundární kopie do virtuálního počítače pro obnovení. Replikace ze zálohovacího skladu eliminuje dopad čtení v/v na zdrojovém počítači.

1. Během konfigurace živé synchronizace bude nutné zadat zdrojovou Azure Stack (agenta virtuálního serveru) a podrobnosti cíle Azure Stack.

2. Postup konfigurace CommVault Live Sync najdete v tématu [replikace za provozu pro Microsoft Azure Stack](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm).

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Během konfigurace živé synchronizace bude potřeba zadat podrobnosti o cílovém Azure Stack a agentovi virtuálního serveru.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Pokračujte v konfiguraci a přidejte cílový účet úložiště, kde budou hostované disky repliky, skupiny prostředků, kde se budou virtuální počítače repliky umístit, a název, který chcete připojit k virtuálním počítačům repliky.

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. Můžete také změnit velikost virtuálního počítače a nakonfigurovat nastavení sítě výběrem možnosti **Konfigurovat** vedle každého virtuálního počítače.

6. Nastavte četnost replikace na cílovou Azure Stack

    ![](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Zkontrolujte nastavení a uložte konfiguraci. Prostředí pro obnovení se pak vytvoří a replikace se spustí ve zvoleném intervalu.


## <a name="set-up-failover-behavior-using-live-sync"></a>Nastavení chování při selhání pomocí živé synchronizace

CommVault Live Sync umožňuje převzetí služeb při selhání z jednoho Azure Stack do jiného a navrácení služeb po obnovení pro obnovení operací na původním Azure Stack. Pracovní postup je automatizovaný a protokolovaný.

![](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Vyberte virtuální počítače, u kterých chcete převzít služby při selhání Azure Stack obnovení, a zvolte plánované nebo neplánované převzetí služeb při selhání. Plánované převzetí služeb při selhání je vhodné v případě, že je čas na bezproblémové vypnutí provozního prostředí před obnovením operací v lokalitě pro obnovení. Při plánovaném převzetí služeb při selhání dojde k vypnutí produkčních virtuálních počítačů, replikují se poslední změny lokality pro obnovení a přinášejí virtuální počítače pro obnovení do online režimu s nejnovějšími daty a uplatní velikost virtuálního počítače a konfiguraci sítě zadanou během konfigurace živé synchronizace. Neplánované převzetí služeb při selhání se pokusí vypnout provozní virtuální počítače, ale bude pokračovat, pokud je provozní prostředí nedostupné a jednoduše přepnete virtuální počítače pro obnovení do režimu online s poslední obdrženou replikační datovou sadou, která se používá pro virtuální počítač a velikost a síť. dříve zvolená konfigurace. Následující obrázky znázorňují neplánované převzetí služeb při selhání, kdy se virtuální počítače pro obnovení dostaly do online režimu CommVault Live Sync.

![](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  