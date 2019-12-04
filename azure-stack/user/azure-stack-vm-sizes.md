---
title: Velikosti virtuálních počítačů podporované v Azure Stack | Microsoft Docs
description: Referenční informace o podporovaných velikostech virtuálních počítačů v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/3/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/3/2019
ms.openlocfilehash: d38168fee437b27dfde8d5381c960b8e2a237487
ms.sourcegitcommit: 757f612663059f1af8c535d8b37f63a8cf4a5e7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74784089"
---
# <a name="vm-sizes-supported-in-azure-stack"></a>Podporované velikosti virtuálních počítačů v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

V tomto článku jsou uvedeny velikosti virtuálních počítačů, které jsou k dispozici v Azure Stack. Tento článek vám může pomoci zajistit, aby váš výběr virtuálního počítače podporoval vaše řešení Azure Stack.

Disk IOPS (vstupně-výstupní operace za sekundu) na Azure Stack je funkce velikosti virtuálního počítače místo typu disku. To znamená, že pro virtuální počítač s Standard_Fs Series bez ohledu na to, jestli pro daný typ disku zvolíte SSD nebo HDD, je limit IOPS pro jeden další datový disk 2300 IOPS. Stanovená omezení IOPS je limit (maximální možný), aby se zabránilo sousedním sousedům. Nejedná se o záruku za IOPS, kterou získáte na konkrétní velikosti virtuálního počítače.

## <a name="vm-general-purpose"></a>Obecné účely virtuálního počítače

Velikosti virtuálních počítačů pro obecné účely představují vyvážený poměr PROCESORů k paměti. Používají se pro testování a vývoj, malé až střední databáze a webové servery s nízkým a středním provozem. Každý datový disk je 2300 vstupně-výstupních operací pro velikosti virtuálních počítačů úrovně Premium, s výjimkou řady Basic A. Pro Basic A je velikost datového disku 500 IOPS.

### <a name="basic-a"></a>Basic A

> [!NOTE]
> *Základní A* Velikosti virtuálních počítačů se při [vytváření služby Virtual Machine Scale Sets](../operator/azure-stack-compute-add-scalesets.md) (VMSS) prostřednictvím portálu neúčtují. Pokud chcete vytvořit VMSS s touto velikostí, použijte PowerShell nebo šablonu.

|Velikost – Size\Name |vCPU     |Paměť | Max. velikost dočasného disku | Maximální propustnost disku s operačním systémem: (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální propustnost datového disku (IOPS) | Maximální počet síťových karet |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0 \ Basic_A0**  |1\. místo    |768 MB   | 20 GB   |300  | 300  |1/1x300  |2   |
|**A1 \ Basic_A1**  |1\. místo    |1,75 GB  | 40 GB   |300  | 300  |2/2x300  |2   |
|**A2 \ Basic_A2**  |2    |3,5 GB   | 60 GB   |300  | 300  |4/4x300  |2   |
|**A3 \ Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8/8x300  |2   |
|**A4 \ Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16/16X300 |2   |

### <a name="standard-a"></a>Standardní A 
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1\. místo |0,768 |20  |500 |500 |1×500  |2 |
|**Standard_A1** |1\. místo |1,75  |70  |500 |500 |2×500  |2 |
|**Standard_A2** |2 |3,5   |135 |500 |500 |4×500  |2 |
|**Standard_A3** |4 |7     |285 |500 |500 |8×500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16×500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4×500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8×500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16×500 |4 |

### <a name="av2-series"></a>Av2-series
*Vyžaduje Azure Stack verze 1804 nebo novější.*

|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1\. místo   |2   |10   |500 |1 000  |2 / 2×500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4×500   |2 |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8×500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16×500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4×500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8×500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16×500 |8 |

### <a name="d-series"></a>D-series
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1\. místo   |3,5 |50   |500 |3000  |4 / 4×500   |2 |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8×500   |2 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16×500 |4 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32×500 |8 |


### <a name="ds-series"></a>Řada DS
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1\. místo   |3,5 |7    |1 000 |4000  |4/4x2300   |2 |
|**Standard_DS2** |2   |7   |14   |1 000 |8000  |8/8x2300   |2 |
|**Standard_DS3** |4   |14  |28   |1 000 |16000 |16/16x2300 |4 |
|**Standard_DS4** |8   |28  |56   |1 000 |32000 |32/32x2300 |8 |

### <a name="dv2-series"></a>Řada Dv2
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1\. místo   |3,5 |50   |500 |3000  |4 / 4×500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8×500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16×500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32×500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>Řada DSv2
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1\. místo   |3,5 |7   |1 000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1 000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1 000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1 000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1 000 |64000 |64/64x2300 |8 |


## <a name="compute-optimized"></a>Výpočtově optimalizované
### <a name="f-series"></a>Řada F
*Vyžaduje Azure Stack verze 1804 nebo novější.*

|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1\. místo   |2   |16   |500 |3000  |4 / 4×500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8×500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16×500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32×500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64/64x500 |8 |


### <a name="fs-series"></a>Řada Fs
*Vyžaduje Azure Stack verze 1804 nebo novější.*  

|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1\. místo   |2   |4   |1 000 |4000  |4/4x2300   |2 |
|**Standard_F2s**  |2   |4   |8   |1 000 |8000  |8/8x2300   |2 |
|**Standard_F4s**  |4   |8   |16  |1 000 |16000 |16/16x2300 |4 |
|**Standard_F8s**  |8   |16  |32  |1 000 |32000 |32/32x2300 |8 |
|**Standard_F16s** |16  |32  |64  |1 000 |64000 |64/64x2300 |8 |


### <a name="fsv2-series"></a>Fsv2-series
*Vyžaduje Azure Stack verze 1804 nebo novější.* 

|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1 000 |4000   |4/4x2300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1 000 |8000   |8/8x2300    |2 |
|**Standard_F8s_v2**  |8   |16  |64   |1 000 |16000  |16/16x2300  |4 |
|**Standard_F16s_v2** |16  |32  |128  |1 000 |32000  |32/32x2300  |4 |
|**Standard_F32s_v2** |32  |64  |256  |1 000 |64000  |32/32x2300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1 000 |128000 |32/32x2300  |8 |


## <a name="memory-optimized"></a>Paměťově optimalizované

Paměťově optimalizované velikosti virtuálních počítačů poskytují vysoký poměr paměti k procesoru, který je určený pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti.

### <a name="mo-d"></a>Řady D-Series
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8×500   |2 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16×500 |4 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32×500 |8 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64/64x500 |8 |

### <a name="mo-ds"></a>Řada DS
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1 000    |8000  |8/8x2300   |2 |
|**Standard_DS12**  |4  |28  |56  |1 000    |12000 |16/16x2300 |4 |
|**Standard_DS13**  |8  |56  |112 |1 000    |32000 |32/32x2300 |8 |
|**Standard_DS14**  |16 |112 |224 |1 000    |64000 |64/64x2300 |8 |

### <a name="mo-dv2"></a>Dv2-Series
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8×500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16×500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32×500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="mo-dsv2"></a>DSv2-Series
|Velikost     |vCPU     |Paměť (GiB) | Dočasné úložiště (GiB)  | Maximální propustnost disku s operačním systémem (IOPS) | Maximální propustnost dočasného úložiště (IOPS) | Maximální počet datových disků/propustnost (IOPS) | Maximální počet síťových karet |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1 000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1 000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1 000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1 000 |64000  |32/32x2300  |8 |


## <a name="next-steps"></a>Další kroky

[Funkce Azure Stack virtuálního počítače](azure-stack-vm-considerations.md)
