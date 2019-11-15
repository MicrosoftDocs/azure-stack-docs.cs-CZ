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
ms.openlocfilehash: 367a2cb5a07c0db2b272b8ffc951f51e5ed9cc0e
ms.sourcegitcommit: bb2bbfad8061f7677954f6ce5a435b4e6f9299b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74100031"
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

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu instalovat aktualizace](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
- Výskyt: běžné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

### <a name="subscriptions-properties-blade"></a>Okno vlastností předplatných

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu pro správu se okno **vlastnosti** pro odběry nenačte správně.
- Náprava: tyto vlastnosti předplatného můžete zobrazit v podokně **základy** v okně s **přehledem předplatných** .
- Výskyt: běžné

### <a name="subscriptions-lock-blade"></a>Okno zámku předplatných

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu pro správu má okno **zámku** pro odběry uživatele dvě butonsy, které říkají **předplatné**.
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě
- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: explicitní pravidlo **DenyAllOutbound** nejde vytvořit v NSG, protože to zabrání v dokončení veškeré interní komunikace s infrastrukturou, která je potřebná pro nasazení virtuálního počítače.
- Výskyt: běžné

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: primární síťová karta virtuálního počítače se nedá změnit. Odstranění nebo odpojení primární síťové karty bude mít za následek problémy při spuštění virtuálního počítače.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="local-network-gateway-deletion"></a>Odstranění brány místní sítě

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při odstraňování **brány místní sítě** se zobrazí následující chybová zpráva: **bránu místní sítě s aktivním připojením nejde odstranit**, i když neexistuje žádné aktivní připojení.
- Zmírnění: Oprava tohoto problému se uvolní v 1907. Alternativním řešením tohoto problému je vytvořit novou bránu místní sítě se stejnou IP adresou, adresním prostorem a podrobnostmi konfigurace s jiným názvem. Po aktualizaci prostředí na 1907 se dá původní LNG odstranit.
- Výskyt: běžné

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro dokumentaci Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Služby Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové okno pro resetování hesla se zobrazí v uživatelském rozhraní sady škálování, ale Azure Stack nepodporuje resetování hesla v sadě škálování.
- Náprava: žádné.
- Výskyt: běžné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na stránce s přehledem sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Náprava: žádné.
- Výskyt: běžné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- Platí: Tento problém se vztahuje na všechny podporované verze.    
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Náprava: žádné.
- Výskyt: běžné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>proces aktualizace 1907

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace 1907 Azure Stack aktualizace může dojít k selhání stavu aktualizace a změně stavu na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
- Výskyt: běžné

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

### <a name="subscriptions-properties-blade"></a>Okno vlastností předplatných

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu pro správu se okno **vlastnosti** pro odběry nenačte správně.
- Náprava: tyto vlastnosti předplatného můžete zobrazit v podokně **základy** v okně s **přehledem předplatných** .
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

### <a name="network-connection-type"></a>Typ síťového připojení

- Platí: Tento problém se týká jakéhokoli prostředí 1906 nebo 1907. 
- Příčina: na portálu User Portal zobrazuje okno **AddConnection** možnost použít **VNet-to-VNet**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje. 
- Výskyt: běžné 

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro dokumentaci Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Služby Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- Platí: Tento problém se týká verzí 1906 a 1907.
- Příčina: nové okno pro resetování hesla se zobrazí v uživatelském rozhraní sady škálování, ale Azure Stack nepodporuje resetování hesla v sadě škálování.
- Náprava: žádné.
- Výskyt: běžné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- Platí: Tento problém se týká verzí 1906 a 1907.
- Příčina: na stránce s přehledem sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Náprava: žádné.
- Výskyt: běžné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- Platí: Tento problém se týká verzí 1906 a 1907.    
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Náprava: žádné.
- Výskyt: běžné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>proces aktualizace 1906

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace 1906 Azure Stack aktualizace může dojít k selhání stavu aktualizace a změně stavu na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. 
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
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

### <a name="subscriptions-properties-blade"></a>Okno vlastností předplatných
- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu pro správu se okno **vlastnosti** pro odběry nenačte správně.
- Náprava: tyto vlastnosti předplatného můžete zobrazit v podokně základy v okně s přehledem předplatných.
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v Azure Stack podporovaná.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt BLOB

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

### <a name="update"></a>Aktualizovat

- Platí: Tento problém se týká verze 1906.
- Příčina: na portálu operátora stav aktualizace opravy hotfix zobrazuje nesprávný stav aktualizace. Počáteční stav znamená, že se instalace aktualizace nezdařila, i když stále probíhá.
- Náprava: aktualizujte portál a stav se aktualizuje na probíhá.
- Výskyt: občasné

## <a name="networking"></a>Sítě

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro Azure Stack dokumentaci použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-end fond

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte přidat **back-end fond** do **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...** .
- Náprava: pomocí PowerShellu, CLI nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-inbound-nat"></a>Vytvoření příchozího překladu adres (NAT)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...** .
- Náprava: pomocí PowerShellu, CLI nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

## <a name="compute"></a>Služby Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- Platí: Tento problém se týká verze 1906.
- Příčina: nové okno pro resetování hesla se zobrazí v uživatelském rozhraní sady škálování, ale Azure Stack nepodporuje resetování hesla v sadě škálování.
- Náprava: žádné.
- Výskyt: běžné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- Platí: Tento problém se týká verze 1906.
- Příčina: na stránce s přehledem sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním Azure Stack buildu.
- Náprava: žádné.
- Výskyt: běžné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- Platí: Tento problém se týká verze 1906.
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Náprava: žádné.
- Výskyt: běžné

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

- Platí: Tento problém se týká aktualizace 1905.
- Příčina: při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace se může stát, že se stav aktualizace nezdařil z důvodu splnění **požadavků na aktualizaci uzlu hostitele**. To je obvykle způsobeno hostitelským uzlem, který má nedostatek volného místa na disku.
- Náprava: obraťte se na podporu Azure Stack, aby bylo možné získat pomoc při mazání místa na disku v uzlu hostitele.
- Výskyt: Neběžné

### <a name="preparation-failed"></a>Příprava se nezdařila

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace 1905 Azure Stack aktualizace může dojít k selhání stavu aktualizace a změně stavu na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Balíček aktualizace 1905 je větší než předchozí balíčky aktualizací, což může způsobit, že dojde k většímu problému.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu import a instalace aktualizací](azure-stack-apply-updates.md).
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

### <a name="template"></a>Šablona

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal uživatelské rozhraní nasazení šablony neplní parametry pro názvy šablon začínající znakem "_" (znak podtržítka).
- Oprava: z názvu šablony odeberte znak "_" (podtržítko).
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

#### <a name="add-backend-pool"></a>Přidat back-end fond

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte přidat **back-end fond** do **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...** .
- Náprava: pomocí PowerShellu, CLI nebo šablony Správce prostředků přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.
- Výskyt: běžné

#### <a name="create-inbound-nat"></a>Vytvoření příchozího překladu adres (NAT)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se na portálu User Portal pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří a chybová zpráva **se nepovedlo aktualizovat Load Balancer...** .
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

## <a name="compute"></a>Služby Compute

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
- Náprava: momentálně nedochází k nápravě a pracujeme na opravě. Až pak použijte příkaz CLI `az vmss get-instance-view`, abyste získali zobrazení instance pro sadu škálování.

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
