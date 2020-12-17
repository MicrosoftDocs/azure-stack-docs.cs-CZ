---
title: Nainstalovat aktualizace centra Azure Stack
description: Naučte se instalovat aktualizace centra Azure Stack.
author: sethmanheim
ms.topic: how-to
ms.date: 12/16/2020
ms.author: sethm
ms.lastreviewed: 09/10/2019
ms.reviewer: sranthar
ms.openlocfilehash: a54e47ff5788ca100e45be103e4d3d5192766f3b
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620752"
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

2. Vyberte **řídicí panel**. V podokně **aktualizace** je uvedena aktuální verze:

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-apply-updates/dashboard.png)

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a monitorování průběhu

> [!IMPORTANT]
> Před použitím aktualizací v centru Azure Stack se ujistěte, že jste dokončili všechny kroky v [kontrolním seznamu před aktualizací](release-notes-checklist.md) a naplánovali jste příslušné okno údržby pro typ aktualizace, který použijete.

1. Otevřete portál Azure Stack centrum pro správu.

2. Vyberte **řídicí panel**. Vyberte **Aktualizovat**.

3. Vyberte dostupnou aktualizaci, kterou chcete nainstalovat. Pokud nemáte aktualizaci označenou jako **k dispozici**, [Připravte balíček aktualizace](azure-stack-update-prepare-package.md).

4. Vyberte **aktualizovat hned**.

    ![Snímek obrazovky, který ukazuje, jak spustit aktualizaci v centru Azure Stack.](./media/azure-stack-apply-updates/image2.png)

5. Stav vysoké úrovně můžete zobrazit v případě, že proces aktualizace prochází z různých subsystémů v centru Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují portál pro správu i uživatele. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a počet probíhajících.

6. Pro stažení úplných protokolů vyberte **Souhrn stažení** v okně Podrobnosti o spuštění aktualizace.

    Pokud dojde k potížím při monitorování aktualizace, můžete použít [privilegovaný koncový bod](./azure-stack-privileged-endpoint.md) ke sledování průběhu Azure Stackho spuštění aktualizace centra. Pomocí privilegovaného koncového bodu můžete také pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, pokud portál centra Azure Stack nezůstane k dispozici. Pokyny najdete v tématu [monitorování aktualizací v centru Azure Stack pomocí PowerShellu](azure-stack-update-monitor.md).

    ![Podrobnosti o spuštění aktualizace centra Azure Stack](./media/azure-stack-apply-updates/image3.png)

7. Po dokončení bude poskytovatel prostředků aktualizace zobrazovat potvrzení **úspěšného** dokončení procesu aktualizace a dobu trvání. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Azure-Stack-Update-Apply](./media/azure-stack-apply-updates/image4.png)

    Pokud se aktualizace nezdařila, sestavy okna **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů Azure Stack centra pomáhá s diagnostikou a řešením potíží.

## <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte **Aktualizovat**.

3. Vyberte **historii aktualizací**.

    ![Historie aktualizace centra Azure Stack](./media/azure-stack-apply-updates/image5.png)

## <a name="next-steps"></a>Další kroky

- [Přehled správy aktualizací ve službě Azure Stack Hub](./azure-stack-updates.md)  
- [Zásady obsluhy centra Azure Stack](./azure-stack-servicing-policy.md)  
