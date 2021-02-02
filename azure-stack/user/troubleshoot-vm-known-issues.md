---
title: Řešení známých problémů s virtuálním počítačem v centru Azure Stack
description: Naučte se řešit známé problémy s virtuálním počítačem v centru Azure Stack.
author: mattbriggs
ms.topic: troubleshooting
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 07/09/2020
ms.openlocfilehash: 45fcd2857a7da8820f1f588d864873e0e5f41467
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246584"
---
# <a name="known-issues-vms-on-azure-stack-hub"></a>Známé problémy: virtuální počítače v centru Azure Stack

Známé problémy pro řešení potíží se zprostředkovatelem prostředků služby Azure Stack Hub můžete najít při práci s virtuálními počítači (VM) s a sadami škálování v tomto článku.

## <a name="portal-doesnt-show-correct-vm-name"></a>Portál nezobrazuje správný název virtuálního počítače
- **To**  
    Tento problém se týká všech verzí.  
- **Příčina**  
    Při zobrazení podrobností o VIRTUÁLNÍm počítači v okně Přehled se zobrazí název počítače (není k dispozici). Zobrazení je záměrné pro virtuální počítače vytvořené z specializovaných disků nebo snímků disků.  
- **Náprava**  
    Na portálu vyberte **Nastavení**  >  **vlastnosti**.
- **Výskyt**  
    Společné  

## <a name="nvv4-vm-size-on-portal"></a>Velikost virtuálního počítače NVv4 na portálu
- **To**  
    Tento problém se týká centra Azure Stack verze 2002 a novější.  
- **Příčina**  
    Když projdete prostředí pro vytváření virtuálních počítačů, zobrazí se velikost virtuálního počítače: NV4as_v4. Zákazníci, kteří mají k dispozici hardware potřebný pro procesory GPU Azure Stack v systému AMD ve verzi Preview, mohou mít úspěšné nasazení virtuálního počítače. U všech ostatních zákazníků dojde k neúspěšnému nasazení virtuálního počítače pomocí této velikosti virtuálního počítače.  
- **Náprava**  
    Žádné  
- **Výskyt**  
    Společné  

## <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů
- **To**  
    Tento problém se vztahuje na všechny podporované verze.  
- **Příčina**  
    Při vytváření nového virtuálního počítače se může zobrazit následující chyba: nepovedlo se spustit virtuální počítač s názvem VM-Name. Chyba: nepovedlo se aktualizovat nastavení sériového výstupu pro virtuální počítač s názvem VM-Name. K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění.  
- **Náprava**  
    Vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.
- **Výskyt**  
    Společné  

## <a name="vm-diagnostics-storage-account-not-found"></a>Nepovedlo se najít účet úložiště diagnostiky virtuálních počítačů.
- **To**  
    Tento problém se vztahuje na všechny podporované verze.  
- **Příčina**  
    Při pokusu o spuštění nepřiděleného virtuálního počítače se může zobrazit následující chyba: účet úložiště diagnostiky virtuálních počítačů ' diagnosticstorageaccount ' nebyl nalezen. Ujistěte se, že se účet úložiště neodstranil. K této chybě dojde, pokud se pokusíte spustit virtuální počítač s povolenou diagnostikou spouštění, ale odkazovaný účet úložiště diagnostiky spouštění se odstraní.  
- **Náprava**  
    Vytvořte znovu účet úložiště se stejným názvem, který jste použili dříve.  
- **Výskyt** Obecný  

## <a name="consumed-compute-quota"></a>Spotřebovaná kvóta COMPUTE
- **To**  
    Tento problém se vztahuje na všechny podporované verze.  
- **Způsobit**   
    Při vytváření nového virtuálního počítače se může zobrazit chyba, například toto předplatné je v kapacitě celkového regionu vCPU v tomto umístění. V tomto předplatném je k dispozici všechny dostupné vCPU celkové oblasti 50. To znamená, že byla dosažena kvóta pro celkový počet jader, která jsou k dispozici.  
- **Náprava**  
    Zeptejte se operátoru na plán doplňku s dodatečnou kvótou. Změna kvóty aktuálního plánu nebude fungovat ani se neprojeví vyšší kvóta.
- **Výskyt**  
    Časté  

## <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

-  **To**  
    Tento problém se vztahuje na všechny podporované verze.  
- **Příčina**  
    Během opravy a aktualizace v prostředích centra Azure Stack se čtyřmi uzly vytvořte selhání. Vytváření virtuálních počítačů v sadě dostupnosti tří domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou FabricVmPlacementErrorUnsupportedFaultDomainSize během procesu aktualizace v prostředí centra Azure Stack se čtyřmi uzly.  
- **Náprava**  
    Jednotlivé virtuální počítače ve skupině dostupnosti se dvěma doménami selhání můžete vytvořit úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace v nasazení centra Azure Stack se čtyřmi uzly k dispozici.  
- **Výskyt**  
    Časté  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [funkcích virtuálních počítačů Azure Stack hub](azure-stack-vm-considerations.md).
