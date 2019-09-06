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
ms.date: 09/04/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/04/2019
ms.openlocfilehash: a9d62640b2baabfd3283099656719a880dd0a41b
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377250"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Řešení potíží s Microsoft Azure Stack

Tento dokument poskytuje informace pro řešení potíží s Azure Stack. 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tyto části obsahují odkazy na dokumenty, které pokrývají běžné otázky odeslané službám Microsoft Customer Support Services (CSS).

### <a name="purchase-considerations"></a>Předpoklady nákupu

* [Možnosti nákupu](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)
* [Přehled Azure Stack](azure-stack-overview.md)

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK)

Pro nápovědu k [Azure Stack Development Kit](../asdk/asdk-what-is.md)se můžete obrátit na odborníky na [webu Azure Stack MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). ASDK se nabízí jako zkušební prostředí bez podpory šablon stylů CSS. Případy podpory otevřené pro ASDK se označují jako Fórum MSDN.

### <a name="updates-and-diagnostics"></a>Aktualizace a diagnostika

* [Použití diagnostických nástrojů v Azure Stack](azure-stack-diagnostics.md)
* [Ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md)
* [Aktualizace tempo verze balíčku](azure-stack-servicing-policy.md#update-package-release-cadence)

### <a name="supported-operating-systems-and-sizes-for-guest-vms"></a>Podporované operační systémy a velikosti pro virtuální počítače hosta

* [Podporované hostované operační systémy ve službě Azure Stack](azure-stack-supported-os.md)
* [Podporované velikosti virtuálních počítačů v Azure Stack](../user/azure-stack-vm-sizes.md)

### <a name="azure-marketplace"></a>Azure Marketplace

* [Dostupné položky Azure Marketplace pro službu Azure Stack](azure-stack-marketplace-azure-items.md)

### <a name="manage-capacity"></a>Správa kapacity

#### <a name="memory"></a>Memory (Paměť)

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti pro službu Azure Stack, můžete přidat další paměť. Ve službě Azure Stack se fyzický server označuje také jako uzel jednotky škálování. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít [stejnou velikost paměti](azure-stack-manage-storage-physical-memory-capacity.md).

#### <a name="retention-period"></a>Období udržení

Nastavení doby uchovávání umožňuje operátorovi cloudu nastavit časové období ve dnech (od 0 do 9 999 dnů), během kterého je potenciálně možné obnovit jakýkoli odstraněný účet. Výchozí doba uchovávání je nastavená na 0 dnů. Nastavení na hodnotu 0 znamená, že se všechny odstraněné účty okamžitě vyjmou z uchovávání a označí se pro pravidelné uvolňování paměti.

* [Nastavení doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period)

### <a name="security-compliance-and-identity"></a>Zabezpečení, dodržování předpisů a identita  

#### <a name="manage-rbac"></a>Správa RBAC

Uživatel ve službě Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem každé instance předplatného, skupiny prostředků nebo služby.

* [Správa RBAC ve službě Azure Stack](azure-stack-manage-permissions.md)

Pokud předdefinované role pro prostředky Azure nesplňují konkrétní požadavky vaší organizace, můžete si vytvořit vlastní role. V tomto kurzu pomocí Azure PowerShellu vytvoříte vlastní roli Čtenář lístků podpory.

* [Kurz: Vytvoření vlastní role pro prostředky Azure pomocí Azure PowerShellu](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell)

### <a name="manage-usage-and-billing-as-a-csp"></a>Spravovat využití a fakturace jako zprostředkovatel kryptografických služeb

* [Správa využití a fakturace jako poskytovatel CSP](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)
* [Vytvoření předplatného CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)

Zvolte typ účtu sdílených služeb, který používáte pro službu Azure Stack. K registraci služby Azure Stack pro více tenantů je možné použít následující typy předplatných:

* Poskytovatel cloudových služeb
* Předplatné Partner Shared Services


## <a name="troubleshoot-deployment"></a>Řešení potíží s nasazením 
### <a name="general-deployment-failure"></a>Obecné selhání nasazení
Pokud při instalaci dojde k chybě, můžete restartovat nasazení z neúspěšného kroku pomocí možnosti-znovu spustit ve skriptu nasazení.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení ASDK je relace PowerShellu stále otevřená a nezobrazuje žádný výstup.
Toto chování je pravděpodobně pouze výsledkem výchozího chování okna příkazového řádku prostředí PowerShell, když bylo vybráno. Nasazení vývojářské sady bylo úspěšné, ale při výběru okna byl skript pozastaven. Dokončení instalace můžete ověřit tak, že si vyhledáte slovo "vybrat" v záhlaví okna příkazového řádku. Stisknutím klávesy ESC zrušte výběr a po ní by se měla zobrazit zpráva o dokončení.

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Nasazení se nezdařilo z důvodu nedostatku externího přístupu.
Pokud se nasazení nezdaří ve fázích, kde je vyžadován externí přístup, bude vrácena výjimka jako v následujícím příkladu:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Pokud dojde k této chybě, zkontrolujte, zda jsou splněny všechny minimální požadavky na síť v dokumentaci k [provozu sítě nasazení](deployment-networking.md). Nástroj pro kontrolu sítě je k dispozici také pro partnery jako součást sady partner Toolkit.

Selhání nasazení s výše uvedenou výjimkou jsou obvykle způsobeny problémy s připojením k prostředkům na internetu.

Pokud chcete ověřit, že se jedná o váš problém, můžete provést následující kroky:

1. Otevřít PowerShell
2. Zadejte-PSSession k WAS01 nebo libovolnému virtuálnímu počítači ERCs
3. Spusťte rutiny: Test-NetConnection login.windows.net-port 443

Pokud tento příkaz neproběhne úspěšně, ověřte, zda je přepínač pro ověřování a všechna další síťová zařízení nakonfigurována tak, aby [povolovala síťový provoz](azure-stack-network.md).

## <a name="troubleshoot-virtual-machines"></a>Řešení potíží s virtuálními počítači
### <a name="default-image-and-gallery-item"></a>Výchozí položka obrázku a galerie
Před nasazením virtuálních počítačů v Azure Stack je třeba přidat položku galerie a image Windows serveru.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po restartování Azure Stack hostitele se některé virtuální počítače nemusí automaticky spustit.
Po restartování hostitele si můžete všimnout, že Azure Stack služby nejsou hned k dispozici.  Důvodem je to, že [virtuální počítače Azure Stack infrastruktury](../asdk/asdk-architecture.md#virtual-machine-roles ) a poskytovatelé prostředků nějakou dobu nějakým účelem kontrolují konzistenci, ale nakonec se spustí automaticky.

Můžete si také všimnout, že se virtuální počítače tenanta po restartování hostitele vývojové sady Azure Stack automaticky nespustí. Jedná se o známý problém a jenom pár ručních kroků, které je potřeba převést do režimu online:

1.  Na hostiteli Azure Stack Development Kit spusťte **Správce clusteru s podporou převzetí služeb při selhání** z nabídky Start.
2.  Vyberte cluster **S-cluster. azurestack. Local**.
3.  Vyberte **role**.
4.  Virtuální počítače tenanta se zobrazí v *uloženém* stavu. Jakmile budou všechny virtuální počítače infrastruktury spuštěné, klikněte pravým tlačítkem na virtuální počítače tenanta a vyberte **Spustit** , aby se obnovily.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Odstranil (a) jsem některé virtuální počítače, ale pořád na disku uvidí soubory VHD. Je toto chování očekávané?
Ano, toto chování je očekávané. Bylo navrženo tímto způsobem:

* Při odstranění virtuálního počítače se virtuální pevné disky neodstraňují. Disky jsou samostatné prostředky ve skupině prostředků.
* Když se účet úložiště odstraní, odstraní se hned po Azure Resource Manager, ale disky, které můžou obsahovat, se pořád uchovávají v úložišti, až do doby, než se spustí shromažďování paměti.

Pokud se zobrazí "osamocené" disky VHD, je důležité znát, jestli jsou součástí složky pro účet úložiště, který se odstranil. Pokud se účet úložiště neodstranil, je normální, ale pořád tam.

Další informace o konfiguraci prahové hodnoty pro uchování a opětovného získávání na vyžádání najdete v tématu [Správa účtů úložiště](azure-stack-manage-storage-accounts.md).

## <a name="troubleshoot-storage"></a>Řešení potíží s úložištěm
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin, než se kapacita uvolní, aby se na portálu zobrazovala. Recyklace místa závisí na různých faktorech, včetně procentuálního využití vnitřních souborů kontejneru v úložišti objektů blob bloku. V závislosti na tom, kolik dat je odstraněno, však není zaručeno množství místa, které by mohlo být uvolněno při spuštění systému uvolňování paměti.

