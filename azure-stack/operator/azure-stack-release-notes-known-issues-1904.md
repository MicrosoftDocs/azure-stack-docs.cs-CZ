---
title: Známé problémy s Azure Stack 1904 | Dokumentace Microsoftu
description: Přečtěte si o známých problémech v Azure stacku 1904.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 70d6cfae2eb6e9508e2141aa59c6986ec8817d11
ms.sourcegitcommit: d1fdecdfa843dfc0629bfc226f1baf14f3ea621d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387718"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904 známé problémy

V tomto článku najdete seznam známých problémů v 1904 verzi služby Azure Stack. V seznamu je aktualizovat, protože nové problémy jsou označeny.

> [!IMPORTANT]  
> V této části najdete před instalací této aktualizace.

## <a name="update-process"></a>Proces aktualizace

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace služby Azure Stack, stav aktualizace může selhat a změnu stavu na **PreparationFailed**. To je způsobeno aktualizace poskytovateli prostředků (URP) není schopen správně přenosu souborů z kontejneru úložiště do sdílené složky interní infrastruktury pro zpracování.
- Náprava: Počínaje verzí 1901 (1.1901.0.95), můžete alternativně vyřešit tento problém kliknutím **aktualizovat** znovu (ne **Resume**). URP potom vyčistí soubory z předchozího pokusu o a znovu spustí stahování.
- Výskyt: Společné

## <a name="portal"></a>Portál

### <a name="subscription-resources"></a>Prostředky předplatného

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Odstraňuje se předplatné uživatele za následek osamocené prostředky.
- Náprava: Nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.
- Výskyt: Společné

