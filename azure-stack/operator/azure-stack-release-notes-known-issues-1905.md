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
monikerRange: azs-1905
ms.openlocfilehash: f5fdabf85e7fca25fbe341453c37c1072078b6f2
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188270"
---
# <a name="azure-stack-1905-known-issues"></a>Známé problémy s Azure Stack 1905

V tomto článku jsou uvedené známé problémy ve verzi 1905 Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.

## <a name="update-process"></a>Aktualizovat proces

### <a name="host-node-update-prerequisite-failure"></a>Selhání požadovaných součástí aktualizace uzlu hostitele

- To Tento problém se týká aktualizace 1905.
- Příčina: Při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může dojít k selhání stavu aktualizace z důvodu splnění **požadavků na aktualizaci uzlu hostitele**. To je obvykle způsobeno hostitelským uzlem, který má nedostatek volného místa na disku.
- Nápravy Pokud chcete získat pomoc s vymazáváním místa na disku v uzlu hostitele, obraťte se na podporu Azure Stack.
- Výskyt Mimořádné

### <a name="preparation-failed"></a>Příprava se nezdařila

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může stav aktualizace selhat a změnit stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Balíček aktualizace 1905 je větší než předchozí balíčky aktualizací, což může způsobit, že dojde k většímu problému.
- Nápravy Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** (nepokračovat). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
- Výskyt Společné

## <a name="portal"></a>Portál

### <a name="subscription-resources"></a>Prostředky předplatného

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Odstraňuje se předplatné uživatele za následek osamocené prostředky.
- Nápravy Nejprve odstraňte prostředky uživatele nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.
- Výskyt Společné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack.
- Nápravy [K ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt Společné

### <a name="marketplace-management"></a>Správa Marketplace

- To Tento problém se týká 1904 a 1905.
- Příčina: Obrazovka správy Marketplace se při přihlášení k portálu pro správu nezobrazí.
- Nápravy Aktualizujte prohlížeč nebo přejít na **Nastavení** a vyberte možnost **Obnovit výchozí nastavení**.
- Výskyt Občasně

### <a name="docker-extension"></a>Rozšíření Docker

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Pokud vyhledáte Docker, na portále správce iv uživatelském prostředí se položka nesprávně vrátí. Není k dispozici ve službě Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba.
- Nápravy Žádné zmírnění.
- Výskyt Společné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Nápravy Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt Společné

### <a name="template"></a>Šablona

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Uživatelské rozhraní nasazení šablon na portálu User Portal neplní parametry pro názvy šablon začínající znakem "_" (podtržítko).
- Nápravy Odebere znak "_" (znak podtržítka) z názvu šablony.
- Výskyt Společné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-endový fond

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Pokud se v uživatelském portálu pokusíte přidat **back-end fond** do **Load Balancer**, operace se nezdaří s chybovou zprávou **se nepodařilo aktualizovat Load Balancer...** .
- Nápravy Pomocí PowerShellu, rozhraní příkazového řádku nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt Společné

#### <a name="create-inbound-nat"></a>Vytvoření příchozího překladu adres (NAT)

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Pokud se v uživatelském portálu pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří s chybovou zprávou **se nepodařilo aktualizovat Load Balancer...** .
- Nápravy Pomocí PowerShellu, rozhraní příkazového řádku nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt Společné

#### <a name="create-load-balancer"></a>Vytvořit nástroj pro vyrovnávání zatížení

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal se v okně **vytvořit Load Balancer** zobrazí možnost vytvořit **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není v Azure Stack podporována.
- Nápravy Místo toho použijte **základní** možnosti nástroje pro vyrovnávání zatížení.
- Výskyt Společné

### <a name="public-ip-address"></a>Veřejná IP adresa

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: V uživatelském portálu se v okně **vytvořit veřejnou IP adresu** zobrazí možnost vytvořit **standardní** SKU. **Standard** SKU není v Azure Stack podporován.
- Nápravy Použijte **základní** SKU pro veřejnou IP adresu.
- Výskyt Společné

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: Nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s**názvem VM-Name.
K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Nápravy Vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt Společné

### <a name="vm-resize"></a>Změna velikosti virtuálního počítače

- To Tento problém se týká verze 1905.
- Příčina: Nepovedlo se úspěšně změnit velikost virtuálního počítače spravovaného disku. Při pokusu o změnu velikosti virtuálního počítače dojde k chybě s kódem: "InternalOperationError", "zpráva": "Při operaci došlo k vnitřní chybě."
- Nápravy Pracujeme na tom, abychom tento problém napravili v příští verzi. V současné době musíte virtuální počítač vytvořit znovu s novou velikostí virtuálního počítače.
- Výskyt Společné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="centos"></a>CentOS

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Prostředí pro vytvoření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není na Azure Stack Marketplace k dispozici, což způsobí selhání nasazení při volání, že se image nenajde.
- Nápravy Vyberte jiný operační systém pro nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.
- Výskyt Společné

#### <a name="remove-scale-set"></a>Odebrat sadu škálování

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nemůžete odebrat sadu škálování z okna **Virtual Machine Scale Sets** .
- Nápravy Vyberte sadu škálování, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit** v podokně **Přehled** .
- Výskyt Společné

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Nápravy Můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

#### <a name="scale-set-instance-view-blade-doesnt-load"></a>Okno zobrazení instance sady škálování se nenačte.

- To Tento problém se týká verze 1904 a 1905.
- Příčina: Okno zobrazení instance sady škálování virtuálního počítače, které se nachází na Azure Stack Portalu > řídicím panelu > Virtual Machine Scale Sets – > AnyScaleSet-Instances-> AnyScaleSetInstance se nepovede načíst a zobrazí cloudovou image Crying.
- Nápravy V současné době nedochází k nápravě a pracujeme na opravě. Až pak použijte příkaz `az vmss get-instance-view` CLI, abyste získali zobrazení instance pro sadu škálování.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Nápravy Použijte přístup k virtuálnímu počítači pro rozšíření pro Linux k implementaci klíčů SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt Společné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](azure-stack-release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](azure-stack-release-notes-security-updates.md)
