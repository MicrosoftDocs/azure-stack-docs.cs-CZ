---
title: Známé problémy centra Azure Stack
description: Přečtěte si o známých problémech v Azure Stackch vydáních centra.
author: sethmanheim
ms.topic: article
ms.date: 03/18/2020
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: a2f86835a9b1008417e427bc62229e94d6b00595
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512231"
---
# <a name="azure-stack-hub-known-issues"></a>Známé problémy centra Azure Stack

V tomto článku jsou uvedené známé problémy v části vydání centra Azure Stack. Seznam se aktualizuje, protože se identifikují nové problémy.

Chcete-li získat přístup ke známým problémům pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1907"
> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.
::: moniker-end
::: moniker range="<azs-1907"
> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2002"
## <a name="update"></a>Aktualizace

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](azure-stack-updates-troubleshoot.md).

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack hub zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-options"></a>Možnosti účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se názvy účtů úložiště zobrazují jako **účet úložiště – objekt blob, soubor, tabulka, fronta**; v Azure Stackovém centru se ale nepodporuje **soubor** .
- Výskyt: běžné

### <a name="create-managed-disk-snapshot"></a>Vytvořit snímek spravovaného disku

- Platí: Tento problém se týká verze 2002.
- Příčina: při vytváření snímku spravovaného disku na portálu User Portal je pole **typ účtu** prázdné. Když vyberete tlačítko **vytvořit** s prázdným typem účtu, vytvoření snímku se nezdařilo.
- Náprava: Vyberte typ účtu v rozevíracím seznamu **typ účtu** a pak vytvořte snímek.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: explicitní pravidlo **DenyAllOutbound** nejde vytvořit v NSG, protože to zabrání v dokončení veškeré interní komunikace s infrastrukturou, která je potřebná pro nasazení virtuálního počítače.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

#### <a name="addingremoving-network-interface"></a>Přidávání/odebírání síťového rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

#### <a name="primary-network-interface"></a>Primární síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: primární síťová karta virtuálního počítače se nedá změnit. Při odstraňování nebo odpojování primární síťové karty dojde k problémům při spouštění virtuálního počítače.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="consumed-compute-quota"></a>Spotřebovaná kvóta COMPUTE

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při vytváření nového virtuálního počítače se může zobrazit chyba, například **Toto předplatné má kapacitu pro celkovou oblast vCPU v tomto umístění. V tomto předplatném je k dispozici všechny dostupné vCPU celkové oblasti 50.** To znamená, že byla dosažena kvóta pro celkový počet jader, která jsou k dispozici.
- Náprava: Zeptejte se operátora na plán doplňku s dodatečnou kvótou. Úpravy kvóty aktuálního plánu nebudou fungovat ani by odrážely vyšší kvótu.
- Výskyt: vzácná

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí centra Azure Stack se 4 uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace ve 4 Azure Stackovém nasazení centra stále k dispozici.

## <a name="resource-providers"></a>Poskytovatelé prostředků

### <a name="sqlmysql"></a>SQL/MySQL

- Platí: Tento problém se týká verze 2002. 
- Příčina: Pokud razítko obsahuje poskytovatele prostředků SQL (RP) verze 1.1.33.0 nebo starší, po aktualizaci razítka se nenačte i okna pro SQL/MySQL.
- Náprava: aktualizujte RP na verzi 1.1.47.0

### <a name="app-service"></a>App Service

- Platí: Tento problém se týká verze 2002.
- Příčina: Pokud razítko obsahuje App Service Resource Provider (RP) verze 1,7 a starší, po aktualizaci razítka se okna pro App Service nenačte.
- Náprava: aktualizujte RP na verzi 1,8.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1910"
## <a name="update"></a>Aktualizace

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](azure-stack-updates-troubleshoot.md).

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

### <a name="subscriptions-lock-blade"></a>Okno zámku předplatných

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu pro správu má okno **zámku** pro předplatné uživatele dvě tlačítka, která říká **předplatné**.
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack hub zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v centru Azure Stack podporována.
- Výskyt: běžné

### <a name="upload-blob-with-oauth-error"></a>Nahrát objekt BLOB s chybou OAuth

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

