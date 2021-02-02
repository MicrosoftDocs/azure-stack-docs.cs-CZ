---
title: Známé problémy s Azure Stack 1904 | Microsoft Docs
description: Přečtěte si o známých problémech v Azure Stack 1904.
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
ROBOTS: NOINDEX
ms.openlocfilehash: f977c1671468257ac205c494f33ad46965f1f2eb
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248163"
---
# <a name="azure-stack-1904-known-issues"></a>Známé problémy s Azure Stack 1904

V tomto článku jsou uvedené známé problémy ve verzi 1904 Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.

## <a name="update-process"></a>Aktualizovat proces

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a znovu spustí stahování.
- Výskyt: běžné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

### <a name="subscription-resources"></a>Prostředky předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při odstraňování předplatných uživatelů dojde k osamoceným prostředkům.
- Náprava: nejprve odstraňte prostředky uživatele nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="docker-extension"></a>Rozšíření Docker

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portále správce i uživatelů v případě, že vyhledáváte **Docker**, se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba.
- Náprava: bez zmírnění.
- Výskyt: běžné

### <a name="marketplace-management"></a>Správa Marketplace

- Platí: Jedná se o nový problém s verzí 1904.
- Příčina: obrazovka správy Marketplace se při přihlášení k portálu pro správu nezobrazí.
- Náprava: aktualizujte prohlížeč.
- Výskyt: občasné

### <a name="marketplace-management"></a>Správa Marketplace

- Platí: Tento problém se týká 1904.
- Příčina: když filtrujete výsledky v okně **Přidat z Azure** na kartě Správa Marketplace na portálu pro správu, můžou se zobrazit nesprávné filtrované výsledky.
- Náprava: Seřadí výsledky podle sloupce název a výsledky se opraví.
- Výskyt: občasné

### <a name="marketplace-management"></a>Správa Marketplace

- Platí: Tento problém se týká 1904.
- Příčina: když filtrujete výsledky správy Marketplace na portálu pro správu, zobrazí se v rozevíracím seznamu vydavateli duplicitní názvy vydavatelů. 
- Náprava: výběrem všech duplicitních položek zobrazíte správný seznam všech produktů Marketplace, které jsou v tomto vydavateli k dispozici.
- Výskyt: občasné

### <a name="docker-extension"></a>Rozšíření Docker

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portále správce i uživatelů v případě, že vyhledáváte **Docker**, se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba.
- Náprava: bez zmírnění.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti OAuth (Preview) na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

### <a name="template"></a>Template (Šablona)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal uživatelské rozhraní nasazení šablony neplní parametry pro názvy šablon začínající znakem "_" (znak podtržítka).
- Oprava: z názvu šablony odeberte znak "_" (podtržítko).
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-end fond

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte přidat **back-end fond** do **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...**.
- Náprava: pomocí PowerShellu, CLI nebo šablony Azure Resource Manager přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-inbound-nat"></a>Vytvoření příchozího překladu adres (NAT)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...**.
- Náprava: pomocí PowerShellu, CLI nebo šablony Azure Resource Manager přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **vytvořit Load Balancer** zobrazí možnost vytvořit **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není v Azure Stack podporována.
- Náprava: použijte místo toho základní možnosti nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="public-ip-address"></a>Veřejná IP adresa

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **vytvořit veřejnou IP adresu** možnost vytvoření **standardní** SKU. **Standard** SKU není v Azure Stack podporován.
- Oprava: místo toho použijte pro veřejné IP adresy **základní** SKU.
- Výskyt: běžné

## <a name="compute"></a>Výpočetní prostředky

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**.
K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="centos"></a>CentOS

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: prostředí pro vytváření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není na Azure Stack Marketplace k dispozici, což způsobí selhání nasazení při volání, že se image nenajde.
- Náprava: vyberte jiný operační systém pro nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.
- Výskyt: běžné

#### <a name="remove-scale-set"></a>Odebrat sadu škálování

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: sadu škálování nejde odebrat z okna **Virtual Machine Scale Sets** .
- Náprava: vyberte sadu škálování, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit** v podokně **Přehled** .
- Výskyt: běžné

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="compute-host-agent-alert"></a>Výstraha agenta hostitele COMPUTE

- Platí: Jedná se o nový problém s verzí 1904.
- Příčina: po restartování uzlu v jednotce škálování se zobrazí upozornění **agenta pro výpočet hostitele** . Restart změní výchozí nastavení spuštění služby COMPUTE Host agent. Tato výstraha vypadá podobně jako v následujícím příkladu:

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

- Nápravy
  - Tato výstraha může být ignorována. Agent nereaguje, nemá žádný vliv na operace operátora a uživatele nebo uživatelské aplikace. Pokud je tato výstraha zavřena ručně, zobrazí se po 24 hodinách.
  - Tento problém je opravený v nejnovější [Azure Stack opravě hotfix pro 1904](https://support.microsoft.com/help/4505688).
- Výskyt: běžné

### <a name="virtual-machine-scale-set-instance-view"></a>Zobrazení instance sady škálování virtuálních počítačů

- Platí: Tento problém se týká verzí 1904 a 1905.
- Příčina: v okně zobrazení instance sady škálování na Azure Stackovém portálu se v **řídicím panelu**  >  **Virtual Machine Scale Sets**  >  **AnyScaleSet-Instances**  >  **AnyScaleSetInstance** nepovede načíst.
- Náprava: momentálně nedochází k nápravě a pracujeme na opravě. Až pak použijte rutinu CLI, `az vmss get-instance-view` abyste získali zobrazení instance pro sadu škálování virtuálního počítače.

### <a name="user-image-service"></a>Služba image uživatele
- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nepodařilo se vytvořit bitovou kopii uživatele, která je v nesprávném stavu. Operace vytváření a odstraňování uživatelských imagí začnou selhat. Odstranění bitové kopie uživatele může selhat s chybou: "Chyba: došlo k interní chybě správy disku."
- Náprava: bez zmírnění. Otevřete lístek podpory s Microsoftem.

## <a name="storage"></a>Storage

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) není v Azure Stack podporován a má za následek vytvoření disku s ID **$null** . Tím zabráníte provádění operací na virtuálním počítači, jako je spuštění a zastavení. Disk se nezobrazuje v uživatelském rozhraní, ani se nezobrazuje přes rozhraní API. Virtuální počítač v tomto okamžiku nejde opravit a musí se odstranit.
- Náprava: Pokud chcete disky správně převést, postupujte podle [příručky převést na spravované disky](../../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- Klienti musí zaregistrovat poskytovatele prostředků úložiště před vytvořením první funkce Azure v předplatném.
- V důsledku nekompatibility s portálem v 1903 se některé uživatelské prostředí portálu klienta přerušilo. v podstatě i v uživatelském prostředí pro sloty nasazení, testování v produkčním prostředí a rozšířeních lokality. Pokud chcete tento problém obejít, použijte [modul Azure App Service PowerShell](/azure/app-service/deploy-staging-slots#automate-with-powershell) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true). Prostředí portálu se obnoví upgradem nasazení [Azure App Service v Azure Stack na 1,6 (Update 6)](../azure-stack-app-service-release-notes-update-six.md).

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](../release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](../release-notes-security-updates.md)
