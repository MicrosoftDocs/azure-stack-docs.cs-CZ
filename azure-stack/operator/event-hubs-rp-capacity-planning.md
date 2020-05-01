---
title: Postup plánování kapacity pro Event Hubs v centru Azure Stack
description: Naučte se plánovat kapacitu pro poskytovatele prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 71bc2a58dd3420aad1672c431f3c5114364980c4
ms.sourcegitcommit: f2d80d705a222095c2ea785b9797bbac0cf96fcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82605658"
---
# <a name="how-to-do-capacity-planning-for-event-hubs-on-azure-stack-hub"></a>Postup plánování kapacity pro Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Jako operátor můžete spravovat kapacitu centra Azure Stack s využitím [kvót](azure-stack-quota-types.md) u prostředků. Event Hubs spotřebu prostředků můžete řídit nastavením kvót na maximálním počtu jader, které používají clustery Event Hubs. Event Hubs clustery jsou vytvořeny uživateli při nasazení Event Hubs prostředků. Existují také různé požadavky na spotřebu prostředků pro poskytovatele prostředků, které jsou pokryty v tomto článku.

## <a name="cluster-resource-consumption"></a>Spotřeba prostředků clusteru

Aby bylo možné pochopit spotřebu kapacity Event Hubs nasazení, je důležité si uvědomit, že uživatelé vytvářejí Event Hubs clustery na základě kapacitních jednotek (kapacitní jednotky). Při vytváření clusteru Event Hubs neurčují počet jader procesoru. Každé CU se však přímo mapuje na určitý počet spotřebovaných jader. 

Uživatelé budou muset vytvářet Event Hubs clustery s kapacitní jednotky, které splňují své obchodní požadavky. V následující tabulce jsou uvedeny informace o tom, jak vaše rozhodnutí ohledně konfigurace kvót informovat:
- Celkový počet jader používaných clusterem 1 CU Event Hubs.
- Přibližná kapacita požadovaná pro spotřebu dalších prostředků, včetně úložiště virtuálních počítačů, paměti a účtů úložiště.

| | Typ virtuálního počítače | Uzly clusteru | Jader na virtuální počítač/uzel | Celkový počet jader | Úložiště virtuálního počítače | Memory (Paměť) | Účty úložiště | Veřejné IP adresy |
|-|---------|-------|-------------------|-------------|------------|--------|------------------|---|
| **1 CU Event Hubs cluster** | [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) | 5 | 2 | 10 | 500 GiB | 70 GiB | 4 | 1 |

Všechny clustery Event Hubs používají pro své uzly typ virtuálního počítače [D11_V2](../user/azure-stack-vm-sizes.md#mo-dv2) . Typ virtuálního počítače D11_V2 se skládá ze 2 jader. Takže 1 CU Event Hubs cluster používá 5 D11_V2 virtuálních počítačů, které se vztahují k využívanému 10 jader. Při určování počtu jader, které se mají pro kvótu nakonfigurovat, použijte násobek celkového počtu jader, které používá 1 CU. Tento výpočet odráží maximální počet CU, které umožníte uživatelům používat při vytváření Event Hubs clusterů. Pokud chcete například nakonfigurovat kvótu, která umožňuje uživatelům vytvořit cluster se 2 kapacitní jednotky kapacitou, nastavte kvótu na 20 jader.

> [!NOTE]
> **Jenom veřejná verze Preview** Dostupná verze Event Hubs v centru Azure Stack podporuje pouze vytváření 1 CU clusterů. Verze Event Hubs obecné dostupnosti (GA) bude zahrnovat podporu pro různé možnosti konfigurace.

## <a name="resource-provider-resource-consumption"></a>Spotřeba prostředků poskytovatele prostředků  

Spotřeba prostředků poskytovatelem prostředků Event Hubs je konstantní a nezávisle na počtu nebo velikosti clusterů vytvořených uživateli. Následující tabulka ukazuje základní využití poskytovatele prostředků Event Hubs v centru Azure Stack a přibližnou spotřebu prostředků jinými prostředky. Poskytovatel prostředků Event Hubs používá pro své nasazení [D2_V2](/azure-stack/user/azure-stack-vm-sizes#dv2-series) typ virtuálního počítače.

|                                  | Typ virtuálního počítače | Uzly clusteru | Cores | Úložiště virtuálního počítače | Memory (Paměť) | Účty úložiště | Veřejné IP adresy |
|----------------------------------|---------|---------------|-------|------------|--------|------------------|------------|
| **Poskytovatel prostředků Event Hubs** | D2_V2   | 3     | 6     | 300 GiB | 21 GiB | 2 | 1 |

> [!IMPORTANT]
> Spotřeba poskytovatele prostředků není něco, co se řídí kvótami. V konfiguracích kvót nemusíte používat základní součásti používané poskytovatelem prostředků. Poskytovatelé prostředků se instalují pomocí předplatného správce. Při instalaci požadovaných poskytovatelů prostředků předplatné nezavádí v operátorech omezení spotřeby prostředků.

## <a name="total-resource-consumption"></a>Celková spotřeba prostředků

Celková kapacita spotřebovaná službou Event Hubs zahrnuje spotřebu prostředků poskytovatelem prostředků a spotřebu podle uživatelem vytvořených clusterů.

Následující tabulka ukazuje celkovou spotřebu Event Hubs v různých konfiguracích bez ohledu na to, jestli jsou spravovaná kvótou. Tato čísla jsou založená na poskytovateli prostředků a Event Hubsch využitích clusteru uvedených výše. V těchto příkladech můžete snadno vypočítat celkové využití centra Azure Stack pro jiné velikosti nasazení.

|                                      | Cores | Úložiště virtuálního počítače | Memory (Paměť)  | Účty úložiště | Celkové úložiště\* | Veřejné IP adresy\*\* |
|--------------------------------------|-------|------------|---------|------------------|---------------|------------|
| **1. CU poskytovatel prostředků clusteru** | 16    | 800 GiB    | 91 GiB  | 6                | proměnná    | 2 |
| **2 – CU poskytovatel prostředků clusteru** | 26    | 1,3 TB     | 161 GiB | 10               | proměnná    | 2 |
| **4 – CU poskytovatel prostředků clusteru** | 46    | 2,3 TB     | 301 GiB | 18               | proměnná    | 2 |

\*Přenosová rychlost příchozího přenosu dat (zpráva/událost) a uchovávání zpráv jsou dva důležité faktory, které přispívají k úložišti používanému Event Hubs clustery. Pokud je například uchovávání zpráv nastaveno na 7 dní při vytváření centra událostí a zprávy se ingestují rychlostí 1 MB/s, přibližné využité úložiště je 604 GB (1 MB × 60 sekund × 60 minut × 24 hodin × 7 dnů). Pokud se zprávy odesílají rychlostí 20MB/s s uchováním 7 dní, je přibližná spotřeba úložiště 12 TB pokryje. Nezapomeňte vzít v úvahu rychlost příchozího přenosu dat a dobu uchování, abyste plně pochopili spotřebu kapacity úložiště.

\*\*Veřejné IP adresy se spotřebovávají z [kvóty sítě poskytované jako součást předplatného](azure-stack-quota-types.md#network-quota-types).

## <a name="next-steps"></a>Další kroky

Před zahájením procesu instalace dokončete [požadavky pro instalaci Event Hubs v centru Azure Stack](event-hubs-rp-prerequisites.md).





