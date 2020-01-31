---
title: Nainstalovat aktualizace centra Azure Stack
description: Naučte se instalovat aktualizace centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: b854a7334055dcee4a753143f4a601f1eb8281f8
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877780"
---
# <a name="install-azure-stack-hub-updates"></a>Nainstalovat aktualizace centra Azure Stack

Balíčky aktualizací můžete nainstalovat pomocí okna **aktualizace** v centru Azure Stack. Tento článek vás provede jednotlivými kroky aktualizace, monitorování a řešení potíží s procesem aktualizace. Pomocí okna aktualizace si můžete zobrazit informace o aktualizaci, nainstalovat aktualizace, sledovat průběh aktualizace, zkontrolovat historii aktualizací a zobrazit aktuální Azure Stack centra a verzi balíčku OEM.

Aktualizace můžete spravovat z portálu pro správu a pomocí části **aktualizace** na řídicím panelu:

- Zobrazit důležité informace, jako je aktuální verze
- Nainstalujte aktualizace a sledujte průběh.
- Zkontrolujte historii aktualizací pro dříve nainstalované aktualizace.
- Zobrazit aktuální verzi balíčku OEM v cloudu

## <a name="determine-the-current-version"></a>Určení aktuální verze

Aktuální verzi centra Azure Stack můžete zobrazit v okně **aktualizace** . Pro otevření:

1.  Otevřete portál Azure Stack centrum pro správu.

2.  Vyberte **řídicí panel**. V okně **aktualizace** je uvedena aktuální verze.

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-update-apply/image1.png)

    Například v tomto obrázku je verze 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a sledování průběhu

> [!Important]
> Před použitím aktualizací v centru Azure Stack se ujistěte, že jste dokončili **všechny** kroky v [kontrolním seznamu před aktualizací](release-notes-checklist.md) a naplánovali jste příslušné okno údržby pro typ aktualizace, který použijete.

1. Otevřete portál Azure Stack centrum pro správu.

2. Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3. Vyberte dostupnou aktualizaci, kterou chcete nainstalovat. Pokud nemáte aktualizaci označenou jako **k dispozici**, musíte [připravit balíček aktualizace](azure-stack-update-prepare-package.md) .

4. Vyberte **aktualizovat hned**.

    ![Podrobnosti o spuštění aktualizace centra Azure Stack](./media/azure-stack-update-apply/image2.png)

5. Stav vysoké úrovně můžete zobrazit v případě, že proces aktualizace prochází z různých subsystémů v centru Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují portál pro správu i uživatele. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a počet probíhajících.

6. Pro stažení úplných protokolů vyberte **Souhrn stažení** v okně Podrobnosti o spuštění aktualizace.

    Pokud narazíte na problém při monitorování aktualizace, můžete pomocí [privilegovaného koncového bodu](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) monitorovat průběh spuštění aktualizace centra Azure Stack. Pomocí privilegovaného koncového bodu můžete také pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, pokud portál centra Azure Stack nezůstane k dispozici. Pokyny najdete v tématu [monitorování aktualizací v centru Azure Stack pomocí PowerShellu](azure-stack-update-monitor.md).

    ![Podrobnosti o spuštění aktualizace centra Azure Stack](./media/azure-stack-update-apply/image3.png)

7. Po dokončení dokončí poskytovatel prostředků aktualizace **úspěšné** potvrzení, které ukáže, že proces aktualizace skončil a jak dlouho trvalo. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Azure-Stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Pokud se aktualizace nezdařila, sestavy okna **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů centra Azure Stack pomáhá s diagnostikou a řešením potíží.

## <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3. Vyberte **historii aktualizací**.

    ![Historie aktualizace centra Azure Stack](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Další kroky

-   [Přehled správy aktualizací v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Zásady obsluhy centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
