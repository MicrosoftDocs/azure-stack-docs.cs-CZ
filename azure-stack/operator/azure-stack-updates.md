---
title: Správa aktualizací v Azure Stack přehled | Microsoft Docs
description: Přečtěte si o správě aktualizací pro Azure Stack integrovaných systémech.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: a5fb41e63a8cd9e5f57e4476b1b7c23738f116bb
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418016"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Přehled správy aktualizací v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Balíčky služby Microsoft Update pro Azure Stack integrované systémy jsou obvykle vydány každý měsíc. Zeptejte se výrobce OEM na svůj konkrétní proces oznámení, aby bylo zajištěno, že oznámení o aktualizacích dosáhnou vaší organizace. Informace o vydaných vydáních s aktivní podporou najdete v části **Přehled** > **poznámek k verzi** .

Každá verze aktualizací softwaru společnosti Microsoft je tvořena jedním balíčkem aktualizace. Jako operátor Azure Stack můžete naimportovat, instalovat a monitorovat průběh instalace balíčků aktualizací z portálu pro správu Azure Stack.

Dodavatel OEM bude také vydávat aktualizace, například ovladače a aktualizace firmwaru. I když jsou tyto aktualizace dodávány jako samostatné balíčky podle dodavatele, některé jsou importovány, instalovány a spravovány stejným způsobem jako balíčky aktualizací od společnosti Microsoft.

Chcete-li zachovat systém v rámci podpory, je třeba zachovat Azure Stack aktualizace na konkrétní úroveň verze. Ujistěte se, že jste provedli kontrolu [zásad Azure Stack Servicing](azure-stack-servicing-policy.md).

> [!NOTE]
> Balíčky aktualizací Azure Stack nelze použít na Azure Stack Development Kit (ASDK). Balíčky aktualizací jsou navržené pro integrované systémy. Další informace najdete v tématu o [opětovném nasazení ASDK](../asdk/asdk-redeploy.md).

## <a name="the-update-resource-provider"></a>Poskytovatel prostředku aktualizace

Azure Stack obsahuje poskytovatele prostředků aktualizace, který zpracovává aplikaci aktualizací softwaru společnosti Microsoft. Tento poskytovatel kontroluje, zda jsou aktualizace aplikovány na všechny fyzické hostitele, Service Fabric aplikací a modulech runtime a na všech virtuálních počítačích infrastruktury a jejich přidružených službách.

Při instalaci aktualizací můžete zobrazit stav vysoké úrovně, protože proces aktualizace cílí na různé subsystémy v Azure Stack (například fyzické hostitele a virtuální počítače infrastruktury).

## <a name="plan-for-updates"></a>Plánování aktualizací

Důrazně doporučujeme, abyste uživatelům informovali o všech operacích údržby a v případě potřeby naplánovali normální časová období údržby během nepracovních hodin. Operace údržby mohou ovlivnit úlohy tenanta i operace portálu.

Při plánování časového období údržby je důležité zkontrolovat konkrétní typ balíčku aktualizace vydaný od společnosti Microsoft, který je vyvolán v příslušné poznámce k verzi. Kromě příležitostné opravy hotfix bude mít každý balíček aktualizace odpovídající typ, **úplný** nebo **expresní**. Balíčky s úplnými aktualizacemi aktualizují fyzické hostitelské operační systémy v jednotce škálování a vyžadují větší časový interval pro správu a údržbu. Balíčky Express Update mají rozsah a neaktualizují základní fyzické hostitelské operační systémy.

Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

```powershell
Test-AzureStack -Group UpdateReadiness
```

## <a name="using-the-update-tile-to-manage-updates"></a>Použití dlaždice aktualizace ke správě aktualizací

Aktualizace můžete spravovat z portálu pro správu. Jako operátor Azure Stack můžete na řídicím panelu použít dlaždici **aktualizace** na:

- Zobrazení důležitých informací, jako je například aktuální verze.
- Nainstalujte aktualizace a sledujte průběh.
- Zkontrolujte historii aktualizací pro dříve nainstalované aktualizace.
- Zobrazit aktuální verzi balíčku OEM v cloudu

## <a name="determine-the-current-version"></a>Určení aktuální verze

Aktuální verzi Azure Stack můžete zobrazit na dlaždici **aktualizace** . Chcete-li otevřít dlaždici:

1. Otevřete portál Azure Stack správce.
2. Vyberte **řídicí panel**. Na dlaždici **aktualizace** se zobrazí aktuální verze.

    ![Dlaždice aktualizace na výchozím řídicím panelu](./media/azure-stack-updates/image1.png)

    Například v tomto obrázku je verze 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalace aktualizací a sledování průběhu

1. Otevřete portál Azure Stack správce.
2. Vyberte **řídicí panel**. Vyberte dlaždici **aktualizace** .
3. Vyberte **aktualizovat hned**.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/azure-stack-update-button.png)

4. Stav vysoké úrovně můžete zobrazit, protože proces aktualizace prochází různými subsystémy v Azure Stack. Příklady subsystémů zahrnují fyzické hostitele, Service Fabric, virtuální počítače infrastruktury a služby, které poskytují správce i uživatelské portály. V průběhu procesu aktualizace hlásí poskytovatel prostředků aktualizace další podrobnosti o aktualizaci, například počet kroků, které byly úspěšné, a také počet probíhajících.

5. Pokud si chcete stáhnout úplné protokoly, vyberte **Stáhnout úplné protokoly** z okna podrobností o spuštění aktualizace.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/update-run-details.png)

6. Po dokončení dokončí poskytovatel prostředků aktualizace **úspěšné** potvrzení, které indikuje, že se proces aktualizace dokončil a jak dlouho trvala. Odtud můžete zobrazit informace o všech aktualizacích, dostupných aktualizacích nebo nainstalovaných aktualizacích pomocí filtru.

    ![Podrobnosti o úspěšném spuštění aktualizace Azure Stack](media/azure-stack-updates/update-success.png)

   Pokud se aktualizace nezdařila, sestavy dlaždice **aktualizace** **vyžadují pozornost**. Použijte možnost **Stáhnout úplné protokoly** a získejte stav vysoké úrovně, kde se aktualizace nezdařila. Shromažďování protokolů Azure Stack pomáhá zjednodušit diagnostiku a řešení potíží.

## <a name="next-steps"></a>Další kroky

- [Zásady údržby služby Azure Stack](azure-stack-servicing-policy.md) 
- [Správa oblastí v Azure Stack](azure-stack-region-management.md)
