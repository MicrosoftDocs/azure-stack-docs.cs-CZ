---
title: Virtuální počítač GPU (Graphics Processing Unit) na rozbočovači Azure Stack
description: Referenční informace o výpočetním prostředí GPU v Azure Stack hub.
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: reference
ms.date: 2/8/2021
ms.reviewer: kivenkat
ms.lastreviewed: /8/2021
ms.openlocfilehash: 7091ebff9fae07b1e5eb97f54a33889bc73eda7b
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009226"
---
# <a name="graphics-processing-unit-gpu-virtual-machine-vm-on-azure-stack-hub"></a>Virtuální počítač GPU (Graphics Processing Unit) na rozbočovači Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje, které modely GPU (Graphics Processing Unit) jsou podporované v systému s více uzly Azure Stack hub. Můžete si také najít pokyny k instalaci ovladačů používaných s grafickými procesory. Podpora GPU v centru Azure Stack umožňuje řešení, jako jsou umělá inteligentní analýza, školení, odvozování a vizualizace dat. AMD Radeon Instinct MI25 se dá použít k podpoře aplikací náročných na grafiku, jako je Autodesk AutoCAD.

V období veřejné verze Preview si můžete vybrat ze tří modelů GPU. Jsou k dispozici v NVIDIA V100, NVIDIA T4 a AMD MI25 GPU. Tyto fyzické GPU se zarovnají s následujícími typy virtuálních počítačů Azure N-Series následujícím způsobem:
- [NCv3](/azure/virtual-machines/ncv3-series)
- [NVv4 (AMD MI25)](/azure/virtual-machines/nvv4-series)
- [NCasT4_v3](/azure/virtual-machines/nct4-v3-series)

::: moniker range=">=azs-2005"
> [!IMPORTANT]  
> Podpora Azure Stack centra GPU je ve verzi Public Preview pro verze 2005 a 2008 Azure Stack centra.  
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
::: moniker-end

::: moniker range="<=azs-2002"
> [!WARNING]  
> Virtuální počítače GPU nejsou v této verzi podporované. Budete muset upgradovat na Azure Stack hub 2005 nebo novější. Kromě toho musí mít hardware centra Azure Stack fyzické GPU.
::: moniker-end
## <a name="ncv3"></a>NCv3

Virtuální počítače řady NCv3-Series využívají grafické procesory NVIDIA Tesla V100. Zákazníci můžou využít těchto aktualizovaných GPU pro tradiční úlohy HPC, jako je modelování zásobníku, sekvence DNA, analýza bílkovin, simulace Monte Carlo a další. 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 8 |

## <a name="nvv4"></a>NVv4

Virtuální počítače řady NVv4-Series využívají GPU technologie [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-MI25) . Pomocí centra Azure Stack řady NVv4-Series Představujeme virtuální počítače s využitím částečných procesorů GPU. Tato velikost se dá použít pro akcelerované grafické aplikace GPU a virtuální plochy. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows. 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="ncast4_v3"></a>NCasT4_v3

::: moniker range=">=azs-2005"
> [!IMPORTANT]
> Tyto velikosti virtuálních počítačů se nedají nasadit pomocí uživatelského rozhraní portálu (UI). Můžete použít PowerShell, Azure CLI nebo šablony Azure Resource Manager.
::: moniker-end

| Velikost | Virtuální procesory | Paměť: GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | 
| --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 | 1 | 16 | 8 | 4 | 
| Standard_NC8as_T4_v3 |8 |56 | 1 | 16 | 16 | 8 | 
| Standard_NC16as_T4_v3 |16 |112 | 1 | 16 | 32 | 8 | 
| Standard_NC64as_T4_v3 |64 |448 | 4 | 64 | 32 | 8 |

## <a name="patch-and-update-fru-behavior-of-vms"></a>Oprava a aktualizace, chování funkce FRU virtuálních počítačů 

Virtuální počítače GPU budou podléhat výpadkům během operací, jako je třeba patch and Update (PnU), a také nahrazení hardwaru (FRU) centra Azure Stack. Následující tabulka přechází do stavu virtuálního počítače, jak se zjistila během těchto aktivit, a také ruční akci, kterou může uživatel udělat, aby tyto virtuální počítače znovu vyúčtovaly tyto operace. 

| Operace | PnU – expresní aktualizace | PnU – úplná aktualizace, OEM Update | JEDNOTKA | 
| --- | --- | --- | --- | 
| Stav virtuálního počítače  | Během a po aktualizaci bez operace ručního spuštění není k dispozici. | Během aktualizace není k dispozici. Dostupná aktualizace po aktualizaci s ruční operací | Během aktualizace není k dispozici. Dostupná aktualizace po aktualizaci s ruční operací| 
| Ruční operace | Pokud je potřeba virtuální počítač zpřístupnit během aktualizace, pokud jsou dostupné oddíly GPU, můžete virtuální počítač restartovat z portálu kliknutím na tlačítko **restartovat** . Restartování virtuálního počítače po aktualizaci z portálu pomocí tlačítka **restartovat** | Virtuální počítač nejde během aktualizace zpřístupnit. Po dokončení aktualizace se musí virtuální počítač zastavit a zrušit jeho přidělení pomocí tlačítka **stop** a spustit zálohování pomocí tlačítka Spustit. | Virtuální počítač nejde během aktualizace zpřístupnit. Po dokončení aktualizace se musí virtuální počítač zastavit a zrušit jeho přidělení pomocí tlačítka **zastavit** a spustit zálohování pomocí tlačítka **Start** .| 

## <a name="guest-driver-installation"></a>Instalace ovladače hosta

### <a name="amd-mi25"></a>AMD MI25

V článku [instalace ovladačů AMD GPU na virtuálních počítačích řady N-Series, na kterých běží Windows](/azure/virtual-machines/windows/n-series-amd-driver-setup) , najdete pokyny k instalaci ovladače pro AMD Radeon Instinct MI25 uvnitř virtuálního počítače NVv4 GPU-P s povolenými kroky, jak ověřit instalaci ovladače. Toto rozšíření funguje pouze v připojeném režimu.

### <a name="nvidia"></a>GRAF

Ovladače NVIDIA musí být nainstalované v rámci virtuálního počítače pro úlohy CUDA nebo GRIDu pomocí GPU.

#### <a name="use-case-graphicsvisualization"></a>Případ použití: grafika/vizualizace

Tento scénář vyžaduje použití ovladačů mřížky. Ovladače mřížky je možné stáhnout prostřednictvím centra aplikací NVIDIA, pokud máte požadované licence. Ovladače mřížky také vyžadují licenční server s MŘÍŽKou s příslušnými licencemi k mřížce před použitím ovladačů mřížky na virtuálním počítači. Tato možnost slouží k získání informací o tom, jak nastavit licenční server.

#### <a name="use-case-computecuda"></a>Případ použití: COMPUTE/CUDA

Na virtuálním počítači bude potřeba ručně nainstalovat CUDA sušičky a ovladače pro MŘÍŽKu NVIDIA. Ovladače Tesla CUDA lze získat z [webu pro stažení](https://www.nvidia.com/Download/index.aspx)NVIDIA. Ovladače CUDA nepotřebují licenční server.

## <a name="next-steps"></a>Další kroky

- [Funkce Azure Stack virtuálního počítače](azure-stack-vm-considerations.md)  
- [Nasazení modulu IoT s povoleným grafickým procesorem do centra Azure Stack](gpu-deploy-sample-module.md)
