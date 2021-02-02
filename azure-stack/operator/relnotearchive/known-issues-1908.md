---
title: Známé problémy s Azure Stack 1908
description: Přečtěte si o známých problémech ve verzích Azure Stack 1908.
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5cf4a0bcbef1a5f1d34a979f26c35a10efadc8f8
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248120"
---
# <a name="azure-stack-1908-known-issues"></a>Známé problémy s Azure Stack 1908

V tomto článku jsou uvedené známé problémy ve verzích Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

Chcete-li získat přístup ke známým problémům pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.

> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="1908-update-process"></a>proces aktualizace 1908

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
- Náprava: od verze 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí [oddílu instalovat aktualizace](../azure-stack-apply-updates.md#install-updates-and-monitor-progress).
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
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway** se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v Azure Stack v tuto chvíli nepodporuje.
- Výskyt: běžné

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo Azure Stack. Pro dokumentaci Azure Stack použijte následující odkazy:

  - [Skladové položky brány](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v Azure Stack](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](../azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Výpočetní prostředky

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
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application** Insights. Toto je výsledek nového okna a v Azure Stack ještě není podporovaný.
- Náprava: žádné.
- Výskyt: běžné
