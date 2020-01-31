---
title: Typy kvót v centru Azure Stack
description: Zobrazení a úprava různých typů kvót, které jsou dostupné pro služby a prostředky v centru Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 1994f35a2865de75655c3b82d9ec1ee1618b7cee
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881486"
---
# <a name="quota-types-in-azure-stack-hub"></a>Typy kvót v centru Azure Stack


[Kvóty](service-plan-offer-subscription-overview.md#plans) definují omezení prostředků, které může předplatné uživatele zřizovat nebo využívat. Kvóta může například uživateli dovolit vytvořit až pět virtuálních počítačů (VM). Každý prostředek může mít své vlastní typy kvót.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

## <a name="compute-quota-types"></a>Typy kvót výpočetních prostředků

| **Typ** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet virtuálních počítačů | 50 | Maximální počet virtuálních počítačů, které může vytvořit předplatné v tomto umístění. |
| Maximální počet jader virtuálních počítačů | 100 | Maximální počet jader, které může vytvořit předplatné v tomto umístění (například virtuální počítač a3 má čtyři jádra). |
| Maximální počet skupin dostupnosti | 10 | Maximální počet skupin dostupnosti, které lze vytvořit v tomto umístění. |
| Maximální počet virtuálních počítačů v sadě škálování | 100 | Maximální počet sad škálování, které je možné vytvořit v tomto umístění. |
| Maximální kapacita (v GB) standardního spravovaného disku | 2 048 | Maximální kapacita standardních spravovaných disků, které lze vytvořit v tomto umístění. Tato hodnota je celková velikost přidělení všech standardních spravovaných disků a použitá velikost všech standardních snímků. |
| Maximální kapacita (v GB) spravovaného disku úrovně Premium | 2 048 | Maximální kapacita spravovaných disků úrovně Premium, které je možné vytvořit v tomto umístění. Tato hodnota je celková velikost přidělení všech spravovaných disků úrovně Premium a využité velikosti všech snímků úrovně Premium. |

> [!NOTE]
> Maximální kapacita nespravovaných disků (objekty blob stránky) je oddělená od kvóty spravovaného disku. Tuto hodnotu můžete nastavit v **maximální kapacitě (GB)** v **kvótách úložiště**.

## <a name="storage-quota-types"></a>Typy kvót úložiště

| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální kapacita (GB) |2 048 |Celková kapacita úložiště, kterou může předplatné spotřebovat v tomto umístění. Tato hodnota je celková velikost využité velikosti všech objektů BLOB (včetně nespravovaných disků) a všech přidružených snímků, tabulek a front. |
| Celkový počet účtů úložiště |20 |Maximální počet účtů úložiště, které může předplatné vytvořit v tomto umístění. |

> [!NOTE]
> Pokud se v jednom předplatném překročila **Maximální kapacita (GB)** , nemůžete v tomto předplatném vytvořit nový prostředek úložiště. Ale v rámci virtuálních počítačů můžete nepřetržitě používat nespravované disky vytvořené v tomto předplatném, což může způsobit, že se nad rámec kvóty omezí celková využitá kapacita.<br>Maximální kapacita spravovaných disků je oddělená od celkové kvóty úložiště. Tuto hodnotu můžete nastavit v **výpočetních kvótách**.

## <a name="network-quota-types"></a>Typy kvót sítě

| **Položka** | **Výchozí hodnota** | **Popis** |
| --- | --- | --- |
| Maximální počet virtuálních sítí |50 |Maximální počet virtuálních sítí, které může vytvořit předplatné v tomto umístění. |
| Maximální počet bran virtuální sítě |1\. místo |Maximální počet bran virtuální sítě (bran sítě VPN), které může vytvořit předplatné v tomto umístění. |
| Maximální počet síťových připojení |2 |Maximální počet síťových připojení (Point-to-Point nebo site-to-site), které může předplatné vytvořit v rámci všech bran virtuální sítě v tomto umístění. |
| Maximální počet veřejných IP adres |50 |Maximální počet veřejných IP adres, které může vytvořit předplatné v tomto umístění. |
| Maximální počet síťových karet |100 |Maximální počet síťových rozhraní, která může vytvořit předplatné v tomto umístění. |
| Maximální počet nástrojů pro vyrovnávání zatížení |50 |Maximální počet nástrojů pro vyrovnávání zatížení, které může vytvořit předplatné v tomto umístění. |
| Maximální počet skupin zabezpečení sítě |50 |Maximální počet skupin zabezpečení sítě, které může vytvořit předplatné v tomto umístění. |

## <a name="view-an-existing-quota"></a>Zobrazit existující kvótu

Existují dva různé způsoby, jak zobrazit existující kvótu:

### <a name="plans"></a>Plány

1. V levém navigačním podokně portálu pro správu vyberte **plány**.
2. Kliknutím na název vyberte plán, pro který chcete zobrazit podrobnosti.
3. V okně, které se otevře, vyberte **služby a kvóty**.
4. Vyberte kvótu, kterou chcete zobrazit, kliknutím na ni ve sloupci **název** .

    [![Kvóty na portálu pro správu centra Azure Stack](media/azure-stack-quota-types/quotas1sm.png "Zobrazení kvót na portálu pro správu")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>Poskytovatelé prostředků

1. Na výchozím řídicím panelu portálu pro správu Najděte dlaždici **poskytovatelé prostředků** .
2. Vyberte službu s kvótou, kterou chcete zobrazit, jako jsou **výpočetní**prostředky, **sítě**nebo **úložiště**.
3. Vyberte **kvóty**a pak vyberte kvótu, kterou chcete zobrazit.

## <a name="edit-a-quota"></a>Upravit kvótu

Existují dva různé způsoby, jak kvótu upravit:

### <a name="edit-a-plan"></a>Upravit plán

1. V levém navigačním podokně portálu pro správu vyberte **plány**.
2. Kliknutím na název vyberte plán, pro který chcete upravit kvótu.
3. V okně, které se otevře, vyberte **služby a kvóty**.
4. Vyberte kvótu, kterou chcete upravit, kliknutím na ni ve sloupci **název** .

    [![Kvóty na portálu pro správu centra Azure Stack](media/azure-stack-quota-types/quotas1sm.png "Zobrazení kvót na portálu pro správu")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. V okně, které se otevře, vyberte **Upravit v části výpočty**, **Upravit v síti**nebo **Upravit v úložišti**.

    ![Úprava plánu na portálu pro správu centra Azure Stack](media/azure-stack-quota-types/quotas3.png "Úprava plánu na portálu pro správu centra Azure Stack")

Alternativně můžete pomocí tohoto postupu upravit kvótu:

1. Na výchozím řídicím panelu portálu pro správu Najděte dlaždici **poskytovatelé prostředků** .
2. Vyberte službu s kvótou, kterou chcete upravit, jako jsou **výpočetní**prostředky, **sítě**nebo **úložiště**.
3. V dalším kroku vyberte **kvóty**a pak vyberte kvótu, kterou chcete změnit.
4. V podokně **nastavit kvóty úložiště**, **Nastavte kvóty výpočtů**nebo **nastavte nastavení kvót sítě** (v závislosti na typu kvóty, kterou jste zvolili k úpravám), upravte hodnoty a pak vyberte **Save (Uložit**).

### <a name="edit-original-configuration"></a>Upravit původní konfiguraci
  
Místo [použití plánu doplňku](create-add-on-plan.md)můžete zvolit úpravu původní konfigurace kvóty. Když upravujete kvótu, nová konfigurace se automaticky použije globálně na všechny plány, které tuto kvótu používají, a všechna stávající předplatná, která tyto plány používají. Úprava kvóty se liší od použití plánu doplňku k poskytnutí upravené kvóty, kterou uživatel zvolí k přihlášení k odběru.

Nové hodnoty pro kvótu platí globálně pro všechny plány, které používají upravenou kvótu, a pro všechna existující předplatná, která tyto plány používají.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o službách, plánech, nabídkách a kvótách.](service-plan-offer-subscription-overview.md)
- [Vytváření kvót při vytváření plánu](azure-stack-create-plan.md)