### <a name="upload-blob-option-unsupported"></a>Možnost nahrát objekt BLOB není podporována

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se při pokusu o nahrání objektu BLOB v okně nahrávání dá vybrat možnost ověřování **AAD** nebo **klíč**, ale v centru Azure Stack se nepodporuje **AAD** .
- Výskyt: běžné


### <a name="alert-for-network-interface-disconnected"></a>Výstraha pro síťové rozhraní odpojena

- Platí: Tento problém se týká 1908 a novějších.
- Příčina: když je kabel odpojený od síťového adaptéru, výstraha se na portálu pro správu nezobrazí. Příčinou této chyby je, že tato chyba je ve výchozím nastavení ve Windows serveru 2019 zakázaná.
- Výskyt: běžné

### <a name="incorrect-tooltip-when-creating-vm"></a>Nesprávný popis při vytváření virtuálního počítače

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když v uživatelském portálu vyberete spravovaný disk s typem disku SSD úrovně Premium, zobrazí se v rozevíracím seznamu **disk s operačním systémem**. Popis vedle této možnosti říká, že **některé velikosti disků s operačním systémem můžou být dostupné zdarma s bezplatným účtem Azure**; to však není platné pro Azure Stack hub. Seznam navíc zahrnuje **bezplatný nárok na účet** , který není platný i pro centrum Azure Stack.
- Výskyt: běžné

### <a name="vpn-troubleshoot-and-metrics"></a>Řešení potíží a metriky sítě VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se zobrazí funkce **řešení potíží s VPN** a **metriky** v prostředku brány sítě VPN, ale toto nastavení není v centru Azure Stack podporováno.
- Výskyt: běžné


### <a name="delete-a-storage-container"></a>Odstranění kontejneru úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když se uživatel pokusí odstranit kontejner úložiště, v uživatelském portálu se operace nepovede, když uživatel nepřepne **Nastavení Role Azure Policy a RBAC**.
- Náprava: Ujistěte se, že je zaškrtnuté políčko **přepsat Azure Policy a nastavení role RBAC**.
- Výskyt: běžné

### <a name="refresh-button-on-virtual-machines-fails"></a>Tlačítko Aktualizovat na virtuálních počítačích se nezdařila

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když na portálu User Portal přejdete na **Virtual Machines** a pokusíte se aktualizovat pomocí tlačítka v horní části, stavy se nemůžou aktualizovat přesně.
- Náprava: stav se automaticky aktualizuje každých 5 minut bez ohledu na to, zda bylo tlačítko Aktualizovat stisknuto nebo ne. Počkejte 5 minut a ověřte stav.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: když v uživatelském portálu vytvoříte směrovací tabulku, **Virtual Network brána** se zobrazí jako jedna z možností dalšího typu segmentu směrování. To se ale v centru Azure Stack nepodporuje.
- Výskyt: běžné

### <a name="storage-account-options"></a>Možnosti účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se názvy účtů úložiště zobrazují jako **účet úložiště – objekt blob, soubor, tabulka, fronta**; v Azure Stackovém centru se ale nepodporuje **soubor** .
- Výskyt: běžné

### <a name="storage-account-configuration"></a>Konfigurace účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření účtu úložiště a zobrazení jeho **Konfigurace**nemůžete uložit změny konfigurace, protože výsledkem je chyba AJAX.
- Výskyt: běžné

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>Sledování kapacity v poskytovateli prostředků SQL udržuje načítání

- Platí: Tento problém se týká centra Azure Stack aktualizace 1910 nebo novější s nainstalovanou verzí poskytovatele prostředků SQL verze 1.1.33.0 nebo starší.
- Příčina: aktuální verze poskytovatele prostředků SQL není kompatibilní s některými nejnovějšími změnami portálu v aktualizaci 1910.
- Náprava: pomocí procesu aktualizace poskytovatele prostředků použijte opravu hotfix poskytovatele prostředků SQL 1.1.47.0 po upgradu centra Azure Stack na aktualizaci 1910 ([SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)). U poskytovatele prostředků MySQL doporučujeme, abyste po upgradu centra Azure Stack na verzi 1910 Update ([MySQL RP verze 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)) použili opravu hotfix poskytovatele prostředků MySQL 1.1.47.0.
- Výskyt: běžné

