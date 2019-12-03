---
title: Řešení potíží s Microsoft Azure Stack | Microsoft Docs
description: Azure Stack řešení potíží.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 28e067abef24fbc60cff629e5112ffacdf14b1ec
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689945"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Řešení potíží s Microsoft Azure Stack

Tento dokument poskytuje informace pro řešení potíží s Azure Stack integrovanými prostředími. Nápovědu k Azure Stack Development Kit najdete v tématu [řešení potíží s ASDK](../asdk/asdk-troubleshooting.md) nebo Získejte pomoc od odborníků na [Azure Stack MSDN fóra](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tyto části obsahují odkazy na dokumenty, které pokrývají běžné otázky odeslané službám Microsoft Customer Support Services (CSS).

### <a name="purchase-considerations"></a>Předpoklady nákupu

* [Možnosti nákupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Přehled Azure Stack](azure-stack-overview.md)

### <a name="updates-and-diagnostics"></a>Aktualizace a diagnostika

* [Použití diagnostických nástrojů v Azure Stack](azure-stack-diagnostics.md)
* [Ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md)
* [Aktualizace tempo verze balíčku](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Podporované operační systémy a velikosti pro virtuální počítače hosta

* [Podporované hostované operační systémy ve službě Azure Stack](azure-stack-supported-os.md)
* [Podporované velikosti virtuálních počítačů ve službě Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Dostupné položky Azure Marketplace pro službu Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Správa kapacity

#### <a name="memory"></a>Paměť

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti pro službu Azure Stack, můžete přidat další paměť. Ve službě Azure Stack se fyzický server označuje také jako uzel jednotky škálování. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít [stejnou velikost paměti](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Doba uchování

Nastavení doby uchovávání umožňuje operátorovi cloudu nastavit časové období ve dnech (od 0 do 9 999 dnů), během kterého je potenciálně možné obnovit jakýkoli odstraněný účet. Výchozí doba uchování je nastavená na **0** dní. Nastavení hodnoty na **0** znamená, že libovolný odstraněný účet je ihned neuchováván a označený pro periodické uvolňování paměti.

* [Nastavení doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Zabezpečení, dodržování předpisů a identita  

#### <a name="manage-rbac"></a>Správa RBAC

Uživatel ve službě Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem každé instance předplatného, skupiny prostředků nebo služby.

* [Správa RBAC ve službě Azure Stack](azure-stack-manage-permissions.md)

Pokud předdefinované role pro prostředky Azure nesplňují konkrétní požadavky vaší organizace, můžete si vytvořit vlastní role. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory.

* [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Správa využití a fakturace jako poskytovatel CSP

* [Správa využití a fakturace jako poskytovatel CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Vytvoření předplatného CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Zvolte typ účtu sdílených služeb, který používáte pro službu Azure Stack. K registraci služby Azure Stack pro více tenantů je možné použít následující typy předplatných:

* Cloud Solution Provider
* Předplatné Partner Shared Services

### <a name="get-scale-unit-metrics"></a>Získat metriky jednotek škálování

Pomocí prostředí PowerShell můžete získat informace o využití razítka bez nutnosti pomáhat z šablon stylů CSS. Získání využití razítka: 

1. Vytvoření relace PEP
2. Spuštění rutiny Test-azurestack
3. Ukončit relaci PEP
4. Spuštění rutiny Get-azurestacklog-filterbyrole s použitím volání Invoke-Command
5. Extrahujte soubor seedring. zip a můžete získat zprávu o ověření ze složky ERCS, ve které jste spustili test-azurestack

Další informace najdete v tématu [diagnostika Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="troubleshoot-virtual-machines"></a>Řešení potíží s virtuálními počítači
### <a name="default-image-and-gallery-item"></a>Výchozí položka obrázku a galerie
Před nasazením virtuálních počítačů v Azure Stack je třeba přidat položku galerie a image Windows serveru.


### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk"></a>Odstranil (a) jsem některé virtuální počítače, ale pořád zobrazuje soubory VHD na disku
Toto chování je záměrné:

* Při odstranění virtuálního počítače se virtuální pevné disky neodstraňují. Disky jsou samostatné prostředky ve skupině prostředků.
* Když se účet úložiště odstraní, odstraní se hned po Azure Resource Manager, ale disky, které můžou obsahovat, se pořád uchovávají v úložišti, až do doby, než se spustí shromažďování paměti.

Pokud se zobrazí "osamocené" disky VHD, je důležité znát, jestli jsou součástí složky pro účet úložiště, který se odstranil. Pokud se účet úložiště neodstranil, je normální, ale pořád tam.

Další informace o konfiguraci prahové hodnoty pro uchování a opětovného získávání na vyžádání najdete v tématu [Správa účtů úložiště](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Řešení potíží s úložištěm
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin, než se kapacita uvolní, aby se na portálu zobrazovala. Recyklace místa závisí na různých faktorech, včetně procentuálního využití vnitřních souborů kontejneru v úložišti objektů blob bloku. V závislosti na tom, kolik dat je odstraněno, však není zaručeno množství místa, které by mohlo být uvolněno při spuštění systému uvolňování paměti.

### <a name="azure-storage-explorer-not-working-with-azure-stack"></a>Průzkumník služby Azure Storage nepracuje s Azure Stack 
 
Pokud používáte integrovaný systém v odpojeném scénáři, doporučuje se používat certifikační autoritu (CA) organizace. Exportujte kořenový certifikát ve formátu Base-64 a pak ho importujte do Průzkumník služby Azure Storage. Nezapomeňte odebrat koncové lomítko ('/') z koncového bodu ARM. Další informace najdete v tématu [Příprava na připojení k Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se#prepare-for-connecting-to-azure-stack).
 

## <a name="troubleshooting-app-service"></a>Řešení potíží s App Service
### <a name="create-aadidentityappps1-script-fails"></a>Skript Create-AADIdentityApp. ps1 se nezdařil

Pokud skript Create-AADIdentityApp. ps1, který je vyžadován pro App Service, neproběhne úspěšně, nezapomeňte při spuštění skriptu zahrnout parametr Required-AzureStackAdminCredential. Další informace najdete v tématu [předpoklady pro nasazení App Service v Azure Stack](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app).

