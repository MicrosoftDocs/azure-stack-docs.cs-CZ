---
title: Řešení potíží s Azure Stack hub
titleSuffix: Azure Stack
description: Naučte se řešit potíže s centrem Azure Stack, včetně problémů s virtuálními počítači, úložištěm a App Service.
author: justinha
ms.topic: article
ms.date: 04/30/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d85ee89edc6b36fb9eca84f59867621b37e8dd5a
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777878"
---
# <a name="troubleshoot-issues-in-azure-stack-hub"></a>Řešení potíží v centru Azure Stack

Tento dokument poskytuje informace pro řešení potíží s integrovanými prostředími centra Azure Stack. Nápovědu k Azure Stack Development Kit najdete v tématu [řešení potíží s ASDK](../asdk/asdk-troubleshooting.md) nebo Získejte pomoc od expertů ve [fóru Azure Stack centra MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tyto části obsahují odkazy na dokumenty, které pokrývají běžné otázky odeslané službám Microsoft Customer Support Services (CSS).

### <a name="purchase-considerations"></a>Předpoklady nákupu

* [Možnosti nákupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Přehled centra Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Aktualizace a diagnostika

* [Použití diagnostických nástrojů v centru Azure Stack](azure-stack-diagnostics.md)
* [Ověření stavu systému Azure Stack hub](azure-stack-diagnostic-test.md)
* [Aktualizace tempo verze balíčku](azure-stack-servicing-policy.md#update-package-release-cadence)
* [Ověření a řešení potíží se stavem uzlu](azure-stack-node-actions.md)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Podporované operační systémy a velikosti pro virtuální počítače hosta

* [Hostované operační systémy podporované v centru Azure Stack](azure-stack-supported-os.md)
* [Podporované velikosti virtuálních počítačů v centru Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Azure Marketplace dostupné položky pro centrum Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Správa kapacity

#### <a name="memory"></a>Memory (Paměť)

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti pro centrum Azure Stack, můžete přidat další paměť. V Azure Stackovém centru se váš fyzický server označuje také jako uzel jednotky škálování. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít [stejnou velikost paměti](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Doba uchovávání

Nastavení Doba uchování umožňuje operátorovi cloudu zadat časové období ve dnech (mezi 0 a 9999 dny), během kterého může být kterýkoli odstraněný účet potenciálně obnoven. Výchozí doba uchování je nastavená na **0** dní. Nastavení hodnoty na **0** znamená, že libovolný odstraněný účet je ihned neuchováván a označený pro periodické uvolňování paměti.

* [Nastavení doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Zabezpečení, dodržování předpisů a identita  

#### <a name="manage-rbac"></a>Správa RBAC

Uživatel v centru Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem pro každou instanci předplatného, skupiny prostředků nebo služby.

* [Centrum Azure Stack Správa RBAC](azure-stack-manage-permissions.md)

Pokud předdefinované role pro prostředky Azure nesplňují konkrétní požadavky vaší organizace, můžete si vytvořit vlastní role. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory.

* [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Správa využití a fakturace jako poskytovatel CSP

* [Správa využití a fakturace jako poskytovatel CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Vytvoření předplatného CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Vyberte typ účtu sdílených služeb, který používáte pro Azure Stack hub. Typy předplatných, které se dají použít k registraci Azure Stackho centra pro více tenantů, jsou:

* Program Cloud Solution Provider
* Předplatné Partner Shared Services

### <a name="get-scale-unit-metrics"></a>Získat metriky jednotek škálování

Pomocí prostředí PowerShell můžete získat informace o využití razítka bez nutnosti pomáhat z šablon stylů CSS. Získání využití razítka:

1. Vytvořte relaci PEP.
2. Spusťte `test-azurestack`.
3. Ukončete relaci PEP.
4. Spusťte `get-azurestacklog -filterbyrole seedring` pomocí volání vyvolání příkazu.
5. Extrahujte soubor seedring. zip. Sestavu ověření můžete získat ze složky ERCS, ve které jste spustili `test-azurestack`.

Další informace najdete v tématu [Diagnostika centra Azure Stack](azure-stack-get-azurestacklog.md).

## <a name="troubleshoot-virtual-machines-vms"></a>Řešení potíží s virtuálními počítači

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

Pokud používáte integrovaný systém v odpojeném scénáři, doporučuje se používat certifikační autoritu (CA) organizace. Exportujte kořenový certifikát ve formátu Base-64 a pak ho importujte do Průzkumník služby Azure Storage. Nezapomeňte odebrat koncové lomítko (`/`) z správce prostředkůho koncového bodu. Další informace najdete v tématu [Příprava na připojení k centru Azure Stack](/azure-stack/user/azure-stack-storage-connect-se).

## <a name="troubleshooting-app-service"></a>Řešení potíží s App Service

### <a name="create-aadidentityappps1-script-fails"></a>Skript Create-AADIdentityApp. ps1 se nezdařil

Pokud skript Create-AADIdentityApp. ps1 vyžadovaný pro App Service selhává, nezapomeňte při spuštění skriptu zahrnout požadovaný `-AzureStackAdminCredential` parametr. Další informace najdete v tématu [předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).
