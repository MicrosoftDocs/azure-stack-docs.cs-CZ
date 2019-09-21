---
title: Azure Stack známé problémy
description: Přečtěte si o známých problémech v Azure Stack verzích.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 09/13/2019
ms.openlocfilehash: ba5b8358dced378f499c30f9b8d409497319fc86
ms.sourcegitcommit: 2c37ac8e88de19430080128bac1b70e33557d354
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71164236"
---
# <a name="azure-stack-known-issues"></a>Azure Stack známé problémy

V tomto článku jsou uvedené známé problémy ve verzích Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

Chcete-li získat přístup ke známým problémům pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>proces aktualizace 1908

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

### <a name="upload-blob"></a>Nahrát objekt blob

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
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>proces aktualizace 1907

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace 1907 Azure Stack aktualizace může stav aktualizace selhat a změnit stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Nápravy Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
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

### <a name="subscription-permissions"></a>Oprávnění předplatného

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack.
- Nápravy [K ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt Společné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt Společné

### <a name="upload-blob"></a>Nahrát objekt blob

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

### <a name="network-connection-type"></a>Typ síťového připojení

- To Tento problém se týká jakéhokoli prostředí 1906 nebo 1907. 
- Příčina: Na portálu User Portal zobrazuje okno **AddConnection** možnost použít **VNet-to-VNet**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje. 
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

- To Tento problém se týká verzí 1906 a 1907.
- Příčina: V uživatelském rozhraní sady škálování se zobrazí nové okno pro resetování hesla, ale Azure Stack nepodporuje resetování hesla u sady škálování.
- Nápravy Žádné.
- Výskyt Společné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- To Tento problém se týká verzí 1906 a 1907.
- Příčina: Na stránce Přehled sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Nápravy Žádné.
- Výskyt Společné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- To Tento problém se týká verzí 1906 a 1907.    
- Příčina: Okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Nápravy Žádné.
- Výskyt Společné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>proces aktualizace 1906

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace 1906 Azure Stack aktualizace může stav aktualizace selhat a změnit stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. 
- Nápravy Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
- Výskyt Společné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Tato dvě předplatná pro správu, která byla představena s verzí 1804, by se neměla používat. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Nápravy Pokud máte prostředky běžící na těchto dvou předplatných, vytvořte je znovu v předplatných uživatele.
- Výskyt Společné

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

### <a name="subscriptions-properties-blade"></a>Okno vlastností předplatných
- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu pro správu se okno **vlastnosti** pro odběry nenačte správně.
- Nápravy Tyto vlastnosti předplatných můžete zobrazit v podokně základy v okně s přehledem předplatných.
- Výskyt Společné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt Společné

### <a name="upload-blob"></a>Nahrát objekt blob

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Nápravy Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt Společné

### <a name="update"></a>Aktualizace

- To Tento problém se týká verze 1906.
- Příčina: V portálu operátora aktualizace stav opravy hotfix zobrazuje nesprávný stav aktualizace. Počáteční stav znamená, že se instalace aktualizace nezdařila, i když stále probíhá.
- Nápravy Aktualizujte portál a stav se aktualizuje na probíhá.
- Výskyt Občasně

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
- Příčina: Dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro Azure Stack dokumentaci použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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

- To Tento problém se týká verze 1906.
- Příčina: V uživatelském rozhraní sady škálování se zobrazí nové okno pro resetování hesla, ale Azure Stack nepodporuje resetování hesla u sady škálování.
- Nápravy Žádné.
- Výskyt Společné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- To Tento problém se týká verze 1906.
- Příčina: Na stránce Přehled sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Nápravy Žádné.
- Výskyt Společné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- To Tento problém se týká verze 1906.
- Příčina: Okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Nápravy Žádné.
- Výskyt Společné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-update-process"></a>proces aktualizace 1905

### <a name="host-node-update-prerequisite-failure"></a>Selhání požadovaných součástí aktualizace uzlu hostitele

- To Tento problém se týká aktualizace 1905.
- Příčina: Při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může dojít k selhání stavu aktualizace z důvodu splnění **požadavků na aktualizaci uzlu hostitele**. To je obvykle způsobeno hostitelským uzlem, který má nedostatek volného místa na disku.
- Nápravy Pokud chcete získat pomoc s vymazáváním místa na disku v uzlu hostitele, obraťte se na podporu Azure Stack.
- Výskyt Mimořádné

### <a name="preparation-failed"></a>Příprava se nezdařila

- To Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může stav aktualizace selhat a změnit stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Balíček aktualizace 1905 je větší než předchozí balíčky aktualizací, což může způsobit, že dojde k většímu problému.
- Nápravy Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
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
- Příčina: Pokud vyhledáte **Docker**, na portále správce i v uživatelském prostředí se položka nesprávně vrátí. Není k dispozici ve službě Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba.
- Nápravy Žádné zmírnění.
- Výskyt Společné

### <a name="upload-blob"></a>Nahrát objekt blob

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
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="archive"></a>Archiv

Chcete-li získat přístup k archivovaným známým problémům pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>1904 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>1903 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>1902 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>1901 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>1811 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>1809 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>1808 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>1807 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>1805 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>1804 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>1803 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>1802 archivovaných známých problémů
::: moniker-end

::: moniker range="<azs-1905"
Ke [starším verzím Azure Stack známým problémům můžete přistupovat v Galerii TechNet](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře Azure Stack najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end