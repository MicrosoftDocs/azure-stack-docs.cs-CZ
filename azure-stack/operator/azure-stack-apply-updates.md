---
title: Nainstalovat aktualizace Azure Stack | Microsoft Docs
description: Naučte se instalovat aktualizace Azure Stack.
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
ms.date: 09/10/2019
ms.author: mabriggs
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 2153a91bcf833386ef5884a9fd4e7c0fc759c01b
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70863075"
---
# <a name="install-azure-stack-updates"></a>Nainstalovat aktualizace Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Balíčky aktualizací můžete nainstalovat pomocí okna **aktualizace** v Azure Stack. Tento článek vás provede jednotlivými kroky aktualizace, monitorování a řešení potíží s procesem aktualizace. Pomocí okna aktualizace si můžete zobrazit informace o aktualizaci, nainstalovat aktualizace, sledovat průběh aktualizace, zkontrolovat historii aktualizací a zobrazit aktuální Azure Stack a verzi balíčku OEM.

Aktualizace můžete spravovat z portálu pro správu a pomocí části **aktualizace** na řídicím panelu:

- Zobrazit důležité informace, jako je aktuální verze
- Nainstalujte aktualizace a sledujte průběh.
- Zkontrolujte historii aktualizací pro dříve nainstalované aktualizace.
- Zobrazit aktuální verzi balíčku OEM v cloudu

## <a name="determine-the-current-version"></a>Určení aktuální verze

Aktuální verzi Azure Stack můžete zobrazit v okně **aktualizace** . Pro otevření:

1.  Otevřete portál Azure Stack správce.

2.  Vyberte **řídicí panel**. V okně **aktualizace** je uvedena aktuální verze.

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-update-apply/image1.png)

    Například v tomto obrázku je verze 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a sledování průběhu

1. Otevřete portál pro správu Azure Stack.

2. Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3. Vyberte dostupnou aktualizaci, kterou chcete nainstalovat. Pokud nemáte aktualizaci označenou jako **k dispozici**, musíte [připravit balíček aktualizace](azure-stack-update-prepare-package.md) .

4. Vyberte **aktualizovat hned**.

    ![Podrobnosti o spuštění aktualizace Azure Stack](./media/azure-stack-update-apply/image2.png)

5. Stav vysoké úrovně můžete zobrazit, protože proces aktualizace prochází různými subsystémy v Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují portál pro správu i uživatele. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a počet probíhajících.

6. Pro stažení úplných protokolů vyberte **Souhrn stažení** v okně Podrobnosti o spuštění aktualizace.

    Pokud narazíte na problém při monitorování aktualizace, můžete pomocí [privilegovaného koncového bodu](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) monitorovat průběh spuštění aktualizace Azure Stack. Pomocí privilegovaného koncového bodu můžete také pokračovat v neúspěšném spuštění aktualizace z posledního úspěšného kroku, pokud Azure Stack portál nebude k dispozici. Pokyny najdete v tématu [monitorování aktualizací v Azure Stack pomocí prostředí PowerShell](azure-stack-update-monitor.md).

    ![Podrobnosti o spuštění aktualizace Azure Stack](./media/azure-stack-update-apply/image3.png)

7. Po dokončení dokončí poskytovatel prostředků aktualizace **úspěšné** potvrzení, které ukáže, že proces aktualizace skončil a jak dlouho trvalo. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Azure-Stack-Update-Apply](./media/azure-stack-update-apply/image4.png)

    Pokud se aktualizace nezdařila, sestavy okna **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů Azure Stack pomáhá s diagnostikou a řešením potíží.

## <a name="review-update-history"></a>Zkontrolovat historii aktualizací

1. Otevřete portál pro správu.

2. Vyberte **řídicí panel**. Vyberte **aktualizovat**.

3. Vyberte **historii aktualizací**.

    ![Historie aktualizace Azure Stack](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Další postup

-   [Správa aktualizací v přehledu služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Zásady údržby služby Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
