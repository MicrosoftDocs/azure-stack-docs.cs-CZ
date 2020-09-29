---
title: Nahradit jednotky, které selhaly při Azure Stack HCI
description: Postup nahrazení neúspěšných jednotek v Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: d3d03f1e5cc89186e2eb8198b52e96bffbd21768
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866479"
---
# <a name="replace-failed-drives-on-azure-stack-hci"></a>Nahradit jednotky, které selhaly při Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Azure Stack HCI funguje s přímými připojenými jednotkami SATA, SAS, NVMe nebo trvalé paměti, které jsou fyzicky připojené pouze k jednomu serveru. Pokud dojde k chybě jednotky, budete potřebovat přístup k hardwaru fyzického serveru, abyste ho mohli nahradit.

## <a name="find-the-alert"></a>Najít upozornění
Když jednotka dojde k chybě, zobrazí se výstraha v levé horní oblasti **výstrahy** na řídicím panelu **centra pro správu systému Windows** . Můžete také vybrat **jednotky** z navigace na levé straně nebo kliknout na odkaz **Zobrazit jednotky >** v dlaždici v pravém dolním rohu, abyste mohli procházet jednotky a zobrazit jejich stav pro sebe. Na kartě **zobrazení** mřížka podporuje řazení, seskupování a hledání klíčových slov.

1. Z řídicího panelu kliknutím na výstrahu zobrazíte podrobnosti, jako je fyzické umístění jednotky.
1. Další informace získáte kliknutím na zástupce **Přejít na jednotku** na stránce s podrobnostmi o **jednotce** .
1. Pokud ho váš hardware podporuje, můžete kliknout na **zapnout světlo nebo vypnout** a řídit indikátor světla jednotky.
   Prostory úložiště s přímým přístupem automaticky vymezit a evakuace neúspěšných jednotek. Pokud k tomu dojde, je stav jednotky vyřazen a jeho panel kapacity úložiště je prázdný.
1. Odeberte jednotku, která selhala, a vložte její náhradu.

## <a name="wait-for-the-alert-to-clear"></a>Počkejte, než se výstraha vymaže.
V části **jednotky > inventáře**se zobrazí nová jednotka. Výstraha bude v čase nejasná, svazky se opraví zpátky na stav OK a úložiště se znovu vyrovnává na novou jednotku – není třeba žádné akce uživatele.

## <a name="next-steps"></a>Další kroky
- Další informace o tom, jak se stav úložiště sleduje na různých úrovních, včetně na úrovni jednotky, najdete v tématu [stav a provozní stavy](/windows-server/storage/storage-spaces/storage-spaces-states).
- Pokud používáte PMem, [pochopení a nasazení trvalé paměti](/windows-server/storage/storage-spaces/deploy-pmem)