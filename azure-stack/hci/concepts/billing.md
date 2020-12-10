---
title: Fakturace a platba v Azure Stack HCI
description: Způsob, jakým funguje fakturace a platba v Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/19/2020
ms.openlocfilehash: 0cdd8a7f6c0c59eddbe1e8dd93158eb7a280e8ab
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010715"
---
# <a name="azure-stack-hci-billing-and-payment"></a>Fakturace a platba v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2

Azure Stack HCI je služba Azure, která se účtuje na vašem předplatném Azure, stejně jako jakákoli jiná služba Azure. Není nutná žádná tradiční místní softwarová licence, i když hostované virtuální počítače můžou vyžadovat licencování jednotlivých operačních systémů. Měny a slevy jsou centrálně zpracovávány fakturační platformou Azure Commerce a zákazník dostane na konci měsíce jeden jednotný a vydaný účet.

## <a name="what-does-azure-stack-hci-charge-for"></a>K čemu se účtují poplatky za Azure Stack?

Model fakturace v cloudovém stylu Azure Stack HCI je pro zákazníky, kteří už používají Azure nebo jakékoli jiné cloudové služby, konzistentní, dobře známý a snadný. Fakturace vychází z paušální sazby na jádro fyzického procesoru v Azure Stack clusteru HCI (další poplatky za využití platí, pokud používáte další služby Azure).

Cenový model se liší od fakturace založené na využívání nebo na základě spotřeby založené na počtu virtuálních počítačů. I když se počet jader virtuálních procesorů (vCPU) může v průběhu měsíce lišit, nemá to vliv na cenu, kterou platíte za Azure Stack HCI: stále platíte na základě počtu fyzických jader, které jsou v clusteru přítomné.

## <a name="advantages-of-the-azure-stack-hci-billing-model"></a>Výhody modelu fakturace Azure Stack HCI

- Je to jednoduché: není k dispozici žádná minimální, maximální a žádná matematika s pamětí, úložištěm nebo příchozím/výstupem sítě.
- Vyměňuje zákazníky, kteří mají virtualizaci virtualizace efektivněji, s vyšší hustotou mezi virtuálními a fyzickými.
- Je snadné zjistit, kolik z vašich místních Azure Stack nasazení rozhraní HCI bude mít za cenu a bude možné vypovídat z hraniční sítě až po datové centrum.

## <a name="how-the-number-of-processor-cores-is-assessed"></a>Jak se vyhodnocuje počet jader procesoru

Pokud chcete zjistit, kolik jader se v clusteru nachází, Azure Stack HCI pravidelně kontroluje počet fyzických jader a hlásí je do Azure. Pokud se jenom občas připojujete nebo jste připojení přerušili, nedělejte si starosti. může to vždycky zkusit znovu později. Dny nebo týdny základních dat lze odeslat najednou. Zákazníci se musí k Azure připojit aspoň jednou za 30 dní, aby mohli účtovat.

K ručnímu nahrání základních dat do Azure použijte **`Sync-AzureStackHCI`** rutinu.

## <a name="faq"></a>Časté otázky

- Pokud už mám předplatné Azure, můžu ho použít pro Azure Stack HCI? **Ano**
- Pokud finanční oddělení v organizaci již schválilo útratu v Azure, zahrnuje Azure Stack HCI? **Ano**
- Pokud mám k dispozici závazek Azure, můžu ho použít k Azure Stack HCI? **Ano**
- Pokud mám kredity Azure (např. pro studenty nebo jako spotřebu), můžu použít k Azure Stack HCI? **Ano**
- Pokud moje organizace sjednala smlouva Enterprise slevu, vztahuje se na Azure Stack HCI? **Ano**
- Fungují nástroje Azure Portal cost management pro Azure Stack HCI? **Ano**
- Pracují pomocí rozhraní API pro fakturaci Azure pro Azure Stack HCI vlastní nástroje nebo vlastní nástroje **Ano**

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled cen – jak funguje ceny Azure](https://azure.microsoft.com/pricing/)
- [Přehled Azure Cost Management a fakturace](/azure/cost-management-billing/cost-management-billing-overview)
