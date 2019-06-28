---
title: Azure Stack. 1906 známé problémy | Dokumentace Microsoftu
description: Přečtěte si o známých problémech v Azure stacku. 1906.
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
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 3927412b89ad9db919018832f1205d0f8eae3c6d
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419821"
---
# <a name="azure-stack-1906-known-issues"></a>Azure Stack. 1906 známé problémy

Tento článek obsahuje seznam známých problémů. 1906 verze služby Azure Stack. V seznamu je aktualizovat, protože nové problémy jsou označeny.

> [!IMPORTANT]  
> V této části najdete před instalací této aktualizace.

## <a name="update-process"></a>Proces aktualizace

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace služby Azure Stack. 1906, stav aktualizace může selhat a změnu stavu na **PreparationFailed**. To je způsobeno aktualizace poskytovateli prostředků (URP) není schopen správně přenosu souborů z kontejneru úložiště do sdílené složky interní infrastruktury pro zpracování. Balíček aktualizace. 1906 je větší než předchozí balíčky aktualizací, které můžou způsobit, že tento problém pravděpodobně probíhat.
- Náprava: Počínaje verzí 1901 (1.1901.0.95), můžete alternativně vyřešit tento problém kliknutím **aktualizovat** znovu (ne **Resume**). URP potom vyčistí soubory z předchozího pokusu o a zahájí stahování. Pokud se problém nevyřeší, doporučujeme, abyste ručně pomocí následujících nahrání balíčku aktualizace [importu a nainstalovat aktualizace části](azure-stack-apply-updates.md#import-and-install-updates).
- Výskyt: Společné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Pro správu předplatných

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Obě předplatná pro správu, které byly představeny s nástrojem verzi 1804 se nesmí používat. Typy předplatného jsou **měření** předplatného, a **spotřeby** předplatného.
- Náprava: Pokud máte prostředky spuštěné na tyto dva odběry služeb, je znovu vytvořte v předplatných uživatele.
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

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, účet úložiště **konfigurace** okno možnost změny **typu zabezpečení přenosu**. Tato funkce není aktuálně podporovaná ve službě Azure Stack.
- Výskyt: Společné

### <a name="upload-blob"></a>Nahrát objekt blob

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, při pokusu nahrát objekt blob pomocí **OAuth(preview)** možnost, úloha se nezdaří s chybovou zprávou.
- Náprava: Nahrajte objekt blob pomocí možnosti SAS.
- Výskyt: Společné

## <a name="networking"></a>Sítě

### <a name="service-endpoints"></a>Koncové body služby

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **virtuální sítě** okno možnost k použití **koncové body služby**. Tato funkce není aktuálně podporován ve službě Azure Stack.
- Výskyt: Společné

### <a name="network-interface"></a>Síťové rozhraní

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nové síťové rozhraní nelze přidat do virtuálního počítače, který je v **systémem** stavu.
- Náprava: Zastavte virtuální počítač před přidáváním a odebíráním síťové rozhraní.
- Výskyt: Společné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **brány virtuální sítě** okno možnost k použití **výstrahy**. Tato funkce není aktuálně podporován ve službě Azure Stack.
- Výskyt: Společné

#### <a name="active-active"></a>Aktivní–aktivní

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, při vytváření a v nabídce prostředků **brány virtuální sítě**, zobrazí se možnost Povolit **aktivní-aktivní** konfigurace. Tato funkce není aktuálně podporován ve službě Azure Stack.
- Výskyt: Společné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **připojení** okno funkci s názvem **Poradce při potížích s VPN**. Tato funkce není aktuálně podporován ve službě Azure Stack.
- Výskyt: Společné

#### <a name="documentation"></a>Dokumentace

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Odkazy na dokumentaci na stránce Přehled brány virtuální sítě se odkaz na dokumentaci týkající se Azure namísto Azure stacku. Dokumentace ke službě Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP ve službě Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadejte vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření nového virtuálního počítače (VM) Windows, může se zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač 'název_virtuálního_počítače'. Chyba: Nepovedlo se aktualizovat nastavení sériového portu výstup pro virtuální počítač 'název_virtuálního_počítače'** . Pokud jste povolení diagnostiky spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě.
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
- Příčina: Nelze odebrat škálovací sady z **škálovací sady virtuálních počítačů** okno.
- Náprava: Vyberte škálovací sady, že chcete odebrat, klikněte **odstranit** tlačítko **přehled** podokně.
- Výskyt: Společné

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytvoření chyby během opravy a aktualizace v prostředích Azure Stack 4 uzly

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvořit škálovací sadu virtuálních počítačů nastavte instance selže a zobrazí se **FabricVmPlacementErrorUnsupportedFaultDomainSize** chyba během procesu aktualizace ve službě Azure Stack 4 uzly prostředí.
- Náprava: Můžete vytvořit jednotlivých virtuálních počítačů ve skupině dostupnosti s 2 domén selhání byl úspěšně nastaven. Vytváření instance škálovací sady je však stále nejsou k dispozici během procesu aktualizace ve službě Azure Stack 4 uzly.

#### <a name="scale-set-instance-view-blade-does-not-load"></a>Škálovací sada instanci zobrazení okna se nenačte

- Použít: Tento problém se vztahuje na 1904 byl 1905 a 1906 verze.
- Příčina: V okně zobrazení instance škálovací sady virtuálního počítače umístěny na portálu Azure Stack v **řídicí panel > škálovací sady virtuálních počítačů > AnyScaleSet > instancí > AnyScaleSetInstance** nepodaří načíst a zobrazí "rainy Obrázek cloudu".
- Náprava: Aktuálně nejsou k dispozici žádná možnost nápravy a pracujeme na opravě. Dokud to neuděláte, použijte příkaz rozhraní příkazového řádku `az vmss get-instance-view` získat zobrazení instance škálovací sady.

### <a name="ubuntu-ssh-access"></a>Přístup SSH se systémem Ubuntu

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení.
- Náprava: Přístup k virtuálním počítačům pro rozšíření Linuxu použít k implementaci klíče SSH po zřízení, nebo použít ověřování pomocí hesla.
- Výskyt: Společné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Virtuální počítač škálovací sady resetování hesla nefunguje

- Použít: Tento problém se týká verze. 1906.
- Příčina: Nové okno resetování hesla se zobrazí ve škálovací sadě uživatelského rozhraní, ale Azure Stack resetuje se heslo ve škálovací sadě ještě nepodporuje.
- Náprava: Žádné
- Výskyt: Společné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Destivo cloudu na škálování nastavení diagnostiky

- Použít: Tento problém se týká verze. 1906.
- Příčina: Prázdný graf se zobrazí stránka s přehledem škálovací sady virtuálních počítačů. Kliknutím na prázdný graf otevřete okno "prší cloudu". Toto je diagram pro škálovací sadu diagnostické informace, jako je například procento využití procesoru a není funkce podporované v aktuálním sestavení služby Azure Stack.
- Náprava: Žádné
- Výskyt: Společné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno diagnostické nastavení virtuálního počítače

- Použít: Tento problém se týká verze. 1906.
- Příčina: Obsahuje nastavení diagnostiky okno virtuálního počítače **jímky** kartu, která požádá o **Application Insight účet**. To je výsledkem nové okno a není zatím podporována ve službě Azure Stack.
- Náprava: Žádné
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
