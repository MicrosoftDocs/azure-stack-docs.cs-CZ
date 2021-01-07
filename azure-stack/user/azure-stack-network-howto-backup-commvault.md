---
title: Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault
description: Naučte se, jak zálohovat virtuální počítač v Azure Stack hub pomocí CommVault.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 0d5b48c04e546d3b8776f436e5562786b8e77f33
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974128"
---
# <a name="back-up-your-vm-on-azure-stack-hub-with-commvault"></a>Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault

## <a name="overview-of-backing-up-a-vm-with-commvault"></a>Přehled zálohování virtuálního počítače pomocí CommVault

Tento článek vás provede konfigurací CommVault Live Sync k aktualizaci virtuálního počítače pro obnovení, který se nachází na samostatné jednotce škálování centra Azure Stack. Tento článek podrobně popisuje, jak nakonfigurovat společné Partnerské řešení pro ochranu a obnovení dat a stavu systému Virtual Machines nasazených v centru Azure Stack.

Následující diagram ukazuje celkové řešení při použití CommVault k zálohování virtuálních počítačů.

![Diagram ukazuje, jak lze CommVault použít k replikaci dat ze služby Azure Stack do jiného zásobníku nebo do cloudu Azure.](./media/azure-stack-network-howto-backup-commvault/bcdr-commvault-overall-arc.png)

V tomto článku se dozvíte, jak:

1. Vytvořte virtuální počítač, na kterém běží CommVault software, na instanci zdrojového centra Azure Stack.

2. Vytvořte účet úložiště v sekundárním umístění. V článku se předpokládá, že vytvoříte kontejner objektů BLOB v účtu úložiště v instanci služby Azure Stack hub samostatně (cíl) a že je cílový Azure Stack hub dosažitelný ze zdrojového Azure Stack centra.

3. Nakonfigurujte CommVault na instanci centra zdrojového Azure Stack a přidejte virtuální počítače ve zdrojovém Azure Stack centru do skupiny virtuálních počítačů.

4. Nakonfigurujte živou synchronizaci CommVault.

Můžete si také stáhnout a nabídnout kompatibilní image virtuálních počítačů, abyste chránili virtuální počítače centra Azure Stack do cloudu Azure nebo jiného centra Azure Stack. Tento článek ilustruje ochranu virtuálních počítačů pomocí CommVault Live Sync.

Topologie tohoto přístupu bude vypadat jako v následujícím diagramu:

![Diagram zobrazuje cestu k datům z COMMVAULT dodavatelem VSA ve službě Azure Stack hub 1 až Azure Stack hub 2, který má virtuální počítač pro obnovení, který je možné uvést online v případě potřeby k zálohování centra 1.](./media/azure-stack-network-howto-backup-commvault/backup-vm-commvault-diagram.svg)

## <a name="create-the-commvault-vm-from-the-commvault-marketplace-item"></a>Vytvoření virtuálního počítače s CommVault z položky Marketplace pro CommVault

1. Otevřete portál Azure Stack hub User Portal.

2. Vyberte **vytvořit prostředek**  >  **COMPUTE**  >  **CommVault**.

    > [!NOTE]  
    > Pokud vám CommVault k dispozici, obraťte se na svého operátora cloudu.

    ![Vytvoření virtuálního počítače](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-01.png)

3. Konfigurace základního nastavení v části **vytvořit virtuální počítač, 1 základní informace**:

    a. Zadejte **název**.

    b. Vyberte **HDD úrovně Standard**.
    
    c. Zadejte **uživatelské jméno**.
    
    d. Zadejte **heslo**.
    
    e. Potvrďte své heslo.
    
    f. Vyberte **předplatné** pro zálohu.
    
    například Vyberte **skupinu prostředků**.
    
    h. Vyberte **umístění** centra Azure Stack. Pokud používáte ASDK, vyberte **místní**.
    
    i. Vyberte **OK**.

    ![Řídicí panel > nové > vytvořit virtuální počítač > zvolit velikost zobrazuje seznam možností velikosti pro virtuální počítač.](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-02.png)

