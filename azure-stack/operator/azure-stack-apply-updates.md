---
title: Nainstalovat aktualizace centra Azure Stack
description: Naučte se instalovat aktualizace centra Azure Stack.
author: sethmanheim
ms.topic: how-to
ms.date: 07/22/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: d0222de6db809fcbb73d31ac1ef4298e18895643
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106569"
---
# <a name="install-azure-stack-hub-updates"></a>Nainstalovat aktualizace centra Azure Stack

Balíčky aktualizací můžete nainstalovat pomocí okna **aktualizace** na portálu správce centra Azure Stack. Tento článek popisuje kroky pro aktualizaci, monitorování a řešení potíží s procesem aktualizace. Pomocí okna **aktualizace** si můžete zobrazit informace o aktualizaci, nainstalovat aktualizace, sledovat průběh aktualizace, zkontrolovat historii aktualizací a zobrazit aktuální Azure Stack centra a verzi balíčku OEM.

Aktualizace můžete spravovat z portálu pro správu a pomocí části **aktualizace** na řídicím panelu:

- Zobrazit důležité informace, jako je aktuální verze
- Nainstalujte aktualizace a sledujte průběh.
- Zkontrolujte historii aktualizací pro dříve nainstalované aktualizace.
- Zobrazit aktuální verzi balíčku OEM v cloudu

## <a name="determine-the-current-version"></a>Určení aktuální verze

Aktuální verzi centra Azure Stack můžete zobrazit v okně **aktualizace** . Pro otevření:

1. Otevřete portál Azure Stack centrum pro správu.

2. Vyberte **řídicí panel**. V okně **aktualizace** je uvedena aktuální verze. Například v tomto obrázku je verze 1.1903.0.35:

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-update-apply/image1.png)

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a monitorování průběhu

> [!IMPORTANT]
> Před použitím aktualizací v centru Azure Stack se ujistěte, že jste dokončili všechny kroky v [kontrolním seznamu před aktualizací](release-notes-checklist.md) a naplánovali jste příslušné okno údržby pro typ aktualizace, který použijete.

1. Otevřete portál Azure Stack centrum pro správu.

2. Vyberte **řídicí panel**. Vyberte **Aktualizovat**.

3. Vyberte dostupnou aktualizaci, kterou chcete nainstalovat. Pokud nemáte aktualizaci označenou jako **k dispozici**, [Připravte balíček aktualizace](azure-stack-update-prepare-package.md).

4. Vyberte **aktualizovat hned**.

    ![Snímek obrazovky, který ukazuje, jak spustit aktualizaci v centru Azure Stack.](./media/azure-stack-update-apply/image2.png)

5. Stav vysoké úrovně můžete zobrazit v případě, že proces aktualizace prochází z různých subsystémů v centru Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují portál pro správu i uživatele. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a počet probíhajících.

6. Pro stažení úplných protokolů vyberte **Souhrn stažení** v okně Podrobnosti o spuštění aktualizace.

    Pokud dojde k potížím při monitorování aktualizace, můžete použít [privilegovaný koncový bod](./azure-stack-privileged-endpoint.md) ke sledování průběhu Azure Stackho spuštění aktualizace centra. Pomocí privilegovaného koncového bodu můžete také pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, pokud portál centra Azure Stack nezůstane k dispozici. Pokyny najdete v tématu [monitorování aktualizací v centru Azure Stack pomocí PowerShellu](azure-stack-update-monitor.md).

    ![Podrobnosti o spuštění aktualizace centra Azure Stack](./media/azure-stack-update-apply/image3.png)

7. Po dokončení bude poskytovatel prostředků aktualizace zobrazovat potvrzení **úspěšného** dokončení procesu aktualizace a dobu trvání. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Azure-Stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Pokud se aktualizace nezdařila, sestavy okna **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů Azure Stack centra pomáhá s diagnostikou a řešením potíží.

## <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte **Aktualizovat**.

3. Vyberte **historii aktualizací**.

    ![Historie aktualizace centra Azure Stack](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Další kroky

- [Přehled správy aktualizací ve službě Azure Stack Hub](./azure-stack-updates.md)  
- [Zásady obsluhy centra Azure Stack](./azure-stack-servicing-policy.md)  
