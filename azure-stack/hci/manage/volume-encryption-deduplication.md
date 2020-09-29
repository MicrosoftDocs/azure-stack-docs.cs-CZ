---
title: Povolení šifrování svazku, odstranění duplicit a komprese Azure Stack HCI
description: V tomto tématu se dozvíte, jak používat šifrování svazku, odstranění duplicit a kompresi v Azure Stack HCI pomocí centra pro správu systému Windows.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: 9599a4d24d93e545c964de91ad10b905c79b42a1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573492"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>Povolení šifrování svazku, odstranění duplicit a komprimace v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Toto téma popisuje, jak povolit šifrování pomocí nástroje BitLocker na svazcích v Azure Stack HCI pomocí centra pro správu systému Windows. Také se zabývá tím, jak na svazcích povolit odstraňování duplicitních dat a kompresi. Informace o tom, jak vytvářet svazky, najdete v tématu věnovaném [vytváření svazků](create-volumes.md).

## <a name="turn-on-bitlocker-to-protect-volumes"></a>Zapnutí nástroje BitLocker pro ochranu svazků
Zapnutí nástroje BitLocker v centru pro správu systému Windows:

1. Připojte se ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky**.
1. Na stránce **svazky** vyberte kartu **inventář** a potom v části **volitelné funkce**přepněte na přepínač **šifrování (BitLocker)** .

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="Přepnutím přepínače povolíte nástroj BitLocker.":::

1. V automaticky otevíraném okně **šifrování (BitLocker)** vyberte **Start**a pak na stránce **zapnout šifrování** zadejte svoje přihlašovací údaje k dokončení pracovního postupu.

>[!NOTE]
   > Pokud se zobrazí první automaticky otevíraná okna **funkce nainstalovat BitLocker** , postupujte podle pokynů pro instalaci funkce na každý server v clusteru a pak restartujte servery.

## <a name="turn-on-deduplication-and-compression"></a>Zapnutí odstraňování duplicit a komprimace
Odstraňování duplicitních dat a komprese se spravují pro jednotlivé svazky. Odstranění duplicitních dat a komprimace používá model následného zpracování, což znamená, že nebudete moct sledovat úspory, dokud je nespustíte. V takovém případě bude fungovat pro všechny soubory, dokonce i soubory, které existovaly před.

Zapnutí odstraňování duplicit a komprimace na svazku v centru pro správu Windows:

1. Připojte se ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky**.
1. Na stránce **svazky** vyberte kartu **inventář** .
1. V seznamu svazků vyberte název svazku, který chcete spravovat.
1. Na stránce Podrobnosti o svazku přepněte na přepínač **odstranění duplicit a komprese** .
1. V podokně **Povolit odstraňování duplicitních dat** vyberte režim odstranění duplicitních dat.

    Místo složitých nastavení vám centrum pro správu systému Windows umožní vybrat si předem připravené profily pro různé úlohy. Pokud si nejste jistí, použijte výchozí nastavení.

1. Vyberte **Povolit odstraňování duplicitních dat**.

Podívejte se na rychlé video o zapnutí odstraňování duplicit a komprimace. Video nezobrazuje šifrování.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

Povolení šifrování svazku má malý vliv na výkon svazku – obvykle v rámci 10%, ale dopad se liší v závislosti na hardwaru a úlohách. Odstranění duplicitních dat a komprese má vliv na výkon – podrobnosti najdete v tématu [určení úloh, které jsou kandidáty na odstranění duplicitních dat](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads).

<!---Add info on greyed out ReFS option? --->

A je to hotové! Pokud potřebujete chránit data ve svazcích, opakujte postup.

## <a name="next-steps"></a>Další kroky
Související témata a další úlohy správy úložišť najdete zde:

- [Přehled Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Plánování svazků](../concepts/plan-volumes.md)
- [Rozšiřování svazků](extend-volumes.md)
- [Odstranit svazky](delete-volumes.md)