4. Vyberte velikost virtuálního počítače s CommVault. Velikost virtuálního počítače pro zálohování by měla být aspoň 10 GB paměti RAM a 100 GB úložiště.

    ![Dialogová okna > nové > vytvoření > nastavení virtuálního počítače se zobrazí v dialogovém okně pro vytvoření virtuálního počítače.](./media/azure-stack-network-howto-backup-commvault/commvault-create-vm-03.png).

5. Vyberte nastavení pro virtuální počítač CommVault.

    a. Nastavte dostupnost na **žádná**.
    
    b. Vyberte **Ano** , pokud chcete používat spravované disky.
    
    c. Vyberte výchozí virtuální síť pro **virtuální síť**.
    
    d. Vyberte výchozí **podsíť**.
    
    e. Vyberte výchozí **veřejnou IP adresu**.
    
    f. Ponechte virtuální počítač v **základní** skupině zabezpečení sítě.
    
    například Otevřete porty HTTP (80), HTTPS (443), SSH (22) a RDP (3389).
    
    h. Vyberte **žádná rozšíření**.
    
    i. Pro **diagnostiku spouštění** vyberte **povoleno** .
    
    j. Nechte **diagnostiku hostovaného operačního systému** nastavenou na **zakázáno**.
    
    k. Ponechte výchozí **účet úložiště diagnostiky**.
    
    l. Vyberte **OK**.

6. Zkontrolujte souhrn svého virtuálního počítače s CommVault, až se ověření dokončí. Vyberte **OK**.

## <a name="get-your-service-principal"></a>Získání instančního objektu

Budete potřebovat zjistit, jestli je váš správce identit Azure AD nebo AD FS. Následující tabulka obsahuje informace, které budete potřebovat k nastavení CommVault v centru Azure Stack.

| Element | Popis | Zdroj |
|--------------------------|--------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Adresa URL Azure Resource Manager | Koncový bod Správce prostředků centra Azure Stack. | https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint |
| Název aplikace |  |  |
| ID aplikace | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals |
| ID předplatného | ID předplatného se používá pro přístup k nabídkám v centru Azure Stack. | https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview#subscriptions |
| ID klienta (ID adresáře) | Vaše ID tenanta centra Azure Stack. | https://docs.microsoft.com/azure-stack/operator/azure-stack-identity-overview |
| Heslo aplikace | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby. | https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals |

## <a name="configure-backup-using-the-commvault-console"></a>Konfigurace zálohování pomocí konzoly CommVault

1. Otevřete svého klienta RDP a připojte se k virtuálnímu počítači s Commavult v centru Azure Stack. Zadejte svoje přihlašovací údaje.

2. Nainstalujte Azure Stack centra PowerShell a nástroje centra Azure Stack na virtuálním počítači s CommVault.

    a. Pokyny k instalaci prostředí PowerShell centra Azure Stack najdete v tématu [instalace PowerShellu pro centrum Azure Stack](../operator/powershell-install-az-module.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).  
    b. Pokyny k instalaci nástrojů centra Azure Stack najdete v tématu [Stažení nástrojů centra Azure Stack z GitHubu](../operator/azure-stack-powershell-download.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json%3Fview%3Dazs-1908&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json%3Fview%3Dazs-1908&view=azs-1908).

3. Po instalaci nástroje CommVault na VIRTUÁLNÍm počítači s CommVault otevřete konzolu Commcell. V nabídce Start vyberte **CommVault**  >  **CommVault Commcell Console**.

    ![Konzola Commcell má na levé straně navigační podokno s názvem Commcell Browser. V pravém podokně se zobrazí Začínáme Stránka s kartami.](./media/azure-stack-network-howto-backup-commvault/commcell-console.png)

4. Nakonfigurujte úložiště záloh tak, aby používalo úložiště externě v konzole služby Azure Stack v konzole CommVault Commcell. V prohlížeči CommCell vyberte prostředky úložiště > fondy úložišť. Klikněte pravým tlačítkem a vyberte **Přidat fond úložiště.** Vyberte **Cloud**.

