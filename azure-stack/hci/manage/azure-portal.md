---
title: Použití Azure Portal s Azure Stack HCL
description: Jak zobrazit a spravovat clustery Azure Stack HCI pomocí Azure Portal.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/15/2020
ms.openlocfilehash: e261c54140ed62a7b2fd19588e2b3ffc3b3911b8
ms.sourcegitcommit: 0579942530284d63472fb346cf5ff55de328cea4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90560784"
---
# <a name="use-the-azure-portal-with-azure-stack-hci"></a>Použití Azure Portal s Azure Stack HCL

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu se dozvíte, jak se připojit k Azure Stack části HCI Azure Portal pro globální zobrazení Azure Stackch clusterů HCI. Pomocí Azure Portal můžete spravovat a monitorovat clustery i v případě, že je vaše fyzická infrastruktura hostovaná místně. Cloudové monitorování eliminují nutnost udržovat místní monitorovací systém a databázi, což snižuje složitost infrastruktury. Také zvyšuje škálovatelnost tím, že odesílá výstrahy a další informace přímo do Azure, které už každý den spravují miliony objektů.

## <a name="view-your-clusters-in-the-azure-portal"></a>Zobrazení clusterů v Azure Portal

Po registraci clusteru Azure Stack HCI je jeho prostředek Azure viditelný v Azure Portal. Pokud ho chcete zobrazit, nejdřív se přihlaste k [Azure Portal](https://portal.azure.com). Pokud jste už [svůj cluster zaregistrovali v Azure](../deploy/register-with-azure.md), měla by se zobrazit nová skupina prostředků s názvem vašeho clusteru připojeným pomocí "-RG". Pokud se skupina prostředků Azure Stack HCI nezobrazuje, vyhledejte "HCI" a v rozevírací nabídce vyberte svůj cluster:

:::image type="content" source="media/azure-portal/azure-portal-search.png" alt-text="Vyhledejte Azure Portal pro HCI, kde zjistíte, že Azure Stack prostředek HCI.":::

Domovská stránka služby Azure Stack HCI obsahuje seznam všech clusterů spolu s jejich skupinou prostředků, umístěním a přidruženým předplatným.

:::image type="content" source="media/azure-portal/azure-portal-home.png" alt-text="Domovská stránka Azure Stack služby HCI v Azure Portal":::

Kliknutím na Azure Stack prostředek HCI zobrazíte stránku Přehled tohoto prostředku, která zobrazuje souhrnný přehled uzlů clusteru a serveru.

:::image type="content" source="media/azure-portal/azure-portal-overview.png" alt-text="Stránka souhrnu přehledu pro Azure Stack prostředek HCI v Azure Portal":::

## <a name="view-the-activity-log"></a>Zobrazit protokol aktivit

Protokol aktivit nabízí seznam posledních operací a událostí v clusteru spolu s jejich stavem, časem, přidruženým předplatným a inicializací uživatele. Události můžete filtrovat podle předplatného, závažnosti, časového rozsahu, skupiny prostředků a prostředku.

:::image type="content" source="media/azure-portal/azure-portal-activity-log.png" alt-text="Obrazovka protokolu aktivit pro Azure Stack prostředek HCI v Azure Portal":::

## <a name="configure-access-control"></a>Konfigurace řízení přístupu

Pomocí řízení přístupu můžete kontrolovat přístup uživatelů, spravovat role a přidávat a zobrazovat přiřazení rolí a zamítnout přiřazení.

:::image type="content" source="media/azure-portal/azure-portal-iam.png" alt-text="Obrazovka řízení přístupu pro Azure Stack prostředek HCI v Azure Portal":::

## <a name="add-and-edit-tags"></a>Přidání a úprava značek

Značky jsou páry název-hodnota, které umožňují kategorizaci prostředků a zobrazení konsolidované fakturace, a to použitím stejné značky na více prostředků a skupin prostředků. V názvech značek jsou rozlišována velká a malá písmena. [Přečtěte si další informace o značkách](/azure/azure-resource-manager/management/tag-resources).

:::image type="content" source="media/azure-portal/azure-portal-tags.png" alt-text="Přidat nebo upravit značky pro Azure Stack prostředek HCI na Azure Portal":::

## <a name="compare-azure-portal-and-windows-admin-center"></a>Porovnání Azure Portal a centra pro správu Windows

Na rozdíl od centra pro správu systému Windows je Azure Portal prostředí pro Azure Stack HCI navrženo pro monitorování více clusterů v globálním měřítku. Následující tabulka vám pomůže určit, který nástroj pro správu je pro vaše potřeby nejvhodnější. Společně nabízejí jednotný návrh a jsou užitečné v doplňujících scénářích.

| Centrum pro správu systému Windows | portál Azure |
| --------------- | --------------- |
| Správa místního hardwaru a virtuálního počítače v hraniční síti, vždy k dispozici | Správa v měřítku, další funkce |
| Správa Azure Stack infrastruktury HCI | Správa dalších služeb Azure |
| Monitorování a aktualizace jednotlivých clusterů | Monitorování a aktualizace škálované |
| Ruční zřizování a správa virtuálních počítačů | Samoobslužné virtuální počítače z ARC Azure |

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)
- [Monitorování Azure Stack HCL pomocí Azure Monitor](azure-monitor.md)
