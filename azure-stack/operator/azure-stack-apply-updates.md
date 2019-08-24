---
title: Použití aktualizace výrobce OEM (Original Equipment Manufacturer) na Azure Stack | Microsoft Docs
description: Naučte se, jak použít aktualizaci OEM (Original Equipment Manufacturer) na Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 22e6945c5064a93a8dbaf6daa5c39df36cccb597
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008798"
---
# <a name="apply-updates-in-azure-stack"></a>Použít aktualizace v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Aktualizaci můžete použít v okně **aktualizace** v Azure Stack. Tento článek vás provede jednotlivými kroky aktualizace, monitorování a řešení potíží s procesem aktualizace. Pomocí okna aktualizace můžete zobrazit informace o aktualizaci, nainstalovat aktualizace, sledovat průběh aktualizace, zkontrolovat historii aktualizací a zobrazit aktuální verzi balíčku OEM.

Aktualizace můžete spravovat z portálu pro správu. **Aktualizace** na řídicím panelu můžete použít k těmto akcím:

-   Zobrazení důležitých informací, jako je například aktuální verze.
-   Nainstalujte aktualizace a sledujte průběh.
-   Zkontrolujte historii aktualizací pro dříve nainstalované aktualizace.
-   Zobrazit aktuální verzi balíčku OEM v cloudu

## <a name="determine-the-current-version"></a>Určení aktuální verze

Aktuální verzi Azure Stack můžete zobrazit v okně **aktualizace** . Pro otevření:

1.  Otevřete portál Azure Stack správce.

2.  Vyberte **řídicí panel**. V okně **aktualizace** je uvedena aktuální verze.

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-update-apply/image1.png)

    Například v tomto obrázku je verze 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a sledování průběhu

1.  Otevřete portál Azure Stack správce.

2.  Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3.  Vyberte dostupnou aktualizaci, kterou chcete použít. Pokud nemáte aktualizaci označenou jako **k dispozici**, budete muset [připravit balíček aktualizace](azure-stack-update-prepare-package.md) .

4.  Vyberte **aktualizovat hned**. .

    ![Podrobnosti o spuštění aktualizace Azure Stack](./media/azure-stack-update-apply/image2.png)

5.  Stav vysoké úrovně můžete zobrazit, protože proces aktualizace prochází různými subsystémy v Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují správce i uživatelské portály. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a počet probíhajících.

6.  Pokud si chcete stáhnout úplné protokoly, vyberte **Stáhnout úplné protokoly** z okna podrobností o spuštění aktualizace.

    Pokud narazíte na problém při monitorování aktualizace, můžete pomocí [privilegovaného koncového bodu](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) monitorovat průběh spuštění aktualizace Azure Stack a pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, aby byl portál Azure Stack nedostupný. Pokyny najdete v tématu[monitorování aktualizací v Azure Stack pomocí prostředí PowerShell](azure-stack-update-monitor.md).

    ![Podrobnosti o spuštění aktualizace Azure Stack](./media/azure-stack-update-apply/image3.png)

7.  Po dokončení dokončí poskytovatel prostředků aktualizace po **úspěšném** potvrzení, že se proces aktualizace dokončil a jak dlouho trvalá. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Azure-Stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Pokud se aktualizace nezdařila, sestavy okna **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů Azure Stack pomáhá s diagnostikou a řešením potíží.

## <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1.  Otevřete portál pro správu.

2.  Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3.  Vyberte **historii aktualizací**.

![Historie aktualizace Azure Stack](./media/azure-stack-update-apply/image7.png)

# <a name="next-steps"></a>Další postup

-   [Správa aktualizací v přehledu služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Zásady údržby služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
