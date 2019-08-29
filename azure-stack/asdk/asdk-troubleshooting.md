---
title: Řešení potíží s ASDK | Microsoft Docs
description: Naučte se řešit potíže s Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 7946b8339c9ff1127c0a9d9572c49527208b38f2
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118662"
---
# <a name="troubleshoot-the-asdk"></a>Řešení potíží s ASDK
Tento článek poskytuje běžné informace pro řešení potíží s Azure Stack Development Kit (ASDK). Pokud jste narazili na problém, který není dokumentován, zkontrolujte, zda je na [webu MSDN fórum Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) , kde najdete pomoc.  

> [!IMPORTANT]
> Vzhledem k tomu, že ASDK je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím služeb Microsoft Customer Support Services (CSS).

Doporučení pro řešení problémů popsaných v této části jsou odvozena z několika zdrojů a mohou nebo nemusí vyřešit váš konkrétní problém. Příklady kódu jsou poskytovány "tak, jak jsou" a očekávané výsledky nelze zaručit. Tato část podléhá častým úpravám a aktualizacím, protože jsou implementována vylepšení produktu.

## <a name="deployment"></a>Nasazení
### <a name="deployment-failure"></a>Selhání nasazení
Pokud při instalaci dojde k chybě, můžete restartovat nasazení z neúspěšného kroku pomocí možnosti-znovu spustit ve skriptu nasazení. Příklad:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení je relace PowerShellu stále otevřená a nezobrazuje žádný výstup.
Toto chování je pravděpodobně pouze výsledkem výchozího chování příkazového okna prostředí PowerShell, když je vybráno. Nasazení ASDK bylo úspěšné, ale při vybírání okna byl skript pozastaven. Dokončení instalace můžete ověřit tak, že si vyhledáte slovo "vybrat" v záhlaví okna příkazového řádku. Stisknutím klávesy ESC zrušte výběr a po ní by se měla zobrazit zpráva o dokončení.

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="default-image-and-gallery-item"></a>Výchozí položka obrázku a galerie
Před nasazením virtuálních počítačů v Azure Stack je třeba přidat položku galerie a image Windows serveru.

### <a name="after-restarting-my-azure-stack-host-some-vms-dont-automatically-start"></a>Po restartování Azure Stack hostitele se některé virtuální počítače automaticky nespustí.
Po restartování hostitele si můžete všimnout, že Azure Stack Services nejsou hned k dispozici. Důvodem je to, že [virtuální počítače Azure Stack infrastruktury](asdk-architecture.md#virtual-machine-roles) a RPS nějakou dobu nekontrolují konzistenci, ale nakonec se spustí automaticky.

Můžete si také všimnout, že se virtuální počítače tenanta po restartování hostitele ASDK automaticky nespustí. Jedná se o známý problém a jenom pár ručních kroků, které je potřeba převést do režimu online:

1.  Na hostiteli ASDK spusťte **Správce clusteru s podporou převzetí služeb při selhání** v nabídce Start.
2.  Vyberte cluster **S-cluster. azurestack. Local**.
3.  Vyberte **role**.
4.  Virtuální počítače tenanta se zobrazí v uloženém stavu. Jakmile budou všechny virtuální počítače infrastruktury spuštěné, klikněte pravým tlačítkem na virtuální počítače tenanta a výběrem **Spustit spusťte** obnovení virtuálního počítače.

### <a name="ive-deleted-some-vms-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Odstranili jsme některé virtuální počítače, ale pořád se na disku zobrazují soubory VHD. Je toto chování očekávané?
Ano, toto chování je očekávané. To je navrženo tímto způsobem:

* Při odstranění virtuálního počítače se virtuální pevné disky neodstraňují. Disky jsou samostatné prostředky ve skupině prostředků.
* Když se účet úložiště odstraní, odstraní se hned po Azure Resource Manager, ale disky, které můžou obsahovat, se pořád uchovávají v úložišti, až do doby, než se spustí shromažďování paměti.

Pokud se zobrazí "osamocené" disky VHD, je důležité znát, jestli jsou součástí složky pro účet úložiště, který se odstranil. Pokud se účet úložiště neodstranil, je normální, že virtuální pevné disky zůstanou.

Další informace o konfiguraci prahové hodnoty pro uchování a opětovného získávání na vyžádání najdete v tématu [Správa účtů úložiště](../operator/azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Storage
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin, než se kapacita uvolní, aby se na portálu zobrazovala. Recyklace místa závisí na různých faktorech, včetně procentuálního využití vnitřních souborů kontejneru v úložišti objektů blob bloku. V závislosti na tom, kolik dat je odstraněno, však není nijak zaručeno množství místa, které by mohlo být uvolněno při spuštění systému uvolňování paměti.

## <a name="next-steps"></a>Další postup
[Navštívit fórum podpory Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)
