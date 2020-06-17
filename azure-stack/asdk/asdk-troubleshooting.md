---
title: Řešení potíží s ASDK
description: Naučte se řešit potíže s Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 11/05/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: efc23ad5694ba7828c2dc5c5ba5de09be3591e04
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819286"
---
# <a name="troubleshoot-the-asdk"></a>Řešení potíží s ASDK
Tento článek poskytuje běžné informace pro řešení potíží s Azure Stack Development Kit (ASDK). Nápovědu k Azure Stack integrovaným systémům najdete v tématu [řešení potíží s Microsoft Azure Stack](../operator/azure-stack-troubleshooting.md). 

Vzhledem k tomu, že ASDK je zkušební prostředí, podpora Microsoftu neposkytuje podporu. Pokud jste narazili na problém, který není dokumentován, můžete získat pomoc od expertů na [Azure Stack Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 


## <a name="deployment"></a>Nasazení
### <a name="deployment-failure"></a>Selhání nasazení
Pokud při instalaci dojde k chybě, můžete restartovat nasazení z neúspěšného kroku pomocí možnosti-znovu spustit ve skriptu nasazení. Například:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení je relace PowerShellu stále otevřená a nezobrazuje žádný výstup.
Toto chování je pravděpodobně pouze výsledkem výchozího chování příkazového okna prostředí PowerShell, když je vybráno. Nasazení ASDK bylo úspěšné, ale při vybírání okna byl skript pozastaven. Dokončení instalace můžete ověřit tak, že si vyhledáte slovo "vybrat" v záhlaví okna příkazového řádku. Stisknutím klávesy ESC zrušte výběr a po ní by se měla zobrazit zpráva o dokončení.

### <a name="template-validation-error-parameter-osprofile-is-not-allowed"></a>Parametr chyby ověřování šablony osProfile není povolený.

Pokud se během ověřování šablony zobrazí chybová zpráva, že parametr osProfile není povolen, ujistěte se, že používáte správné verze rozhraní API pro tyto komponenty:

- [Compute](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftcompute)
- [Síť](https://docs.microsoft.com/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions#microsoftnetwork)

Pokud chcete zkopírovat VHD z Azure do Azure Stack, použijte [AzCopy 7.3.0](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#download-and-install-azcopy). Spolupracujte se svým dodavatelem, abyste mohli vyřešit problémy s samotným obrázkem. Další informace o požadavcích na WALinuxAgent pro Azure Stack najdete v tématu [Agent Azure Linux](../operator/azure-stack-linux.md#azure-linux-agent).

### <a name="deployment-fails-due-to-lack-of-external-access"></a>Nasazení se nezdařilo z důvodu nedostatku externího přístupu.
Pokud se nasazení nezdaří ve fázích, kde je vyžadován externí přístup, bude vrácena výjimka jako v následujícím příkladu:

```
An error occurred while trying to test identity provider endpoints: System.Net.WebException: The operation has timed out.
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.GetResponse(WebRequest request)
   at Microsoft.PowerShell.Commands.WebRequestPSCmdlet.ProcessRecord()at, <No file>: line 48 - 8/12/2018 2:40:08 AM
```
Pokud k této chybě dojde, zajistěte, aby byly splněny všechny minimální požadavky na síť, a to kontrolou [dokumentace k provozu sítě nasazení](../operator/deployment-networking.md). Nástroj pro kontrolu sítě je k dispozici také pro partnery jako součást sady partner Toolkit.

K ostatním selháním nasazení obvykle dochází v důsledku potíží s připojením k prostředkům na internetu.

Pokud chcete ověřit připojení k prostředkům na internetu, můžete provést následující kroky:

1. Otevřete PowerShell.
2. Zadejte-PSSession k WAS01 nebo libovolnému virtuálnímu počítači ERCs.
3. Spusťte následující rutinu: 
   ```powershell
   Test-NetConnection login.windows.net -port 443
   ```

Pokud tento příkaz neproběhne úspěšně, ověřte, zda je přepínač pro ověřování a všechna další síťová zařízení nakonfigurována tak, aby [povolovala síťový provoz](../operator/azure-stack-network.md).


## <a name="virtual-machines"></a>Virtuální počítače
### <a name="default-image-and-gallery-item"></a>Výchozí položka obrázku a galerie
Před nasazením virtuálních počítačů v Azure Stack je třeba přidat položku galerie a image Windows serveru.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>Po restartování Azure Stack hostitele se některé virtuální počítače automaticky nespustí.
Po restartování hostitele si můžete všimnout, že Azure Stack Services nejsou hned k dispozici. Důvodem je to, že [virtuální počítače Azure Stack infrastruktury](asdk-architecture.md#virtual-machine-roles) a RPS nějakou dobu nekontrolují konzistenci, ale nakonec se spustí automaticky.

Můžete si také všimnout, že se virtuální počítače tenanta po restartování hostitele ASDK automaticky nespustí. Můžete je převést do online režimu pomocí několika ručních kroků:

1.  Na hostiteli ASDK spusťte **Správce clusteru s podporou převzetí služeb při selhání** v nabídce Start.
2.  Vyberte cluster **S-cluster. azurestack. Local**.
3.  Vyberte **role**.
4.  Virtuální počítače tenanta se zobrazí v *uloženém* stavu. Jakmile budou všechny virtuální počítače infrastruktury spuštěné, klikněte pravým tlačítkem na virtuální počítače tenanta a výběrem **Spustit spusťte** obnovení virtuálního počítače.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk"></a>Odstranili jsme některé virtuální počítače, ale pořád se na disku zobrazují soubory VHD 
Toto chování je záměrné:

* Při odstranění virtuálního počítače se virtuální pevné disky neodstraňují. Disky jsou samostatné prostředky ve skupině prostředků.
* Když se účet úložiště odstraní, odstraní se hned po Azure Resource Manager, ale disky, které můžou obsahovat, se pořád uchovávají v úložišti, až do doby, než se spustí shromažďování paměti.

Pokud se zobrazí "osamocené" disky VHD, je důležité znát, jestli jsou součástí složky pro účet úložiště, který se odstranil. Pokud se účet úložiště neodstranil, je normální, že virtuální pevné disky zůstanou.

Další informace o konfiguraci prahové hodnoty pro uchování a opětovného získávání na vyžádání najdete v tématu [Správa účtů úložiště](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin, než se kapacita uvolní, aby se na portálu zobrazovala. Recyklace místa závisí na různých faktorech, včetně procentuálního využití vnitřních souborů kontejneru v úložišti objektů blob bloku. V závislosti na tom, kolik dat je odstraněno, však není nijak zaručeno množství místa, které by mohlo být uvolněno při spuštění systému uvolňování paměti.

## <a name="next-steps"></a>Další kroky
[Navštívit fórum podpory Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
