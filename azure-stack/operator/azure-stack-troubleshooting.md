---
title: Řešení potíží s Azure Stack hub
titleSuffix: Azure Stack
description: Naučte se řešit potíže s centrem Azure Stack, včetně problémů s virtuálními počítači, úložištěm a App Service.
author: myoungerman
ms.topic: article
ms.date: 07/21/2020
ms.author: v-myoung
ms.reviewer: prchint
ms.lastreviewed: 07/21/2020
ms.openlocfilehash: 290f6ba7a8f3c53aafe131dd5c8de5186b88d752
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899767"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Řešení potíží v centru Azure Stack

Tento dokument poskytuje informace pro řešení potíží s integrovanými prostředími centra Azure Stack. Nápovědu k Azure Stack Development Kit najdete v tématu [řešení potíží s ASDK](../asdk/asdk-troubleshooting.md) nebo Získejte pomoc od expertů ve [fóru Azure Stack centra MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tyto části obsahují odkazy na dokumenty, které pokrývají běžné otázky odeslané podpora Microsoftu.

### <a name="purchase-considerations"></a>Důležité informace o nákupu

* [Možnosti nákupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Přehled centra Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Aktualizace a diagnostika

* [Použití diagnostických nástrojů v centru Azure Stack](./azure-stack-diagnostic-log-collection-overview.md)
* [Ověření stavu systému Azure Stack hub](azure-stack-diagnostic-test.md)
* [Aktualizace tempo verze balíčku](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Ověření a řešení potíží se stavem uzlu](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Podporované operační systémy a velikosti pro virtuální počítače hosta

* [Podporované hostované operační systémy ve službě Azure Stack Hub](azure-stack-supported-os.md)
* [Podporované velikosti virtuálních počítačů v centru Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Dostupné položky Azure Marketplace pro službu Azure Stack Hub](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Správa kapacity

#### <a name="memory"></a>Paměť

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti pro centrum Azure Stack, můžete přidat další paměť. V Azure Stackovém centru se váš fyzický server označuje také jako uzel jednotky škálování. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít [stejnou velikost paměti](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Doba uchovávání

Nastavení Doba uchování umožňuje operátorovi cloudu zadat časové období ve dnech (mezi 0 a 9999 dny), během kterého může být kterýkoli odstraněný účet potenciálně obnoven. Výchozí doba uchování je nastavená na **0** dní. Nastavení hodnoty na **0** znamená, že libovolný odstraněný účet je ihned neuchováván a označený pro periodické uvolňování paměti.

* [Nastavení doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Zabezpečení, dodržování předpisů a identita  

#### <a name="manage-rbac"></a>Správa RBAC

Uživatel v centru Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem pro každou instanci předplatného, skupiny prostředků nebo služby.

* [Centrum Azure Stack Správa RBAC](azure-stack-manage-permissions.md)

Pokud předdefinované role pro prostředky Azure nesplňují konkrétní požadavky vaší organizace, můžete si vytvořit vlastní role. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory.

* [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShell](/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Správa využití a fakturace jako poskytovatel CSP

* [Správa využití a fakturace jako poskytovatel CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Vytvoření předplatného CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Vyberte typ účtu sdílených služeb, který používáte pro Azure Stack hub. Typy předplatných, které se dají použít k registraci Azure Stackho centra pro více tenantů, jsou:

* Program Cloud Solution Provider
* Předplatné Partner Shared Services

### <a name="get-scale-unit-metrics"></a>Získat metriky jednotek škálování

Pomocí PowerShellu můžete získat informace o využití razítka bez nutnosti pomáhat podpora Microsoftu. Získání využití razítka:

1. Vytvořte relaci PEP.
2. Spusťte příkaz `test-azurestack`.
3. Ukončete relaci PEP.
4. Spusťte `get-azurestacklog -filterbyrole seedring` pomocí volání vyvolání příkazu.
5. Extrahujte soubor seedring. zip. Sestavu ověření můžete získat ze složky ERCS, ve které jste spustili `test-azurestack` .

Další informace najdete v tématu [Diagnostika centra Azure Stack](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Řešení potíží s virtuálními počítači

### <a name="reset-linux-vm-password"></a>Resetování hesla virtuálního počítače s Linuxem

Pokud zapomenete heslo pro virtuální počítač se systémem Linux a možnost **resetovat heslo** nefunguje kvůli problémům s rozšířením VMAccess, můžete provést resetování pomocí následujících kroků:

1. Vyberte virtuální počítač se systémem Linux, který chcete použít jako virtuální počítač pro obnovení.

1. Přihlaste se k portálu User Portal:
   1. Poznamenejte si velikost virtuálního počítače, síťovou kartu, veřejnou IP adresu, NSG a datové disky.
   1. Zastavte ovlivněný virtuální počítač.
   1. Odeberte ovlivněný virtuální počítač.
   1. Připojte disk z ovlivněného virtuálního počítače jako datový disk na virtuálním počítači pro obnovení (k dispozici může trvat několik minut, než bude disk dostupný).

1. Přihlaste se k virtuálnímu počítači pro obnovení a spusťte následující příkaz:

   ```
   sudo su –
   mkdir /tempmount
   fdisk -l
   mount /dev/sdc2 /tempmount /*adjust /dev/sdc2 as necessary*/
   chroot /tempmount/
   passwd root /*substitute root with the user whose password you want to reset*/
   rm -f /.autorelabel /*Remove the .autorelabel file to prevent a time consuming SELinux relabel of the disk*/
   exit /*to exit the chroot environment*/
   umount /tempmount
   ```

1. Přihlaste se k portálu User Portal:

   1. Odpojte disk od virtuálního počítače pro obnovení.
   1. Znovu vytvořte virtuální počítač z disku.
   1. Nezapomeňte přenést veřejnou IP adresu z předchozího virtuálního počítače, připojit datové disky atd.


Můžete také pořídit snímek původního disku a vytvořit z něj nový disk místo toho, aby se změny prováděly přímo na původním disku. Další informace najdete v těchto tématech:

- [Resetování hesla](/azure/virtual-machines/troubleshooting/reset-password)
- [Vytvoření disku ze snímku](/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-portal-linux#create-a-disk-from-the-snapshot)
- [Změna a resetování hesla root](https://access.redhat.com/documentation/red_hat_enterprise_linux/7/html/system_administrators_guide/sec-terminal_menu_editing_during_boot#sec-Changing_and_Resetting_the_Root_Password)


### <a name="license-activation-fails-for-windows-server-2012-r2-during-provisioning"></a>Aktivace licence pro Windows Server 2012 R2 během zřizování se nezdařila.

V takovém případě se Windows nepodaří aktivovat a v pravém dolním rohu obrazovky se zobrazí vodoznak. Protokoly WaSetup.xml nacházející se v C:\Windows\Panther obsahuje následující událost:

```xml
<Event time="2019-05-16T21:32:58.660Z" category="ERROR" source="Unattend">
    <UnhandledError>
        <Message>InstrumentProcedure: Failed to execute 'Call ConfigureLicensing()'. Will raise error to caller</Message>
        <Number>-2147221500</Number>
        <Description>Could not find the VOLUME_KMSCLIENT product</Description>
        <Source>Licensing.wsf</Source>
    </UnhandledError>
</Event>
```


Licenci aktivujete tak, že zkopírujete klíč automatických aktivací virtuálního počítače (AVMA) pro SKLADOVOU položku, kterou chcete aktivovat.

|Edice|AVMA klíč|
|-|-|
|Datové centrum|Y4TGP-NPTV9-HTC2H-7MGQ3-DV4TW|
|Standard|DBGBW-NPF86-BJVTX-K3WKJ-MTB6V|
|Základy|K2XGM-NMBT3-2R6Q8-WF2FK-P36R2|

Na virtuálním počítači spusťte následující příkaz:

```powershell
slmgr /ipk <AVMA_key>
```

Úplné podrobnosti najdete v tématu [aktivace virtuálního počítače](/windows-server/get-started-19/vm-activation-19).

### <a name="default-image-and-gallery-item"></a>Výchozí položka obrázku a galerie

Před nasazením virtuálních počítačů do centra Azure Stack je třeba přidat položku galerie a image Windows serveru.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Odstranili jsme některé virtuální počítače, ale pořád se na disku zobrazují soubory VHD

Toto chování je záměrné:

* Při odstranění virtuálního počítače se virtuální pevné disky neodstraňují. Disky jsou samostatné prostředky ve skupině prostředků.
* Když se účet úložiště odstraní, odstraní se hned po Azure Resource Manager. Disky, které mohou obsahovat, jsou však nadále uchovávány v úložišti, dokud nebude spuštěno uvolňování paměti.

Pokud se zobrazí "osamocené" disky VHD, je důležité znát, jestli jsou součástí složky pro účet úložiště, který se odstranil. Pokud se účet úložiště neodstranil, je normální, že tam pořád máte.

Další informace o konfiguraci prahové hodnoty pro uchování a opětovného získávání na vyžádání najdete v tématu [Správa účtů úložiště](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Řešení potíží s úložištěm

### <a name="storage-reclamation"></a>Recyklace úložiště

Může trvat až 14 hodin, než se kapacita uvolní, aby se na portálu zobrazovala. Recyklace místa závisí na různých faktorech, včetně procentuálního využití vnitřních souborů kontejneru v úložišti objektů blob bloku. V závislosti na tom, kolik dat je odstraněno, však není nijak zaručeno množství místa, které by mohlo být uvolněno při spuštění systému uvolňování paměti.

### <a name="azure-storage-explorer-not-working-with-azure-stack-hub"></a>Průzkumník služby Azure Storage nepracuje se službou Azure Stack hub

Pokud používáte integrovaný systém v odpojeném scénáři, doporučuje se používat certifikační autoritu (CA) organizace. Exportujte kořenový certifikát ve formátu Base-64 a pak ho importujte do Průzkumník služby Azure Storage. Nezapomeňte odebrat koncové lomítko ( `/` ) z správce prostředkůho koncového bodu. Další informace najdete v tématu [Příprava na připojení k centru Azure Stack](../user/azure-stack-storage-connect-se.md).

## <a name="troubleshoot-app-service"></a>Řešení potíží s App Service

### <a name="create-aadidentityappps1-script-fails"></a>Skript Create-AADIdentityApp.ps1 se nezdařil

Pokud skript Create-AADIdentityApp.ps1, který je požadován pro App Service, se nezdařil, nezapomeňte `-AzureStackAdminCredential` při spuštění skriptu zahrnout požadovaný parametr. Další informace najdete v tématu [předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app).

## <a name="troubleshoot-azure-stack-hub-updates"></a>Řešení potíží s aktualizacemi centra Azure Stack

Proces aktualizace a aktualizace centra Azure Stack slouží k tomu, aby operátoři mohli instalovat balíčky aktualizací konzistentním a efektivnějším způsobem. V neobvyklém případě mohou nastat problémy během procesu aktualizace a aktualizace. V následujících krocích se doporučuje, abyste při procesu aktualizace a aktualizace nastavili problém:

0. **Požadavky**: Ujistěte se, že jste následovali [Kontrolní seznam aktivity aktualizace](release-notes-checklist.md) a [povolili proaktivní shromažďování protokolů](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively).

1. Postupujte podle kroků pro nápravu v upozornění na selhání vytvořeného při selhání aktualizace.

2. Pokud jste tento problém nedokázali vyřešit, vytvořte [lístek podpory centra Azure Stack](./azure-stack-help-and-support-overview.md?view=azs-2002). Ujistěte se, že máte [shromážděné protokoly](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now) pro časové období, kdy k problému došlo.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Běžné problémy s opravou a aktualizacemi centra Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra*

### <a name="preparationfailed"></a>PreparationFailed

**Platí**: Tento problém se vztahuje na všechny podporované verze.

**Příčina**: při pokusu o instalaci aktualizace centra Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na `PreparationFailed` . Pro systémy připojené k Internetu je to obvykle informativní jako aktualizace balíčku aktualizace, který se nedá správně stáhnout z důvodu slabého připojení k Internetu. 

**Náprava**: Tento problém můžete obejít tak, že znovu kliknete na **nainstalovat** . Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí oddílu [instalovat aktualizace](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) .

**Výskyt**: běžné

::: moniker range="azs-2002"
### <a name="2002-update-failed"></a>aktualizace 2002 se nezdařila

**Použitelné**: Tento problém se týká jenom verze 2002.

**Příčina**: při pokusu o aktualizaci 2002 může aktualizace selhat a poskytnout tuto zprávu: `The private network parameter is missing from cloud parameters. Please use set-azsprivatenetwork cmdlet to set private networkTrace` .

**Náprava**: [nastavte privátní interní síť](./azure-stack-network.md?view=azs-2002#private-network).
::: moniker-end