### <a name="access-control-iam"></a>Řízení přístupu (IAM)

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: rozšíření IAM není aktuální. Portál Ibiza, který se dodává s centrem Azure Stack, zavádí nové chování, které způsobí selhání rozšíření RBAC, pokud uživatel otevírá okno **Access Control (IAM)** pro předplatné, které není vybrané v nástroji pro výběr globálního předplatného (**adresář + předplatné** na portálu User Portal). V okně se zobrazí **načítání** ve smyčce a uživatel nemůže do předplatného přidat nové role. Okno **Přidat** také zobrazuje **načítání** ve smyčce.
- Náprava: Ujistěte se, že je předplatné zaškrtnuté v nabídce **adresář a předplatné** . K nabídce se dá dostat z horní části portálu, poblíž tlačítka **oznámení** nebo prostřednictvím zástupce v okně **všechny prostředky** , ve kterém se zobrazuje **předplatné nezobrazené? Otevřete nastavení adresář a předplatné**. V této nabídce je nutné vybrat odběr.

### <a name="sql-resource-provider"></a>Poskytovatel prostředků SQL

- Platí: Tento problém se týká razítek se systémem 1908 nebo starším.
- Příčina: při nasazení poskytovatele prostředků SQL (RP) verze 1.1.47.0 na portálu se nezobrazuje žádné prostředky, které jsou přidružené k SQL RP.
- Náprava: Odstraňte RP, upgradujte razítko a znovu nasaďte SQL RP.

### <a name="activity-log-blade"></a>Okno Protokol aktivit

- Platí: Tento problém se týká razítek se systémem 1907 nebo novějším. <!-- Note: Applies to 2002 as well -->
- Příčina: při přístupu k protokolu aktivit portál zobrazuje jenom první stránku položek. Při **načtení dalších výsledků** nebudou načteny položky sčítání.
- Náprava: Upravte časový rozsah ve filtru ke kontrole položek, které spadají za první stránku.

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když přidáváte virtuální počítače skupiny dostupnosti do back-endu fondu nástroje pro vyrovnávání zatížení, na portálu se zobrazí chybová zpráva s oznámením, že **se nepodařilo uložit fond back-endu nástroje pro**vyrovnávání zatížení. Jedná se o problém s kosmetickým rozhraním na portálu. funkce jsou pořád na místě a virtuální počítače se úspěšně přidávají do back-endu fondu.
- Výskyt: běžné

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: explicitní pravidlo **DenyAllOutbound** nejde vytvořit v NSG, protože to zabrání v dokončení veškeré interní komunikace s infrastrukturou, která je potřebná pro nasazení virtuálního počítače.
- Výskyt: běžné

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

#### <a name="addingremoving-network-interface"></a>Přidávání/odebírání síťového rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

#### <a name="primary-network-interface"></a>Primární síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: primární síťová karta virtuálního počítače se nedá změnit. Při odstraňování nebo odpojování primární síťové karty dojde k problémům při spouštění virtuálního počítače.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazí okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="consumed-compute-quota"></a>Spotřebovaná kvóta COMPUTE

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při vytváření nového virtuálního počítače se může zobrazit chyba, například **Toto předplatné má kapacitu pro celkovou oblast vCPU v tomto umístění. V tomto předplatném je k dispozici všechny dostupné vCPU celkové oblasti 50.** To znamená, že byla dosažena kvóta pro celkový počet jader, která jsou k dispozici.
- Náprava: Zeptejte se operátora na plán doplňku s dodatečnou kvótou. Úpravy kvóty aktuálního plánu nebudou fungovat ani by odrážely vyšší kvótu.
- Výskyt: vzácná

### <a name="privileged-endpoint"></a>Privilegovaný koncový bod

