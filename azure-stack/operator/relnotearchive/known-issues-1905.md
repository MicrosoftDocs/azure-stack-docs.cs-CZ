---
title: Známé problémy s Azure Stack 1905 | Microsoft Docs
description: Přečtěte si o známých problémech v Azure Stack 1905.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1cd35416188cbd10827dd74fa6ba702d67c675ad
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248160"
---
# <a name="azure-stack-1905-known-issues"></a>Známé problémy s Azure Stack 1905

V tomto článku jsou uvedené známé problémy ve verzi 1905 Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.

## <a name="update-process"></a>Aktualizovat proces

### <a name="host-node-update-prerequisite-failure"></a>Selhání požadovaných součástí aktualizace uzlu hostitele

- Platí: Tento problém se týká aktualizace 1905.
- Příčina: při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace se může stát, že se stav aktualizace nezdařil z důvodu splnění **požadavků na aktualizaci uzlu hostitele**. To je obvykle způsobeno hostitelským uzlem, který má nedostatek volného místa na disku.
- Náprava: obraťte se na podporu Azure Stack, aby bylo možné získat pomoc při mazání místa na disku v uzlu hostitele.
- Výskyt: Neběžné

### <a name="preparation-failed"></a>Příprava se nezdařila

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může dojít k selhání stavu aktualizace a změně stavu na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Balíček aktualizace 1905 je větší než předchozí balíčky aktualizací, což může způsobit, že dojde k většímu problému.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](../azure-stack-apply-updates.md).
- Výskyt: běžné

## <a name="portal"></a>Portál

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

### <a name="marketplace-management"></a>Správa Marketplace

- Platí: Tento problém se týká 1904 a 1905.
- Příčina: obrazovka správy Marketplace se při přihlášení k portálu pro správu nezobrazí.
- Náprava: aktualizujte prohlížeč nebo přejít na **Nastavení** a vyberte možnost **Obnovit výchozí nastavení**.
- Výskyt: občasné

### <a name="docker-extension"></a>Rozšíření Docker

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portále správce i uživatelů v případě, že vyhledáváte **Docker**, se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba.
- Náprava: bez zmírnění.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
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
- Náprava: pomocí PowerShellu, CLI nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-inbound-nat"></a>Vytvoření příchozího překladu adres (NAT)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...**.
- Náprava: pomocí PowerShellu, CLI nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **vytvořit Load Balancer** zobrazí možnost vytvořit **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není v Azure Stack podporována.
- Náprava: použijte místo toho **základní** možnosti nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

### <a name="public-ip-address"></a>Veřejná IP adresa

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **vytvořit veřejnou IP adresu** možnost vytvoření **standardní** SKU. **Standard** SKU není v Azure Stack podporován.
- Náprava: použijte **základní** SKU pro veřejnou IP adresu.
- Výskyt: běžné

## <a name="compute"></a>Výpočetní prostředky

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**.
K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="vm-resize"></a>Změna velikosti virtuálního počítače

- Platí: Tento problém se týká verze 1905.
- Příčina: nepovedlo se úspěšně změnit velikost virtuálního počítače spravovaného disku. Při pokusu o změnu velikosti virtuálního počítače dojde k chybě s "kódem": "InternalOperationError", "Message": "v operaci došlo k vnitřní chybě."
- Náprava: pracujeme na nápravě tohoto problému v další verzi. V současné době musíte virtuální počítač vytvořit znovu s novou velikostí virtuálního počítače.
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

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>Okno zobrazení instance sady škálování se nenačte.

- Platí: Tento problém se týká verze 1904 a 1905.
- Příčina: okno zobrazení instance sady škálování virtuálního počítače, které se nachází na Azure Stack Portalu > řídicím panelu > Virtual Machine Scale Sets – > AnyScaleSet-Instances-> AnyScaleSetInstance se nepovede načíst a zobrazí cloudovou image Crying.
- Náprava: momentálně nedochází k nápravě a pracujeme na opravě. Až pak použijte příkaz CLI, `az vmss get-instance-view` abyste získali zobrazení instance pro sadu škálování.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](../release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](../release-notes-security-updates.md)