5. Přidejte název fondu úložiště. Vyberte **Další**.

6. Vyberte **vytvořit**  >  **cloudové úložiště**.

    ![V dialogovém okně StorageDevice # se zobrazí stránka Obecné s kartami s různými seznamy a textovými poli pro určení úložného zařízení, které se má vytvořit.](./media/azure-stack-network-howto-backup-commvault/commcell-storage-add-storage-device.png)

7. Vyberte poskytovatele cloudové služby. V tomto postupu použijeme druhé centrum Azure Stack v jiném umístění. Vyberte Microsoft Azure Storage.

8. Jako svůj MediaAgent vyberte svůj virtuální počítač s CommVault.

9. Zadejte informace o přístupu pro svůj účet úložiště. Pokyny k nastavení Azure Storage účtu najdete tady. Přístup k informacím:

    -  **Hostitel služby**: Získá název adresy URL z vlastností kontejneru objektů BLOB ve vašem prostředku. Například moje adresa URL byla https: \/ /backuptest.blob.westus.stackpoc.com/mybackups a používá se BLOB.westus.stackpoc.com v hostiteli služby.
    
    -   **Název účtu**: použijte název účtu úložiště. Najdete ho v okně přístupové klíče v prostředku úložiště.
    
    -   **Přístupový klíč**: Získejte přístupový klíč z okna přístupové klíče v prostředku úložiště.
    
    -   **Container**: název kontejneru. V tomto případě mybackups.
    
    -   **Třída úložiště**: ponechte jako výchozí třídu úložiště kontejneru uživatele.

10. Vytvořte klienta Microsoft Azure Stack Hub podle pokynů v [tématu Vytvoření klienta Microsoft Azure Stack hub](https://documentation.commvault.com/commvault/v11_sp13/article?p=86495.htm) .

    ![Dialogové okno vytvořit klienta Azure Stack obsahuje seznam a textová pole pro určení charakteristik klienta.](./media/azure-stack-network-howto-backup-commvault/commcell-ceate-client.png)

11. Vyberte virtuální počítače nebo skupiny prostředků pro ochranu a připojení zásady zálohování.

12. Nakonfigurujte plán zálohování tak, aby odpovídal vašim požadavkům na RPO pro obnovení.

13. Proveďte první úplnou zálohu.

## <a name="configure-commvault-live-sync"></a>Konfigurovat živou synchronizaci CommVault 

K dispozici jsou dvě možnosti. Můžete se rozhodnout replikovat změny z primární kopie záloh nebo replikovat změny ze sekundární kopie do virtuálního počítače pro obnovení. Replikace ze zálohovacího skladu eliminuje dopad čtení v/v na zdrojovém počítači.

1. Během konfigurace živé synchronizace bude nutné poskytnout zdrojové Azure Stack centra (agenta virtuálního serveru) a podrobnosti o cílovém Azure Stack centru.

2. Postup konfigurace CommVault Live Sync najdete v tématu [replikace za provozu pro centrum Microsoft Azure Stack](https://documentation.commvault.com/commvault/v11_sp13/article?p=94386.htm).

    ![V konzole Commcell se zobrazuje stránka "VM-kr-CVLT > klientských počítačích > ASIC Azure Stack > Virtual Server > Azure Stack > defaultBackupSet". Místní nabídka pro vypnutou ochranu zásobníku na stránce má možnost konfigurace živé synchronizace >.](./media/azure-stack-network-howto-backup-commvault/live-sync-1.png)
 
3. Během konfigurace živé synchronizace bude nutné zadat podrobnosti cílového centra Azure Stack a agenta virtuálního serveru.

    ![V tomto kroku jsou uvedeny seznamy pro určení klienta virtualizace a klienta proxy serveru v poli cíl možnosti synchronizace pro klienta mimo Průvodce ochranou zásobníku.](./media/azure-stack-network-howto-backup-commvault/live-sync-2.png)

4. Pokračujte v konfiguraci a přidejte cílový účet úložiště, kde budou hostované disky repliky, skupiny prostředků, kde se budou virtuální počítače repliky umístit, a název, který chcete připojit k virtuálním počítačům repliky.

    ![Virtual Machines kroku možnosti synchronizace v reálném čase pro klienta mimo Průvodce ochranou zásobníku umožňuje přidat a odebrat virtuální počítače.](./media/azure-stack-network-howto-backup-commvault/live-sync-3.png)

5. Můžete také změnit velikost virtuálního počítače a nakonfigurovat nastavení sítě výběrem možnosti  **Konfigurovat** vedle každého virtuálního počítače.

6. Nastavení četnosti replikace do cílového centra Azure Stack

    ![Krok možností úlohy v části Možnosti synchronizace pro klienta mimo Průvodce ochranou zásobníku je určen pro zadání plánu zálohování.](./media/azure-stack-network-howto-backup-commvault/live-sync-5.png)

7. Zkontrolujte nastavení a uložte konfiguraci. Prostředí pro obnovení se pak vytvoří a replikace se spustí ve zvoleném intervalu.


## <a name="set-up-failover-behavior-using-live-sync"></a>Nastavení chování při selhání pomocí živé synchronizace

CommVault Live Sync umožňuje převzetí služeb při selhání z jednoho centra Azure Stack do jiného a navrácení služeb po obnovení pro obnovení operací v původním centru Azure Stack. Pracovní postup je automatizovaný a protokolovaný.

![Stránka monitorování replikace v konzole pro správu zobrazuje dostupná data pro různé podpodoknoi podokna bodu obnovení replikace. V podokně sledování replikace jsou uvedené dva virtuální počítače. Pro každý z nich je k dispozici řádek s informacemi o replikaci.](./media/azure-stack-network-howto-backup-commvault/back-up-live-sync-panel.png)

Vyberte virtuální počítače, u kterých chcete převzít služby při selhání pro obnovení Azure Stack hub, a zvolte plánované nebo neplánované převzetí služeb při selhání. Plánované převzetí služeb při selhání je vhodné v případě, že je čas na bezproblémové vypnutí provozního prostředí před obnovením operací v lokalitě pro obnovení. Při plánovaném převzetí služeb při selhání dojde k vypnutí produkčních virtuálních počítačů, replikují se poslední změny lokality pro obnovení a přinášejí virtuální počítače pro obnovení do online režimu s nejnovějšími daty a uplatní velikost virtuálního počítače a konfiguraci sítě zadanou během konfigurace živé synchronizace. Neplánované převzetí služeb při selhání se pokusí vypnout provozní virtuální počítače, ale bude pokračovat, pokud je provozní prostředí nedostupné a jednoduše přepnete virtuální počítače pro obnovení do režimu online s poslední obdrženou replikační datovou sadou, která se použila pro virtuální počítač, a výše vybranou velikost a konfiguraci sítě. Následující obrázky znázorňují neplánované převzetí služeb při selhání, kdy se virtuální počítače pro obnovení dostaly do online režimu CommVault Live Sync.

![V části Souhrn úlohy se zobrazují informace o události zotavení po havárii, včetně typu, priority, "času spuštění" a "koncového času".](./media/azure-stack-network-howto-backup-commvault/unplanned-failover.png)

![Seznam s názvem události zobrazuje jednu událost, která je popsána jako "úloha orchestrace DR byla dokončena." Pro tuto událost jsou k dispozici další informace.](./media/azure-stack-network-howto-backup-commvault/fail-over-2.png)

![Seznam s názvem podrobnosti fáze zobrazuje šest událostí pro čtyři počítače. Pro každý má název fáze, stav, čas spuštění a čas ukončení. Názvy fází jsou vypnutí, zapnutí, vypnutí synchronizace a operace post.](./media/azure-stack-network-howto-backup-commvault/fail-over-3.png)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
