---
title: Aktualizace 1903 Azure Stack | Microsoft Docs
description: Seznamte se s aktualizací 1903 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 0038f36c8eaab9372152aebd6df3ba9103ec4552
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009005"
---
# <a name="azure-stack-1903-update"></a>Aktualizace Azure Stack 1903

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1903. Tato aktualizace zahrnuje vylepšení, opravy a nové funkce pro tuto verzi Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz na stažení aktualizace. Známé problémy jsou rozděleny do potíží přímo souvisejících s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1903 je **1.1903.0.35**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1903 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](../azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1903 je přibližně 16 hodin, ale přesné časy se mohou lišit. Tato přibližná doba běhu je specifická pro aktualizaci 1903 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

> [!IMPORTANT]
> Datová část 1903 neobsahuje verzi ASDK.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1903 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1902.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1902**: [KB 4500637 – Azure Stack hotfix 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**: [KB 4500638 – Azure Stack hotfix 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Vylepšen

- Opravili jsme chybu v síti, která zabránila změně hodnoty **časového limitu nečinnosti** **veřejné IP adresy** (minuty). Dříve byly změny této hodnoty ignorovány, takže bez ohledu na provedené změny by byla hodnota výchozí na 4 minuty. Toto nastavení určuje, kolik minut má být otevřeno připojení TCP, aniž by se museli spoléhat na to, že klienti odesílají zprávy Keep-Alive. Poznámka: Tato chyba se týká pouze veřejných IP adres na úrovni instance, nikoli veřejných IP adres přiřazených k nástroji pro vyrovnávání zatížení.

- Vylepšení spolehlivosti stroje aktualizací, včetně automatické nápravy běžných problémů, aby se aktualizace projevily bez přerušení.

- Vylepšení detekce a nápravy stavů nedostatku místa na disku.

- Azure Stack teď podporuje agenty Windows Azure Linux větší než 2.2.35 verze. Tato podpora umožňuje zákazníkům udržovat konzistentní image Linux mezi Azure a Azure Stack. Byla přidána jako součást oprav hotfix 1901 a 1902.

### <a name="secret-management"></a>Správa tajných kódů

- Azure Stack teď podporuje rotaci kořenového certifikátu používaného certifikáty pro rotaci externích tajných klíčů. Další informace [najdete v tomto článku](../azure-stack-rotate-secrets.md).

- 1903 obsahuje vylepšení výkonu pro rotaci tajných klíčů, které zkracuje dobu potřebnou k provedení interního otočení v tajných klíčích.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Před aktualizací na 1903 nainstalujte nejnovější Azure Stack opravu hotfix pro 1902 (pokud existuje).

- Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi [plánovače kapacity Azure Stack](https://aka.ms/azstackcapacityplanner) . Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydány s každou aktualizací Azure Stack.

- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícím parametrem pro ověření stavu vašeho Azure Stack a vyřešte všechny nalezené provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují:

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Pokud Azure Stack spravujete pomocí System Center Operations Manager, nezapomeňte před použitím 1903 aktualizovat [sadu Management Pack pro Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) na verzi 1.0.3.11.

- Formát balíčku pro aktualizaci Azure Stack se změnil z **. bin/. exe/. XML** na **. zip/. xml** počínaje verzí 1902. Zákazníci s připojenými jednotkami škálování Azure Stack uvidí na portálu zprávu **aktualizace k dispozici** . Zákazníci, kteří nejsou připojeni, teď můžou jednoduše stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a znovu spustí stahování.

- Při spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md)se zobrazí zpráva s upozorněním z řadiče pro správu základní desky (BMC). Toto upozornění můžete klidně ignorovat.

<!-- 2468613 - IS -->
- Během instalace této aktualizace se můžou zobrazit výstrahy s názvem **Chyba – šablona pro typ FaultType UserAccounts. Chybí nový.** Tyto výstrahy můžete bezpečně ignorovat. Výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

## <a name="post-update-steps"></a>Kroky po aktualizaci

- Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu [opravy hotfix](#hotfixes)a [zásady obsluhy](../azure-stack-servicing-policy.md).

- Načtěte data z neaktivních šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení Azure Stack. Postupujte podle [pokynů pro načtení klíčů](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

- Když se na řídicím panelu portálu User Portal pokusíte kliknout na dlaždici **zpětné vazby** , otevře se prázdná karta prohlížeče. Jako alternativní řešení můžete pomocí [Azure Stack uživatelského hlasu](https://aka.ms/azurestackuservoice) zadat požadavek na hlas uživatele.

<!-- 2930820 - IS ASDK -->
- Při hledání "Docker" na portále správce i v uživatelských portálech se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se okno s indikací chyby.

<!-- 2931230 - IS  ASDK -->
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku.

<!-- TBD - IS ASDK -->
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

<!-- 3557860 - IS ASDK -->
- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

<!-- 1663805 - IS ASDK --> 
- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je [ověřit oprávnění pomocí PowerShellu](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- Když v uživatelském portálu přejdete na objekt BLOB v rámci účtu úložiště a pokusíte se otevřít **zásady přístupu** z navigačního stromu, následné okno se nepovede načíst. Pokud chcete tento problém obejít, můžete následující rutiny PowerShellu povolit vytváření, načítání, nastavování a odstraňování zásad přístupu v uvedeném pořadí:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- Při pokusu o nahrání objektu BLOB pomocí možnosti **OAuth (Preview)** na portálu User Portal se úloha nezdařila s chybovou zprávou. Pokud chcete tento problém obejít, nahrajte objekt BLOB pomocí možnosti **SAS** .

- Když se přihlásíte k portálům Azure Stack, můžou se vám zobrazit oznámení o veřejné Azure Portal. Tato oznámení můžete bezpečně ignorovat, protože se v současnosti nevztahují na Azure Stack (například 1 nová aktualizace – k dispozici jsou teď tyto aktualizace: Azure Portal dubna 2019 Update).

- Když na řídicím panelu portálu User Portal vyberete dlaždici **zpětné vazby** , otevře se prázdná karta prohlížeče. Jako alternativní řešení můžete pomocí [Azure Stack uživatelského hlasu](https://aka.ms/azurestackuservoice) zadat požadavek na hlas uživatele.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění. Pokud chcete tento problém obejít, vytvořte znovu účet úložiště se stejným názvem, jako jste použili dříve.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Prostředí pro vytvoření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. Vzhledem k tomu, že tato image není k dispozici na Azure Stack Marketplace, buď vyberte jiný operační systém pro vaše nasazení, nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.

<!-- TBD - IS ASDK -->
- Po použití aktualizace 1903 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

   - Pokud bylo předplatné vytvořeno před aktualizací 1808, může při nasazení virtuálního počítače s Managed Disks dojít k chybě s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
      1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelé prostředků**, vyberte **Microsoft. COMPUTE** a potom klikněte na **znovu registrovat**.
      2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedeno **Azure Stack spravovaný disk** .
   - Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hosta, selhat chyba s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-a-guest-directory) a překonfigurujte všechny adresáře hostů.

- Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožní používat klíče SSH k přihlášení. Alternativním řešením je použít pro rozšíření pro Linux přístup k virtuálnímu počítači k implementaci klíčů SSH po zřízení nebo použití ověřování na základě hesla.

- Pokud nemáte hostitele životního cyklu hardwaru (HLH): před sestavením 1902, museli jste nastavit zásady skupiny **počítač \ \ Policies\Security možnosti** pro **odeslání LM & NTLM – při vyjednání použít zabezpečení relace NTLMv2**. Vzhledem k tomu, že sestavení 1902, je nutné ponechat ho jako **nedefinované** , nebo ho nastavit tak, aby **odesílal pouze odpovědi NTLMv2** (což je výchozí hodnota). V opačném případě nebudete moct vytvořit vzdálenou relaci PowerShellu a zobrazí se chyba **odepření přístupu** :

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- V okně **Virtual Machine Scale Sets** nemůžete odebrat sadu škálování. Jako alternativní řešení vyberte sadu škálování, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit** v podokně **Přehled** .

- Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly. Můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="networking"></a>Sítě

<!-- 3239127 - IS, ASDK -->
- Když v Azure Stackovém portálu změníte statickou IP adresu pro konfiguraci protokolu IP, která je vázaná na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s upozorněním.

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Tuto zprávu můžete ignorovat bez obav. IP adresa se změní i v případě, že se instance virtuálního počítače nerestartuje.

<!-- 3632798 - IS, ASDK -->
- Pokud v portálu přidáte příchozí pravidlo zabezpečení a jako zdroj vyberete možnost **označení služby** , v seznamu **zdrojových značek** se zobrazí několik možností, které nejsou k dispozici pro Azure Stack. Jediné možnosti, které jsou platné v Azure Stack, jsou následující:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**

  Ostatní možnosti nejsou podporovány jako zdrojové značky v Azure Stack. Podobně pokud přidáte odchozí pravidlo zabezpečení a jako cíl vyberete **značku služby** , zobrazí se stejný seznam možností pro **zdrojovou značku** . Jedinými platnými možnostmi jsou stejné možnosti jako u **zdrojové značky**, jak je popsáno v předchozím seznamu.

- Skupiny zabezpečení sítě (skupin zabezpečení sítě) nefungují v Azure Stack stejným způsobem jako globální Azure. V Azure můžete pro jedno pravidlo NSG nastavit několik portů (pomocí portálu, PowerShellu a šablon Správce prostředků). V Azure Stack ale nemůžete na jednom pravidlu NSG nastavit víc portů prostřednictvím portálu. Pokud chcete tento problém obejít, použijte k nastavení těchto dalších pravidel šablonu Správce prostředků nebo PowerShell.

<!-- 3203799 - IS, ASDK -->
- Azure Stack nepodporuje současné připojení více než 4 síťových rozhraní k instanci virtuálního počítače bez ohledu na velikost instance.

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK -->
- Klienti musí zaregistrovat poskytovatele prostředků úložiště před vytvořením první funkce Azure v předplatném.
- V důsledku nekompatibility s portálem v 1903 se některé uživatelské prostředí portálu klienta přerušilo. v podstatě i v uživatelském prostředí pro sloty nasazení, testování v produkčním prostředí a rozšířeních lokality. Pokud chcete tento problém obejít, použijte [modul Azure App Service PowerShell](/azure/app-service/deploy-staging-slots#automate-with-powershell) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/webapp/deployment/slot?view=azure-cli-latest&preserve-view=true). Prostředí portálu se obnoví v nadcházející verzi služby Azure App Service ve službě Azure Stack 1.6 (aktualizace 6).

<!-- ### Usage -->


<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- Konfigurace syslog není trvale prováděna prostřednictvím cyklu aktualizace, což způsobí ztrátu konfigurace klienta syslog a zastavení zpráv syslog. Tento problém se vztahuje na všechny verze Azure Stack od GA klienta syslog (1809). Pokud chcete tento problém obejít, překonfigurujte klienta syslog po použití aktualizace Azure Stack.

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizací Azure Stack 1903 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)

Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).