### <a name="subscription-permissions"></a>Oprávnění pro předplatné

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack.
- Náprava: Použití [Powershellu mohl ověřit oprávnění](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: Společné

### <a name="docker-extension"></a>Rozšíření docker

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálech správce i uživatele, pokud budete hledat **Docker**, položky se nesprávně vrátí. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se chyba.
- Náprava: Žádné zmírňující opatření.
- Výskyt: Společné

### <a name="marketplace-management"></a>Správa webu Marketplace

- Použít: Jedná se o nový problém s verzí 1904.
- Příčina: Na obrazovce správy marketplace není viditelný, při přihlášení k portálu správce.
- Náprava: Aktualizujte prohlížeč.
- Výskyt: Občasně

### <a name="marketplace-management"></a>Správa webu Marketplace

- Použít: Tento problém se týká 1904.
- Příčina: Při filtrování výsledků v **přidat z Azure** okno v kartě správu webu Marketplace na portálu správce se může zobrazit nesprávné filtrované výsledky.
- Náprava: Výsledky řazení podle sloupce název a výsledky budou opraveny.
- Výskyt: Občasně

### <a name="marketplace-management"></a>Správa webu Marketplace

- Použít: Tento problém se týká 1904.
- Příčina: Při filtrování výsledků v Marketplace správu na portálu pro správce, zobrazí se duplicitní vydavatele názvy vydavatele rozevíracího seznamu. 
- Náprava: Vyberte všechny duplicitní položky mít správný seznam všech produktů Marketplace, které jsou k dispozici v rámci daného vydavatele.
- Výskyt: Občasně

### <a name="docker-extension"></a>Rozšíření docker

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálech správce i uživatele, pokud budete hledat **Docker**, položky se nesprávně vrátí. Není k dispozici ve službě Azure Stack. Pokud se pokusíte k jeho vytvoření, zobrazí se chyba.
- Náprava: Žádné zmírňující opatření.
- Výskyt: Společné

### <a name="upload-blob"></a>Nahrát objekt blob

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal při pokusu o nahrání objektu blob pomocí možnosti OAuth(preview), úloha se nezdaří s chybovou zprávu.
- Náprava: Nahrajte objekt blob pomocí možnosti SAS.
- Výskyt: Společné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-endový fond

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte přidat **back-endový fond** k **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Azure Resource Manageru můžete přidružit back-endový fond prostředků nástroje pro vyrovnávání zatížení.
- Výskyt: Společné

#### <a name="create-inbound-nat"></a>Vytvoření příchozích pravidel NAT

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte vytvořit **příchozí pravidlo NAT** pro **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Azure Resource Manageru můžete přidružit back-endový fond prostředků nástroje pro vyrovnávání zatížení.
- Výskyt: Společné

#### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit Load Balancer** okno zobrazuje možnost pro vytvoření **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není podporována ve službě Azure Stack.
- Náprava: Místo toho použijte možnosti nástroje pro vyrovnávání zatížení základní.
- Výskyt: Společné

#### <a name="public-ip-address"></a>Veřejná IP adresa

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit veřejnou IP adresu** okno zobrazuje možnost pro vytvoření **standardní** SKU. **Standardní** SKU se nepodporuje v Azure stacku.
- Náprava: Použití **základní** SKU místo pro veřejné IP adresy.
- Výskyt: Společné

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření virtuálního počítače pro nové Windows (VM), může se zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač 'název_virtuálního_počítače'. Chyba: Nepovedlo se aktualizovat nastavení sériového portu výstup pro virtuální počítač 'název_virtuálního_počítače'** .
Pokud jste povolení diagnostiky spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě.
- Náprava: Znovu vytvořte účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: Společné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="centos"></a>CentOS

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Prostředí pro vytváření virtuálního počítače škálovací sady založené na CentOS 7.2 poskytuje jako možnost pro nasazení. CentOS 7.2 není k dispozici ve službě Azure Stack.
- Náprava: Vyberte jiný operační systém pro vaše nasazení, nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.
- Výskyt: Společné

#### <a name="remove-scale-set"></a>Odstranit škálovací sady

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze odebrat škálovací sady z **Virtual Machine Scale Sets** okno.
- Náprava: Vyberte škálovací sady, že chcete odebrat, klikněte **odstranit** tlačítko **přehled** podokně.
- Výskyt: Společné

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytvoření chyby během opravy a aktualizace v prostředích Azure Stack 4 uzly

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvořit škálovací sadu virtuálních počítačů nastavte instance selže a zobrazí se **FabricVmPlacementErrorUnsupportedFaultDomainSize** chyba během procesu aktualizace ve službě Azure Stack 4 uzly prostředí.
- Náprava: Můžete vytvořit jednotlivých virtuálních počítačů ve skupině dostupnosti s 2 domén selhání byl úspěšně nastaven. Vytváření instance škálovací sady je však stále nejsou k dispozici během procesu aktualizace ve službě Azure Stack 4 uzly.

### <a name="ubuntu-ssh-access"></a>Přístup SSH se systémem Ubuntu

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení.
- Náprava: Přístup k virtuálním počítačům pro rozšíření Linuxu použít k implementaci klíče SSH po zřízení, nebo použít ověřování pomocí hesla.
- Výskyt: Společné

### <a name="compute-host-agent-alert"></a>COMPUTE upozornění agenta hostitele

- Použít: Jedná se o nový problém s verzí 1904.
- Příčina: A **agent hostitele výpočetního** upozornění se zobrazí po restartování uzlu v jednotce škálování. Restartování se změní výchozí nastavení spuštění pro výpočetní služby hostitele agenta. Tato výstraha vypadá podobně jako v následujícím příkladu:

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- Náprava:
  - Toto upozornění můžete ignorovat. Agent neodpovídá nemá žádný vliv na operátor a operace uživatelů nebo uživatelské aplikace. Výstraha se znovu za 24 hodin propojení se zavře, ručně.
  - Problém je vyřešen v nejnovější aktualizaci [Azure Stack oprava hotfix pro 1904](https://support.microsoft.com/help/4505688).
- Výskyt: Společné

### <a name="virtual-machine-scale-set-instance-view"></a>Zobrazení instance virtuálního počítače škálovací sady

- Použít: Tento problém se vztahuje na verze 1904 byl a 1905.
- Příčina: V okně zobrazení instance škálovací sady se nachází na portálu Azure Stack v **řídicí panel** > **škálovací sady virtuálních počítačů**  >   **Instance AnyScaleSet -**  > **AnyScaleSetInstance** nepodaří načíst.
- Náprava: Aktuálně nejsou k dispozici žádná možnost nápravy a pracujeme na opravě. Dokud to neuděláte, použijte prosím rutinu rozhraní příkazového řádku `az vmss get-instance-view` získat zobrazení instance škálovací sady virtuálních počítačů.

## <a name="storage"></a>Úložiště

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) není podporováno ve službě Azure Stack a má za následek vytvoření disku s **$null** ID. Předchází se tak provedení operace na virtuálním počítači, jako je například spouštění a zastavování. Disk se nezobrazí v uživatelském rozhraní ani ho zřejmě prostřednictvím rozhraní API. Virtuální počítač v tomto okamžiku nelze opravit a musí být odstraněny.
- Náprava: Chcete-li převést vaše disky správně, postupujte [převést na spravované disky průvodce](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- Tenanti musí zaregistrovat zprostředkovatele prostředku úložiště před vytvořením své první funkce Azure v rámci předplatného.
- Některé tenanta portálu uživatelské prostředí jsou v 1903; přerušeno z důvodu nekompatibility s rozhraní portálu hlavně uživatelského rozhraní pro sloty nasazení, testování v produkčním prostředí a lokality rozšíření. Chcete-li tento problém obejít, použijte [modulu Powershellu pro Azure App Service](/azure/app-service/deploy-staging-slots#automate-with-powershell) nebo [rozhraní příkazového řádku Azure](/cli/azure/webapp/deployment/slot?view=azure-cli-latest). Obnoví prostředí portálu upgradujte nasazení [služby Azure App Service ve službě Azure Stack na 1.6 (aktualizací 6)](azure-stack-app-service-release-notes-update-six.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další postup

- [Kontrolní seznam pro revizi aktualizace aktivity](azure-stack-release-notes-checklist.md)
- [Zkontrolujte seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1904.md)