- Platí: Tento problém se týká 1910 a starších verzí.
- Příčina: k privilegovanému koncovému bodu (virtuálním počítačům s ERC) se nejde připojit z počítače, na kterém běží jiná než anglická verze Windows.
- Náprava: Jedná se o známý problém, který byl vyřešen v vydáních později než 1910. Jako alternativní řešení můžete spustit rutiny prostředí PowerShell **New-PSSession** a **Enter-PSSession** pomocí jazykové verze **en-US** . v příkladech nastavte jazykovou verzi pomocí tohoto skriptu: https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1.
- Výskyt: vzácná

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí centra Azure Stack se 4 uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace ve 4 Azure Stackovém nasazení centra stále k dispozici.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>proces aktualizace 1908

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o instalaci aktualizace centra Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
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
- Příčina: na portálu pro správu má okno **zámku** pro předplatné uživatele dvě tlačítka označená **předplatným**.
- Výskyt: běžné

### <a name="subscription-permissions"></a>Oprávnění předplatného

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack hub zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v centru Azure Stack podporována.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt blob

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

### <a name="alert-for-network-interface-disconnected"></a>Výstraha pro síťové rozhraní odpojena

- Platí: Tento problém se týká verze 1908.
- Příčina: když je kabel odpojený od síťového adaptéru, výstraha se na portálu pro správu nezobrazí. Příčinou této chyby je, že tato chyba je ve výchozím nastavení ve Windows serveru 2019 zakázaná.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Load Balancer

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: Když přidáváte virtuální počítače dostupnost sady do back-endu Load Balancer, na portálu se zobrazí chybová zpráva s oznámením, že **se nepovedlo Uložit fond back-endu nástroje pro vyrovnávání zatížení**. Jedná se o problém na portálu, ale funkce jsou pořád na místě a virtuální počítače se úspěšně přidají do fondu back-endu. 
- Výskyt: běžné

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: explicitní pravidlo **DenyAllOutbound** nejde vytvořit v NSG, protože to zabrání v dokončení veškeré interní komunikace s infrastrukturou, která je potřebná pro nasazení virtuálního počítače.
- Výskyt: běžné

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

#### <a name="addingremoving-network-interface"></a>Přidávání/odebírání síťového rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

