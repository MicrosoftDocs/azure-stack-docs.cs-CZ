---
title: Známé problémy s Azure Stack 1905 | Dokumentace Microsoftu
description: Přečtěte si o známých problémech v Azure stacku 1905.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: b79c40b26735184d7a0b0501a5ef6f4a1354a375
ms.sourcegitcommit: 75b13158347963063b7ee62b0ec57894b542c1be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66748932"
---
# <a name="azure-stack-1905-known-issues"></a>Azure Stack 1905 známé problémy

V tomto článku najdete seznam známých problémů v 1905 verzi služby Azure Stack. V seznamu je aktualizovat, protože nové problémy jsou označeny.

> [!IMPORTANT]  
> V této části najdete před instalací této aktualizace.

## <a name="update-process"></a>Proces aktualizace

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace služby Azure Stack 1905, stav aktualizace může selhat a změnu stavu na **PreparationFailed**. To je způsobeno aktualizace poskytovateli prostředků (URP) není schopen správně přenosu souborů z kontejneru úložiště do sdílené složky interní infrastruktury pro zpracování. Balíček aktualizace 1905 je větší než předchozí balíčky aktualizací, které můžou způsobit, že tento problém pravděpodobně probíhat.
- Náprava: Počínaje verzí 1901 (1.1901.0.95), můžete alternativně vyřešit tento problém kliknutím **aktualizovat** znovu (ne **Resume**). URP potom vyčistí soubory z předchozího pokusu o a zahájí stahování. Pokud se problém nevyřeší, doporučujeme, abyste ručně pomocí následujících nahrání balíčku aktualizace [importu a nainstalovat aktualizace části](azure-stack-apply-updates.md#import-and-install-updates).
- Výskyt: Společné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Pro správu předplatných

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Obě předplatná pro správu, které byly představeny s nástrojem verzi 1804 se nesmí používat. Typy předplatného jsou **měření** předplatného, a **spotřeby** předplatného.
- Náprava: Tato předplatná se pozastaví, počínaje. 1906 a nakonec odstraněné. Pokud máte prostředky spuštěné na tyto dva odběry služeb, je znovu vytvořte v předplatných uživatele před. 1906.
- Výskyt: Společné

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

### <a name="upload-blob"></a>Nahrát objekt blob

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, při pokusu nahrát objekt blob pomocí **OAuth(preview)** možnost, úloha se nezdaří s chybovou zprávou.
- Náprava: Nahrajte objekt blob pomocí možnosti SAS.
- Výskyt: Společné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-endový fond

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte přidat **back-endový fond** k **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Resource Manageru pro přidružení k prostředku nástroje pro vyrovnávání zatížení back-endový fond.
- Výskyt: Společné

#### <a name="create-inbound-nat"></a>Vytvoření příchozích pravidel NAT

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte vytvořit **příchozí pravidlo NAT** pro **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Resource Manageru pro přidružení k prostředku nástroje pro vyrovnávání zatížení back-endový fond.
- Výskyt: Společné

#### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit Load Balancer** okno zobrazuje možnost pro vytvoření **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není podporována ve službě Azure Stack.
- Náprava: Použití **základní** místo toho možnostech nástroje pro vyrovnávání zatížení.
- Výskyt: Společné

### <a name="public-ip-address"></a>Veřejná IP adresa

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit veřejnou IP adresu** okno zobrazuje možnost pro vytvoření **standardní** SKU. **Standardní** SKU se nepodporuje v Azure stacku.
- Náprava: Použití **základní** SKU pro veřejnou IP adresu.
- Výskyt: Společné

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření nového virtuálního počítače (VM) Windows, může se zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač 'název_virtuálního_počítače'. Chyba: Nepovedlo se aktualizovat nastavení sériového portu výstup pro virtuální počítač 'název_virtuálního_počítače'** .
Pokud jste povolení diagnostiky spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě.
- Náprava: Znovu vytvořte účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: Společné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="centos"></a>CentOS

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Prostředí pro vytváření virtuálního počítače škálovací sady založené na CentOS 7.2 poskytuje jako možnost pro nasazení. CentOS 7.2 není k dispozici na Azure Marketplace zásobníku, což způsobí selhání nasazení volání, že obrázek nebyl nalezen.
- Náprava: Vyberte jiný operační systém pro vaše nasazení, nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.
- Výskyt: Společné

#### <a name="remove-scale-set"></a>Odstranit škálovací sady

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze odebrat škálovací sady z **škálovací sady virtuálních počítačů** okno.
- Náprava: Vyberte škálovací sady, že chcete odebrat, klikněte **odstranit** tlačítko **přehled** podokně.
- Výskyt: Společné

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytvoření chyby během opravy a aktualizace v prostředích Azure Stack 4 uzly

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvořit škálovací sadu virtuálních počítačů nastavte instance selže a zobrazí se **FabricVmPlacementErrorUnsupportedFaultDomainSize** chyba během procesu aktualizace ve službě Azure Stack 4 uzly prostředí.
- Náprava: Můžete vytvořit jednotlivých virtuálních počítačů ve skupině dostupnosti s 2 domén selhání byl úspěšně nastaven. Vytváření instance škálovací sady je však stále nejsou k dispozici během procesu aktualizace ve službě Azure Stack 4 uzly.

#### <a name="vmss-instance-view-blade-doesnt-load"></a>VMSS Instance zobrazení okna se nenačte.
 
- Použít: Tento problém se týká verzí 1904 byl a 1905.
- Příčina: Okně zobrazení instance škálovací sady na portálu Azure Stack -> řídicí panel -> Virtual machine scale sets -> AnyScaleSet - instance -> AnyScaleSetInstance nezdaří se načtení s Plačící cloudu.
- Náprava: Aktuálně nejsou k dispozici žádná možnost nápravy a pracujeme na opravě. Dokud to neuděláte použijte prosím rozhraní příkazového řádku rutiny az vmss get-instance-view získat zobrazení instance VMSS

### <a name="ubuntu-ssh-access"></a>Přístup SSH se systémem Ubuntu

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení.
- Náprava: Přístup k virtuálním počítačům pro rozšíření Linuxu použít k implementaci klíče SSH po zřízení, nebo použít ověřování pomocí hesla.
- Výskyt: Společné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další postup

- [Kontrolní seznam pro revizi aktualizace aktivity](azure-stack-release-notes-checklist.md)
- [Zkontrolujte seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1905.md)
