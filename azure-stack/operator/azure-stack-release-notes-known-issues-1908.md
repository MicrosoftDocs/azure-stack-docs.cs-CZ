---
title: Známé problémy s Azure Stack 1908 | Microsoft Docs
description: Přečtěte si o známých problémech v Azure Stack 1908.
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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: 86d8cea0164a9181f444066181945358122c831b
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862988"
---
# <a name="azure-stack-1908-known-issues"></a>Známé problémy s Azure Stack 1908

V tomto článku jsou uvedené známé problémy ve verzi 1908 Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.

## <a name="update-process"></a>Aktualizovat proces

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Nápravy Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu instalovat aktualizace](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
- Výskyt Společné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Tato dvě předplatná pro správu, která byla představena s verzí 1804, by se neměla používat. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Nápravy Pokud máte prostředky běžící na těchto dvou předplatných, vytvořte je znovu v předplatných uživatele.
- Výskyt Společné

### <a name="subscriptions-properties-blade"></a>Okno vlastností předplatných

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu pro správu se okno **vlastnosti** pro odběry nenačte správně.
- Nápravy Tyto vlastnosti předplatného si můžete zobrazit v podokně **základy** v okně **Přehled předplatných** .
- Výskyt Společné

### <a name="subscriptions-lock-blade"></a>Okno zámku předplatných

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu pro správu má okno **zámku** pro odběry uživatele dvě butonsy, které říkají **předplatné**.
- Výskyt Společné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack.
- Nápravy [K ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt Společné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt Společné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Nápravy Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt Společné

## <a name="networking"></a>Sítě

### <a name="service-endpoints"></a>Koncové body služby

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal se v okně **Virtual Network** zobrazuje možnost použití **koncových bodů služby**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt Společné

### <a name="network-interface"></a>Síťové rozhraní

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Nápravy Před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt Společné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="local-network-gateway-deletion"></a>Odstranění brány místní sítě

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při odstranění **brány místní sítě** na portálu User Portal se zobrazí následující chybová zpráva: **Bránu místní sítě nelze odstranit s aktivním připojením**, i když neexistuje žádné aktivní připojení.
- Zmírnění Oprava tohoto problému se uvolní v 1907. Alternativním řešením tohoto problému je vytvořit novou bránu místní sítě se stejnou IP adresou, adresním prostorem a podrobnostmi konfigurace s jiným názvem. Po aktualizaci prostředí na 1907 se dá původní LNG odstranit.
- Výskyt Společné

#### <a name="alerts"></a>Upozornění

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal zobrazuje okno **Virtual Network brány** možnost používat **výstrahy**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt Společné

#### <a name="active-active"></a>Aktivní–aktivní

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt Společné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt Společné

#### <a name="documentation"></a>Dokumentace

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro dokumentaci Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: Nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s**názvem VM-Name. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Nápravy Vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt Společné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Nápravy Můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Nápravy Použijte přístup k virtuálnímu počítači pro rozšíření pro Linux k implementaci klíčů SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt Společné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: V uživatelském rozhraní sady škálování se zobrazí nové okno pro resetování hesla, ale Azure Stack nepodporuje resetování hesla u sady škálování.
- Nápravy Žádné.
- Výskyt Společné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na stránce Přehled sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Nápravy Žádné.
- Výskyt Společné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- To Tento problém se vztahuje na všechny podporované verze.    
- Příčina: Okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Nápravy Žádné.
- Výskyt Společné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další postup

- [Kontrola kontrolního seznamu aktivit aktualizací](azure-stack-release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](azure-stack-release-notes-security-updates.md)