#### <a name="primary-network-interface"></a>Primární síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí centra Azure Stack se 4 uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stackch prostředcích stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: v uživatelském rozhraní sady škálování se zobrazí nové okno pro resetování hesla, ale centrum Azure Stack v sadě škálování ještě nepodporuje heslo pro obnovení.
- Náprava: žádné.
- Výskyt: běžné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na stránce s přehledem sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním sestavení centra Azure Stack.
- Náprava: žádné.
- Výskyt: běžné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- Platí: Tento problém se vztahuje na všechny podporované verze.    
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a zatím není v Azure Stackovém centru podporován.
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
- Příčina: při pokusu o instalaci aktualizace 1907 Azure Stack centra se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.
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
- Příčina: k předplatnému nemůžete pomocí portálů Azure Stack hub zobrazit oprávnění.
- Náprava: [k ověření oprávnění použijte PowerShell](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: běžné

### <a name="storage-account-settings"></a>Nastavení účtu úložiště

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Konfigurace** účtu úložiště možnost změny **typu přenosu zabezpečení**. Tato funkce v současnosti není v centru Azure Stack podporována.
- Výskyt: běžné

### <a name="upload-blob"></a>Nahrát objekt blob

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou.
- Náprava: Nahrajte objekt BLOB pomocí možnosti SAS.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Load Balancer

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: Když přidáváte virtuální počítače dostupnost sady do back-endu Load Balancer, na portálu se zobrazí chybová zpráva s oznámením, že **se nepovedlo Uložit fond back-endu nástroje pro vyrovnávání zatížení**. Jedná se o problém na portálu, ale funkce jsou pořád na místě a virtuální počítače se úspěšně přidají do fondu back-endu. 
- Výskyt: běžné

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: explicitní pravidlo **DenyAllOutbound** nejde vytvořit v NSG, protože to zabrání v dokončení veškeré interní komunikace s infrastrukturou, která je potřebná pro nasazení virtuálního počítače.
- Výskyt: běžné

### <a name="service-endpoints"></a>Koncové body služby

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **Virtual Network** možnost používat **koncové body služby**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

### <a name="network-interface"></a>Síťové rozhraní

#### <a name="addingremoving-network-interface"></a>Přidávání/odebírání síťového rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

#### <a name="primary-network-interface"></a>Primární síťové rozhraní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nové síťové rozhraní se nedá přidat do virtuálního počítače, který je ve **spuštěném** stavu.
- Náprava: před přidáním nebo odebráním síťového rozhraní zastavte virtuální počítač.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="alerts"></a>Výstrahy

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal se v okně **Virtual Network brány** zobrazí možnost použít **výstrahy**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="active-active"></a>Aktivní–aktivní

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal při vytváření a v nabídce prostředků **služby Virtual Network Gateway**se zobrazí možnost povolit konfiguraci **typu aktivní-aktivní** . Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

#### <a name="vpn-troubleshooter"></a>Poradce při potížích s VPN

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: na portálu User Portal zobrazuje okno **připojení** funkci **s názvem Poradce při potížích s VPN**. Tato funkce se v současnosti v centru Azure Stack nepodporuje.
- Výskyt: běžné

### <a name="network-connection-type"></a>Typ připojení

- Platí: Tento problém se týká jakéhokoli prostředí 1906 nebo 1907. 
- Příčina: na portálu User Portal zobrazuje okno **AddConnection** možnost použít **VNet-to-VNet**. Tato funkce se v současnosti v centru Azure Stack nepodporuje. 
- Výskyt: běžné 

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [SKU brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Když vytváříte nový virtuální počítač s Windows, může se zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí centra Azure Stack se 4 uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stackch prostředcích stále k dispozici.

### <a name="ubuntu-ssh-access"></a>Přístup SSH Ubuntu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: virtuální počítač s Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožňuje používat klíče SSH k přihlášení.
- Náprava: pomocí přístupu k virtuálnímu počítači pro rozšíření pro Linux implementujte klíče SSH po zřízení nebo použijte ověřování pomocí hesla.
- Výskyt: běžné

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>Resetování hesla sady škálování virtuálního počítače nefunguje

- Platí: Tento problém se týká verzí 1906 a 1907.
- Příčina: v uživatelském rozhraní sady škálování se zobrazí nové okno pro resetování hesla, ale centrum Azure Stack v sadě škálování ještě nepodporuje heslo pro obnovení.
- Náprava: žádné.
- Výskyt: běžné

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Diagnostika deště v diagnostice sady škálování

- Platí: Tento problém se týká verzí 1906 a 1907.
- Příčina: na stránce s přehledem sady škálování virtuálního počítače se zobrazuje prázdný graf. Když kliknete na prázdný graf, otevře se okno "deště Cloud". Toto je graf pro diagnostické informace sady škálování, jako je procento využití procesoru, a není funkce podporovaná v aktuálním sestavení centra Azure Stack.
- Náprava: žádné.
- Výskyt: běžné

### <a name="issues-creating-resources"></a>Problémy při vytváření prostředků

- Platí: Tento problém se týká verze 1906.
- Příčina: došlo k známému problému v 1906 s vlastními rolemi a přidělením oprávnění pro vytvoření prostředku. Můžete se setkat s problémy s vytvářením prostředků i v případě, že máte správná oprávnění.
- Náprava: Pokud chcete tento problém zmírnit, aktualizujte prosím na sestavení 1907.
- Výskyt: běžné

### <a name="virtual-machine-diagnostic-settings-blade"></a>Okno nastavení diagnostiky virtuálního počítače

- Platí: Tento problém se týká verzí 1906 a 1907.    
- Příčina: okno nastavení diagnostiky virtuálního počítače má kartu **jímka** , která žádá o **účet Application**Insights. Toto je výsledek nového okna a zatím není v Azure Stackovém centru podporován.
- Náprava: žádné.
- Výskyt: běžné

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1907"
## <a name="archive"></a>Archiv

Chcete-li získat přístup k archivovaným známým problémům pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>1906 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>1905 archivovaných známých problémů
::: moniker-end
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

::: moniker range="<azs-1907"
Ke [známým problémům centra Azure Stack můžete přistupovat v Galerii TechNet](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře centra Azure Stack najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end
