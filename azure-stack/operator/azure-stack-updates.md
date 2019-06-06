---
title: Správa aktualizací v přehledu služby Azure Stack | Dokumentace Microsoftu
description: Další informace o správě aktualizací pro integrované systémy Azure Stack.
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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: 5fa84271c02ebc749871116badb3c767812a48ec
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691549"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Správa aktualizací v přehledu služby Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Balíčky aktualizací Microsoftu pro Azure Stack integrované systémy pro uvolnění obvykle každý měsíc. Výrobce vašeho (výrobcem OEM) požádejte o proces jejich konkrétní upozornění k zajištění oznámení o aktualizacích kontaktovat vaše organizace. Můžete zkontrolovat v této knihovně dokumentace v části **přehled** > **poznámky k verzi** informace o vydaných verzích, které jsou v aktivní technické podpory.

Každá verze aktualizace softwaru Microsoftu se dodává v sadě jako jednu aktualizaci balíčku. Jako operátory Azure stacku můžete importovat, nainstalovat a sledovat průběh instalace balíčků aktualizací z portálu pro správu služby Azure Stack.

Od dodavatele OEM také vydá aktualizace, jako je například aktualizace ovladače a firmware. Zatímco tyto aktualizace jsou dodávány jako samostatné balíčky podle dodavatele, některé jsou importovat, nainstalovat a spravovat stejným způsobem jako balíčky aktualizací od Microsoftu.

Aby váš systém v rámci podpory, je nutné zachovat aktualizovat na úroveň konkrétní verzi služby Azure Stack. Ujistěte se, abyste se seznámili [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).

> [!NOTE]
> Balíčky aktualizací pro Azure Stack nejde použít pro Azure Stack Development Kit (ASDK). Balíčky aktualizací jsou navržené pro integrované systémy. Informace najdete v tématu [opětovné nasazení ASDK](../asdk/asdk-redeploy.md).

## <a name="the-update-resource-provider"></a>Aktualizace poskytovatele prostředků

Azure Stack zahrnuje poskytovatele prostředků aktualizace, která zpracovává použití aktualizací softwaru společnosti Microsoft. Tento zprostředkovatel ověří, že se aktualizace napříč všechny fyzické hostitele, aplikace Service Fabric a moduly runtime a všechny virtuální počítače infrastruktury a jejich přidružené služby.

Jak nainstalovat aktualizace, můžete zobrazit informace na nejvyšší úrovni jako cíle procesu aktualizace různé subsystémy ve službě Azure Stack (například fyzické hostitele a infrastrukturu virtuálních počítačů).

## <a name="plan-for-updates"></a>Plán pro aktualizace

Důrazně doporučujeme, upozornění uživatelů na jakékoli operace údržby a, abyste naplánovali normální správu a údržbu během mimo pracovní dobu Pokud je to možné. Operace údržby může ovlivnit úlohy klientů a operací na portálu.

Při plánování pro okno údržby, je důležité zkontrolovat konkrétní typ balíčku aktualizací vydávat od Microsoftu, jak je uvedeno v příslušné poznámky. Kromě občasné oprav Hotfix, bude mít každý balíček aktualizace odpovídající typ **úplné** nebo **Express**. Úplnou aktualizaci balíčků aktualizovat operační systémy fyzického hostitele v jednotce škálování a vyžadují větší časové období údržby. Express s aktualizací update balíčky jsou omezená a neaktualizují základní operační systémy fyzického hostitele.

Před instalací této aktualizace, spusťte [testovací AzureStack](azure-stack-diagnostic-test.md) s následujícími parametry do ověřte stav služby Azure Stack a vyřešte všechny provozní problémy zjištěné, včetně všech upozornění a chyby. Také aktivní výstrahy můžete zkontrolovat a vyřešit všechny, které vyžadují nějakou akci.  

```powershell
Test-AzureStack -Group UpdateReadiness
```

## <a name="using-the-update-tile-to-manage-updates"></a>Správa aktualizací pomocí aktualizovat dlaždici

Správa aktualizací z portálu správce. Jako operátory Azure stacku můžete použít **aktualizace** dlaždici v řídicím panelu:

- Zobrazit důležité informace, jako je aktuální verze.
- instalace aktualizací a sledovat průběh.
- Kontrola historie aktualizace pro dříve nainstalované aktualizace.
- Zobrazte aktuální verze balíčku výrobce OEM v cloudu.

## <a name="determine-the-current-version"></a>Zjistit aktuální verzi

Můžete zobrazit aktuální verzi služby Azure Stack v **aktualizace** dlaždici. Otevřete dlaždici:

1. Otevření portálu Správce služby Azure Stack.
2. Vyberte **řídicí panel**. V **aktualizace** dlaždice, aktuální verze je uvedená.

    ![Aktualizace dlaždice na výchozí řídicí panel](./media/azure-stack-updates/image1.png)

    Verze je na tomto obrázku 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalovat aktualizace a sledování průběhu

1. Otevření portálu Správce služby Azure Stack.
2. Vyberte **řídicí panel**. Vyberte **aktualizace** dlaždici.
3. Vyberte **aktualizovat**.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/azure-stack-update-button.png)

4. Si můžete zobrazit podrobný stav procesu aktualizace prochází různé subsystémy ve službě Azure Stack. Příklad subsystémy zahrnují fyzické hostitele, Service Fabric, infrastruktury virtuálních počítačů a služeb, které poskytují portálech pro správce i uživatele. Aktualizace poskytovatele prostředků v celém procesu aktualizace sestavy další podrobnosti o aktualizaci, jako je například počet kroků, které byly úspěšné, stejně jako číslo v průběhu.

5. Vyberte **stáhnout úplných protokolů** z aktualizace spusťte okno s podrobnostmi o stažení úplných protokolů.

    ![Podrobnosti o spuštění aktualizace Azure Stack](media/azure-stack-updates/update-run-details.png)

6. Po dokončení aktualizace poskytovatel prostředků nabízí **Succeeded** potvrzení k označení, že dokončení procesu aktualizace a jak dlouho to trvalo. Odtud můžete zobrazit informace o aktualizace, aktualizace nebo nainstalovaných aktualizací pomocí filtru, který.

    ![Aktualizace Azure zásobníku spouštění podrobnosti o úspěchu](media/azure-stack-updates/update-success.png)

   Pokud se aktualizace nezdaří, **aktualizovat** dlaždici sestavy **vyžaduje pozornost**. Použití **stáhnout úplných protokolů** umožňuje získat podrobný stav kde aktualizace se nezdařila. Shromažďování protokolů služby Azure Stack pomáhá usnadnit diagnostiku a řešení potíží.

## <a name="next-steps"></a>Další postup

- [Zásady údržby služby Azure Stack](azure-stack-servicing-policy.md) 
- [Správa oblastí ve službě Azure Stack](azure-stack-region-management.md)
