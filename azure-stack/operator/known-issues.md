---
title: Známé problémy centra Azure Stack
description: Přečtěte si o známých problémech v Azure Stackch vydáních centra.
author: sethmanheim
ms.topic: article
ms.date: 11/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 8fd2bf37b959670923586234f361d7370ebe2508
ms.sourcegitcommit: 990e9cbfc3ce2edd2bd3dccc10db465bf8ac518f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94567220"
---
# <a name="azure-stack-hub-known-issues"></a>Známé problémy centra Azure Stack

V tomto článku jsou uvedené známé problémy v Azure Stack vydáních centra. Seznam se aktualizuje, protože se identifikují nové problémy.

Chcete-li získat přístup ke známým problémům pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1910"
> [!IMPORTANT]  
> Před použitím této aktualizace si přečtěte tento oddíl.
::: moniker-end
::: moniker range="<azs-1910"
> [!IMPORTANT]  
> Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2008"
## <a name="update"></a>Aktualizace

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Při aktualizaci se nepovedlo nainstalovat balíček Microsoft. AzureStack. Compute. Installer do virtuálního počítače certifikační autority.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: během aktualizace proces převezme zámek na novém obsahu, který se musí zkopírovat do virtuálního počítače certifikační autority. V případě, že se aktualizace nezdařila, je zámek uvolněn.
- Náprava: pokračovat v aktualizaci.
- Výskyt: vzácná

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Pravidlo DenyAllOutbound nejde vytvořit.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: explicitní pravidlo **DenyAllOutbound** na Internet nejde během vytváření virtuálního počítače vytvořit v NSG, protože to zabrání dokončení komunikace vyžadované pro nasazení virtuálního počítače.
- Náprava: během vytváření virtuálního počítače povolte odchozí přenosy na Internet a po dokončení vytváření virtuálního počítače změňte NSG tak, aby zablokovala požadovaný provoz.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [Skladové položky brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer směrování provozu na jeden back-end virtuální počítač v určitých scénářích

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: při povolování **spřažení relace** na nástroji pro vyrovnávání zatížení se 2 řazená kolekce členů použije místo privátních IP adres přiřazených k virtuálním počítačům IP adresa PA (IP adresa fyzické adresy). Ve scénářích, kdy se provoz směrovaného na nástroj pro vyrovnávání zatížení dorazí přes síť VPN, nebo pokud je povolené, aby se všechny virtuální počítače klienta (zdrojové IP adresy) nacházely na stejném uzlu a spřažení relace, veškerý provoz se směruje na jeden back-end virtuální počítač.
- Výskyt: běžné

## <a name="compute"></a>Compute

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>Aktualizace

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Při aktualizaci se nepovedlo nainstalovat balíček Microsoft. AzureStack. Compute. Installer do virtuálního počítače certifikační autority.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: během aktualizace proces převezme zámek na novém obsahu, který se musí zkopírovat do virtuálního počítače certifikační autority. V případě, že se aktualizace nezdařila, je zámek uvolněn.
- Náprava: pokračovat v aktualizaci.
- Výskyt: vzácná

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Nejde odstranit NSG, pokud nejsou připojené síťové karty ke spuštěnému virtuálnímu počítači.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při zrušení přidružení NSG a síťové karty, která není připojená ke spuštěnému virtuálnímu počítači, se operace aktualizace (PUT) pro tento objekt na vrstvě síťového adaptéru nezdařila. NSG se aktualizuje na vrstvu poskytovatele síťových prostředků, ale ne na síťovém adaptéru, takže NSG se přesune do stavu selhání.
- Náprava: Připojte síťové karty přidružené k NSG, které je třeba odebrat se spuštěnými virtuálními počítači, a zrušte přidružení NSG nebo odeberte všechny síťové karty, které byly přidruženy k NSG.
- Výskyt: běžné

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Pravidlo DenyAllOutbound nejde vytvořit.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: explicitní pravidlo **DenyAllOutbound** na Internet nejde během vytváření virtuálního počítače vytvořit v NSG, protože to zabrání dokončení komunikace vyžadované pro nasazení virtuálního počítače.
- Náprava: během vytváření virtuálního počítače povolte odchozí přenosy na Internet a po dokončení vytváření virtuálního počítače změňte NSG tak, aby zablokovala požadovaný provoz.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [Skladové položky brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer směrování provozu na jeden back-end virtuální počítač v určitých scénářích

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: při povolování **spřažení relace** na nástroji pro vyrovnávání zatížení se 2 řazená kolekce členů použije místo privátních IP adres přiřazených k virtuálním počítačům IP adresa PA (IP adresa fyzické adresy). Ve scénářích, kdy se provoz směrovaného na nástroj pro vyrovnávání zatížení dorazí přes síť VPN, nebo pokud je povolené, aby se všechny virtuální počítače klienta (zdrojové IP adresy) nacházely na stejném uzlu a spřažení relace, veškerý provoz se směruje na jeden back-end virtuální počítač.
- Výskyt: běžné

#### <a name="public-ip"></a>Veřejná IP adresa

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: hodnota **IdleTimeoutInMinutes** pro veřejnou IP adresu, která je přidružená k nástroji pro vyrovnávání zatížení, se nedá změnit. Tato operace umístí veřejnou IP adresu do stavu selhání.
- Náprava: Pokud chcete veřejnou IP adresu vrátit do úspěšného stavu, změňte hodnotu **IdleTimeoutInMinutes** u pravidla vyrovnávání zatížení, které odkazuje na veřejnou IP adresu zpátky na původní hodnotu (výchozí hodnota je 4 minuty).
- Výskyt: běžné

## <a name="compute"></a>Compute

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>Problémy s nasazením sady škálování virtuálních počítačů s Standard_DS2_v2 velikostí pomocí portálu

- Platí: Tento problém se týká verze 2005.
- Příčina: Chyba portálu způsobí, že vytvoření sady škálování se Standard_DS2_v2ou velikostí selže.
- Náprava: k nasazení této velikosti virtuálního počítače v sadě škálování virtuálního počítače použijte PowerShell nebo CLI.

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>Problémy s rozšířeními virtuálních počítačů na serveru Ubuntu 20,04

- Platí: Tento problém se týká **Ubuntu serveru 20,04 LTS**.
- Příčina: některé distribuce systému Linux přešly do Pythonu 3,8 a úplně odebraly starší verze `/usr/bin/python` EntryPoint pro Python. Uživatelé distribuce systému Linux, kteří přešli na Python 3. x, musí `/usr/bin/python` před pokusem o nasazení těchto rozšíření na své virtuální počítače zajistit, aby existoval starý vstupní bod. V opačném případě může nasazení rozšíření selhat.
- Náprava: postupujte podle kroků řešení v části [problémy s používáním rozšíření virtuálních počítačů v Pythonu 3. v systémech Linux Azure Virtual Machines systémy](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3) , ale přeskočte krok 2, protože centrum Azure Stack nemá funkci **příkazu Spustit** .

### <a name="nvv4-vm-size-on-portal"></a>Velikost virtuálního počítače NVv4 na portálu

- Platí: Tento problém se týká 2002 a novějších verzí.
- Příčina: při přechodu přes prostředí pro vytváření virtuálních počítačů se zobrazí velikost virtuálního počítače: NV4as_v4. Zákazníci, kteří mají k dispozici hardware potřebný pro procesory GPU Azure Stack v systému AMD ve verzi Preview, mohou mít úspěšné nasazení virtuálního počítače. U všech ostatních zákazníků dojde k neúspěšnému nasazení virtuálního počítače pomocí této velikosti virtuálního počítače.
- Náprava: návrh při přípravě na rozhraní Azure Stack hub GPU ve verzi Preview.

### <a name="consumed-compute-quota"></a>Spotřebovaná kvóta COMPUTE

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při vytváření nového virtuálního počítače se může zobrazit chyba, například **Toto předplatné má kapacitu pro celkovou oblast vCPU v tomto umístění. V tomto předplatném je k dispozici všechny dostupné vCPU celkové oblasti 50.** To znamená, že byla dosažena kvóta pro celkový počet jader, která jsou k dispozici.
- Náprava: Zeptejte se operátora na plán doplňku s dodatečnou kvótou. Úpravy kvóty aktuálního plánu nebudou fungovat ani by odrážely vyšší kvótu.
- Výskyt: vzácná

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Okno s přehledem virtuálních počítačů nezobrazuje správný název počítače

- Platí: Tento problém se vztahuje na všechny verze.
- Příčina: při zobrazení podrobností o VIRTUÁLNÍm počítači v okně Přehled se zobrazí název počítače **(není k dispozici)**. Jedná se o návrh pro virtuální počítače vytvořené z specializovaných disků nebo snímků disku a zobrazují se také pro image na webu Marketplace.
- Náprava: v části **Nastavení** zobrazte okno **vlastnosti** .

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Vytváření selhání během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí centra Azure Stack se 4 uzly.
- Náprava: můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace ve 4 Azure Stackovém nasazení centra stále k dispozici.

## <a name="storage"></a>Úložiště

### <a name="retention-period-reverts-to-0"></a>Doba uchování se vrátí na 0.

- Platí: Tento problém se týká verzí 2002 a 2005.
- Příčina: Pokud v nastavení doba uchovávání určíte jiné časové období než 0, při aktualizaci 2002 nebo 2005 se vrátí na hodnotu 0 (výchozí hodnota tohoto nastavení). Nastavení 0 dnů se projeví ihned po dokončení aktualizace, což způsobí, že všechny stávající účty úložiště a všechny nadcházející nově odstraněné účty úložiště budou okamžitě neuchovávány a jsou označeny pro periodické uvolňování paměti (které běží každou hodinu).
- Náprava: ručně zadejte dobu uchování do správného období. Jakýkoli účet úložiště, který se už před zadáním nového období uchování vytvořil, se nedá obnovit.  

## <a name="resource-providers"></a>Poskytovatelé prostředků

### <a name="sqlmysql"></a>SQL/MySQL

- Platí: Tento problém se týká verze 2002.
- Příčina: Pokud razítko obsahuje poskytovatele prostředků SQL (RP) verze 1.1.33.0 nebo starší, po aktualizaci razítka se nenačte i okna pro SQL/MySQL.
- Náprava: aktualizujte RP na verzi 1.1.47.0.

### <a name="app-service"></a>App Service

- Platí: Tento problém se týká verze 2002.
- Příčina: Pokud razítko obsahuje App Service Resource Provider (RP) verze 1,7 a starší, po aktualizaci razítka se okna pro App Service nenačte.
- Náprava: aktualizujte RP na verzi 2002 F2.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>Aktualizace

Po instalaci aktualizace 2002 se může na portálu pro správu nesprávně zobrazit upozornění na neplatný zdroj času. Toto falešně pozitivní upozornění můžete ignorovat a v nadcházející verzi bude opravené. 

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Při aktualizaci se nepovedlo nainstalovat balíček Microsoft. AzureStack. Compute. Installer do virtuálního počítače certifikační autority.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: během aktualizace proces převezme zámek na novém obsahu, který se musí zkopírovat do virtuálního počítače certifikační autority. V případě, že se aktualizace nezdařila, je zámek uvolněn.
- Náprava: pokračovat v aktualizaci.
- Výskyt: vzácná

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
- Příčina: na portálu User Portal se názvy účtů úložiště zobrazují jako **účet úložiště – objekt blob, soubor, tabulka, fronta** ; v Azure Stackovém centru se ale nepodporuje **soubor** .
- Výskyt: běžné

### <a name="create-managed-disk-snapshot"></a>Vytvořit snímek spravovaného disku

- Platí: Tento problém se týká verze 2002.
- Příčina: při vytváření snímku spravovaného disku na portálu User Portal je pole **typ účtu** prázdné. Když vyberete tlačítko **vytvořit** s prázdným typem účtu, vytvoření snímku se nezdařilo.
- Náprava: Vyberte typ účtu v rozevíracím seznamu **typ účtu** a pak vytvořte snímek.
- Výskyt: běžné

### <a name="alert-for-network-interface-disconnected"></a>Výstraha pro síťové rozhraní odpojena

- Platí: Tento problém se týká 1908 a novějších verzí.
- Příčina: když je kabel odpojený od síťového adaptéru, výstraha se na portálu pro správu nezobrazí. Příčinou této chyby je, že tato chyba je ve výchozím nastavení ve Windows serveru 2019 zakázaná.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="denyalloutbound-rule-cannot-be-created"></a>Pravidlo DenyAllOutbound nejde vytvořit.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: explicitní pravidlo **DenyAllOutbound** na Internet nejde během vytváření virtuálního počítače vytvořit v NSG, protože to zabrání dokončení komunikace vyžadované pro nasazení virtuálního počítače.
- Náprava: během vytváření virtuálního počítače povolte odchozí přenosy na Internet a po dokončení vytváření virtuálního počítače změňte NSG tak, aby zablokovala požadovaný provoz.
- Výskyt: běžné

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>Protokol ICMP není pro pravidla NSG podporován.

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: při vytváření příchozího nebo odchozího pravidla zabezpečení sítě zobrazí možnost **protokol možnost protokolu** **ICMP** . V tuto chvíli se v Azure Stackovém centru nepodporuje. Tento problém je opravený a v příští verzi Azure Stack centra se nezobrazí.
- Výskyt: běžné

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>Nejde odstranit NSG, pokud nejsou připojené síťové karty ke spuštěnému virtuálnímu počítači.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při zrušení přidružení NSG a síťové karty, která není připojená ke spuštěnému virtuálnímu počítači, se operace aktualizace (PUT) pro tento objekt na vrstvě síťového adaptéru nezdařila. NSG se aktualizuje na vrstvu poskytovatele síťových prostředků, ale ne na síťovém adaptéru, takže NSG se přesune do stavu selhání.
- Náprava: Připojte síťové karty přidružené k NSG, které je třeba odebrat se spuštěnými virtuálními počítači, a zrušte přidružení NSG nebo odeberte všechny síťové karty, které byly přidruženy k NSG.
- Výskyt: běžné

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer směrování provozu na jeden back-end virtuální počítač v určitých scénářích 

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: při povolování **spřažení relace** na nástroji pro vyrovnávání zatížení se 2 řazená kolekce členů použije místo privátních IP adres přiřazených k virtuálním počítačům IP adresa PA (IP adresa fyzické adresy). Ve scénářích, kdy se provoz směrovaného na nástroj pro vyrovnávání zatížení dorazí přes síť VPN, nebo pokud je povolené, aby se všechny virtuální počítače klienta (zdrojové IP adresy) nacházely na stejném uzlu a spřažení relace, veškerý provoz se směruje na jeden back-end virtuální počítač.
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

### <a name="public-ip"></a>Veřejná IP adresa

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: hodnota **IdleTimeoutInMinutes** pro veřejnou IP adresu, která je přidružená k nástroji pro vyrovnávání zatížení, se nedá změnit. Tato operace umístí veřejnou IP adresu do stavu selhání.
- Náprava: Chcete-li přenést veřejnou IP adresu do úspěšného stavu, změňte hodnotu **IdleTimeoutInMinutes** u pravidla vyrovnávání zatížení, které odkazuje na veřejnou IP adresu zpět na původní hodnotu (výchozí hodnota je 4 minuty).
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [Skladové položky brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Compute

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>Nejde vytvořit sadu škálování virtuálního počítače s Standard_DS2_v2 velikostí virtuálního počítače na portálu.

- Platí: Tento problém se týká verze 2002.
- Příčina: došlo k chybě portálu, která znemožňuje vytvoření sady škálování virtuálního počítače s Standard_DS2_v2 velikostí virtuálního počítače. Při jeho vytváření dojde k chybě: "{" Code ":" DeploymentFailed "," Message ":" nejméně jedna operace nasazení prostředků se nezdařila. Podrobnosti najdete ve výpisu operací nasazení. Podrobnosti o https://aka.ms/arm-debug využití najdete v podrobnostech o použití. "," Details ": [{" Code ":" důvodu chybného požadavku "," Message ":" {\r\n \" Error \" : {\r\n \" Code \" : \" NetworkProfileValidationError \" , \r\n \" zpráva \" : \" Velikost virtuálního počítače Standard_DS2_v2 není v seznamu povolených velikostí virtuálních počítačů pro urychlené síťové služby na virtuálním počítači v indexu 0 pro/Subscriptions/x/resourceGroups/RGVMSS/Providers/Microsoft.COMPUTE/virtualMachineScaleSets/VMSS. VM Scale set. Povolené velikosti:. \" \r\n} \r\n} "}]}"
- Náprava: Vytvořte sadu škálování virtuálního počítače pomocí PowerShellu nebo šablony Resource Manageru.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>Okno s přehledem virtuálních počítačů nezobrazuje správný název počítače

- Platí: Tento problém se vztahuje na všechny verze.
- Příčina: při zobrazení podrobností o VIRTUÁLNÍm počítači v okně Přehled se zobrazí název počítače **(není k dispozici)**. Jedná se o návrh pro virtuální počítače vytvořené z specializovaných disků nebo snímků disku a zobrazují se také pro image na webu Marketplace.
- Náprava: v části **Nastavení** zobrazte okno **vlastnosti** .

### <a name="nvv4-vm-size-on-portal"></a>Velikost virtuálního počítače NVv4 na portálu

- Platí: Tento problém se týká verze 2002 a novější.
- Příčina: při přechodu přes prostředí pro vytváření virtuálních počítačů se zobrazí velikost virtuálního počítače: NV4as_v4. Zákazníci, kteří mají k dispozici hardware potřebný pro procesory GPU Azure Stack v systému AMD ve verzi Preview, mohou mít úspěšné nasazení virtuálního počítače. U všech ostatních zákazníků dojde k neúspěšnému nasazení virtuálního počítače pomocí této velikosti virtuálního počítače.
- Náprava: návrh při přípravě na rozhraní Azure Stack hub GPU ve verzi Preview.

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při vytváření nového virtuálního počítače se může zobrazit následující chyba: **nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name**. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.
- Náprava: vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: běžné

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: při pokusu o spuštění virtuálního počítače, který není přidělen, se může zobrazit následující chyba: **účet úložiště diagnostiky virtuálních počítačů ' diagnosticstorageaccount ' nebyl nalezen. Ujistěte se, že se účet úložiště neodstranil**. K této chybě dojde, pokud se pokusíte spustit virtuální počítač s povolenou diagnostikou spouštění, ale odkazovaný účet úložiště diagnostiky spouštění se odstraní.
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

### <a name="sql-vm"></a>Virtuální počítač SQL

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>Při konfiguraci automatického zálohování se nepovedlo vytvořit účet úložiště.

- Platí: Tento problém se týká 2002.
- Příčina: když konfigurujete automatizované zálohování virtuálních počítačů SQL pomocí nového účtu úložiště, dojde k selhání při **ověřování šablony nasazení chyba. Parametr šablony pro ' SqlAutobackupStorageAccountKind ' nebyl nalezen.**
- Náprava: použijte nejnovější opravu hotfix 2002.

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>Automatické zálohování nejde nakonfigurovat s povoleným TLS 1,2.

- Platí: Tento problém se týká nových instalací 2002 a novějších verzí nebo jakékoli předchozí verze s povoleným protokolem TLS 1,2.
- Příčina: při konfiguraci automatizované zálohy virtuálních počítačů SQL s existujícím účtem úložiště dojde k selhání s chybou **SQL Server agenta IaaS: základní připojení bylo ukončeno: došlo k neočekávané chybě při odeslání.**
- Výskyt: běžné

## <a name="storage"></a>Úložiště

### <a name="retention-period-revert-to-0"></a>Doba uchování se vrátí na 0.

- Platí: Tento problém se týká verze 2002 a 2005.
- Příčina: Pokud jste dřív zadali časové období jiné než 0 v nastavení doby uchovávání, vrátí se zpět na 0 (výchozí hodnota tohoto nastavení) během aktualizace 2002 a 2005. A nastavení 0 dnů se projeví immdiately po dokončení aktualizace, což způsobí, že všechny existující odstraněné účty úložiště a všechny nadcházející nově odstraněné účty úložiště budou okamžitě neuchovávány a jsou označené pro periodické uvolňování paměti (které běží každou hodinu). 
- Náprava: ručně zadejte dobu uchování do správného období. Nicméně všechny účty úložiště, které už byly shromážděné paměti před tím, než je zadaná nová doba uchování, nejde obnovit.  

## <a name="resource-providers"></a>Poskytovatelé prostředků

### <a name="sqlmysql"></a>SQL/MySQL

- Platí: Tento problém se týká verze 2002. 
- Příčina: Pokud razítko obsahuje poskytovatele prostředků SQL (RP) verze 1.1.33.0 nebo starší, po aktualizaci razítka se nenačte i okna pro SQL/MySQL.
- Náprava: aktualizujte RP na verzi 1.1.47.0

### <a name="app-service"></a>App Service

- Platí: Tento problém se týká verze 2002.
- Příčina: Pokud razítko obsahuje App Service Resource Provider (RP) verze 1,7 a starší, po aktualizaci razítka se okna pro App Service nenačte.
- Náprava: aktualizujte RP na verzi [2020 F2](azure-stack-app-service-update.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archiv

Chcete-li získat přístup k archivovaným známým problémům pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- [Kontrola kontrolního seznamu aktivit aktualizací](release-notes-checklist.md)
- [Kontrola seznamu aktualizací zabezpečení](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>1910 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>1908 archivovaných známých problémů
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>1907 archivovaných známých problémů
::: moniker-end
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

::: moniker range="<azs-2002"
Ke [známým problémům centra Azure Stack můžete přistupovat v Galerii TechNet](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře centra Azure Stack najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end
