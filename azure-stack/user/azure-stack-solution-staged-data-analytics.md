---
title: Vytvořit řešení analýzy pracovních dat s využitím Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit řešení analýzy pracovních dat s využitím Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: a10f034e05e97942a6c20d019d0d1930f49f8c81
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691453"
---
# <a name="tutorial-create-a-staged-data-analytics-solution-with-azure-and-azure-stack"></a>Kurz: Vytvořit řešení analýzy pracovních dat s využitím Azure a Azure Stack 

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Další informace o použití místních i veřejných cloudových prostředích k uspokojení požadavků podniků více zařízení. Azure Stack nabízí rychlé, zabezpečené a flexibilní řešení pro shromažďování, zpracování, ukládání a distribuci místními a vzdálenými daty. To je důležité, když zabezpečení, důvěrnosti, podnikové zásady a dodržování legislativních požadavků může lišit mezi umístěními a uživatelů.

V tomto vzoru jsou vaši zákazníci shromažďování dat, která vyžaduje analýzu místě kolekce tak, aby provádět rychlé rozhodování. Toto shromažďování dat často dochází k dispozici žádné internetové připojení. Když se naváže spojení, budete muset provádět náročné analýzu dat umožňuje získat další informace. Můžete přesto analyzovat data, když veřejný cloud je příliš pozdě nebo není k dispozici.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření objektu blob úložiště nezpracovaná data.
> - Vytvořte novou funkci Azure Stack můžete do Azure přesunout vyčištění dat ze služby Azure Stack.
> - Vytvoření funkce aktivované úložiště Blob.
> - Vytvoření účtu úložiště Azure Stack, který obsahuje objekt blob a fronty.
> - Vytvoření funkce aktivované frontou.
> - Funkce aktivovaná testu do fronty.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a umožňuje ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Dokument White Paper [aspekty návrhu pro hybridní aplikace](https://aka.ms/hybrid-cloud-applications-pillars) kontroly pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridní aplikace. Aspekty návrhu při optimalizaci návrhu hybridní aplikace, minimalizovat problémy v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

Některé přípravy je nutné k vytvoření tohoto řešení:

-   Azure Stack nainstalovaná a funkční. Další informace najdete v tématu [Přehled služby Azure Stack](azure-stack-storage-overview.md) článku.

-   Předplatné Azure. Je možné [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

-   Stáhnout a nainstalovat [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

-   Budete muset zadat svoje vlastní data zpracovat pomocí funkcí. Data musí být vygenerovaný a pro nahrání do kontejneru úložiště objektů blob v Azure stacku.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

### <a name="scalability-considerations"></a>Aspekty zabezpečení

Služba Azure functions a řešení úložiště škálovat podle objemu dat a zpracování požadavků. Informace o Azure škálovatelnost a cíle najdete v tématu [dokumentace ke službě Azure škálovatelnost](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

### <a name="availability-considerations"></a>Aspekty dostupnosti

Úložiště je zvážení dostupnosti pro tento model. Je vyžadováno pro zpracování velkých objemů dat svazku a distribuce připojení prostřednictvím rychlých linkách.

### <a name="manageability-considerations"></a>Aspekty správy

Zvažte, jak vývojářské nástroje a správy zdrojových kódů vám umožní spravovat vaše řešení.

## <a name="create-the-raw-data-storage-blob"></a>Vytvoření objektu blob úložiště nezpracovaných dat

Účet a objektů blob v kontejneru úložiště bude obsahovat všechny původní data, které jsou vygenerovány aktivitami v místním, včetně činnost počítače a zaměstnance, data zařízení, výrobní metrik a jiné vytváření sestav.

1.  Přihlaste se k [ *portálu Azure Stack*](https://portal.local.azurestack.external/).

2.  Na portálu Azure Stack rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **všechny služby**. Přejděte dolů k položce **úložiště** a zvolte **účty úložiště**. V okně účty úložiště, zvolte **přidat**.

3.  Pro účet, použijte následující informace:

    a.  Název: **Nějaké si zvolte**

    b.  Model nasazení: **Resource Manager**

    c.  Druh účtu: **Storage (pro obecné účely V1)**

    d.  Umístění: **Západní USA**

    e.  Replikace: **Místně redundantní úložiště (LRS)**

    f.  Výkon: **Standard**

    g.  Vyžádání bezpečného přenosu: **Disabled** (Zakázáno)

    h.  Předplatné: Vyberte jednu

    i.  Skupina prostředků: Zadejte novou skupinu prostředků nebo vyberte existující skupinu prostředků

    j.  Konfigurovat virtuální sítě: **Disabled** (Zakázáno)

4.  Vyberte **chcete vytvořit účet úložiště vytvořte**.

    ![Vytvoření účtu úložiště ve službě Azure Stack](media/azure-stack-solution-staged-data-analytics/image1.png)

5.  Po vytvoření vyberte název účtu úložiště.

6.  V okně účtu v rámci **služby BLOB SERVICE** záhlaví, vyberte **kontejnery**.

7.  V horní části okna vyberte **+ kontejner.** a vyberte **kontejneru**.

    ![Vyberte kontejner ve službě Azure Stack](media/azure-stack-solution-staged-data-analytics/image2.png)

8.  Název: **Podle vašeho výběru**

9.  Úroveň veřejného přístupu: **Kontejner** (anonymní přístup pro čtení kontejnerům a objektům BLOB)

10.  Vyberte **OK**.

## <a name="create-an-azure-stack-function"></a>Vytvoření funkce Azure Stack

Vytvoření nové funkce služby Azure Stack můžete do Azure přesunout vyčištění dat ze služby Azure Stack.

### <a name="create-the-azure-stack-function-app"></a>Vytvoření aplikace funkcí Azure Stack

1. Přihlaste se [portálu Azure Stack](https://portal.local.azurestack.external).
2. Vyberte **Všechny služby**.
3. Vyberte **aplikace Function App** v **Web + mobilní zařízení** skupiny.

4.  Vytvoření aplikace function app pomocí nastavení uvedená v následující tabulce:

    | Nastavení | Navrhovaná hodnota | Popis |
    | ---- | ---- | ---- |
    | App name (Název aplikace) | Globálně jedinečný název | Název identifikující novou aplikaci Function App. Platné znaky jsou `a` - `z`, `0``-9`, a `-`. |
    | Předplatné | Vaše předplatné | Předplatné, pod kterým je tato nová aplikace Function App vytvořena. |
    | **Skupina prostředků** |  |  |
    | myResourceGroup | Název nové skupiny prostředků, ve které chcete vytvořit aplikaci Function App. |  |
    | Operační systém | Windows | Hostování bez serveru je momentálně dostupné jenom při spuštění ve Windows. |
    | **Plán hostování** |  |  |
    | Plán Consumption | Plán hostování, který určuje způsob přidělování prostředků aplikaci Function App. Ve výchozím plánu Consumption prostředky přidávají dynamicky podle požadavků vašich funkcí. V tomto hostování bez serveru, platíte jenom dobu, kdy jsou funkce spuštěné. |  |
    | Location | Oblast nejbližší | Zvolte oblast vaší blízkosti nebo v blízkosti jiných služeb vaší funkce přístupu. |
    | **Účet úložiště** |  |  |
    | \<účet úložiště vytvořený výše > | Název nového účtu úložiště, který bude aplikace Function App používat. Názvy účtů úložiště musí být dlouhý 3 až 24 znaků. Název může použít pouze číslice a malá písmena. Můžete taky použít existující účet. |  |

    **Příklad:**

    ![Určení nastavení nové aplikace Function App](media/azure-stack-solution-staged-data-analytics/image6.png)

5.  Aplikaci Function App zřídíte a nasadíte kliknutím na **Vytvořit**.

6.  Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Nasazení bylo úspěšné – nové funkce](media/azure-stack-solution-staged-data-analytics/image7.png)

7.  Vyberte **přejít k prostředku** Chcete-li zobrazit novou aplikaci function app.

![Zobrazit novou aplikaci funkcí](media/azure-stack-solution-staged-data-analytics/image8.png)

### <a name="add-a-function-to-the-azure-stack-function-app"></a>Přidání funkce do aplikace funkcí Azure Stack

1.  Po kliknutí na vytvořit novou funkci **funkce**, pak bude **+ nová funkce** tlačítko.

    ![Vytvoření nové funkce](media/azure-stack-solution-staged-data-analytics/image3.png)

2.  Vyberte **Trigger časovače**.

    ![Trigger časovače pro nové funkce](media/azure-stack-solution-staged-data-analytics/image4.png)

3.  Vyberte **C\#**  jako jazyk a název funkce: `upload-to-azure`.  Nastavte plán `0 0 * * * *`, který v procesu CRON zápis je jednou za hodinu.

    ![Nové nastavení – funkce](media/azure-stack-solution-staged-data-analytics/image5.png)

## <a name="create-a-blob-storage-triggered-function"></a>Vytvoření funkce aktivované službou Blob Storage

1. Rozbalte aplikaci function app a vyberte **+** vedle **funkce**.

2. Do vyhledávacího pole zadejte `blob` a zvolte jazyk požadovaný pro **aktivační událost objektů Blob** šablony.

   ![Vyberte šablonu funkce aktivované úložištěm objektů blob.](media/azure-stack-solution-staged-data-analytics/image10.png)

3. Použijte nastavení uvedená v následující tabulce:

   | Nastavení | Navrhovaná hodnota | Popis |
   | ------- | ------- | ------- |
   | Name | Jedinečný název v rámci aplikace Function App | Název této funkce aktivované objektem blob. |
   | `Path` | \<cesta z výše uvedených umístění úložiště > | Monitorované umístění ve službě Blob Storage. Název souboru objektu blob se předá v rámci vazby jako parametr name. |
   | Připojení k účtu úložiště | Připojení aplikace – funkce | Můžete použít připojení k účtu úložiště už používá vaši aplikaci function app, nebo vytvořte novou. |

   **Příklad:**

   ![Vytvoření funkce aktivované službou Blob Storage](media/azure-stack-solution-staged-data-analytics/image11.png)

4. Vyberte **vytvořit** vytvořte novou funkci.

### <a name="test-the-function"></a>Testování funkce

1.  Na webu Azure Portal přejděte do funkce. Rozbalte **protokoly** v dolní části stránky a ujistěte se, není pozastavené streamování protokolů.

2.  Otevřete Průzkumníka služby Storage a připojte se k účtu úložiště, vytvořili na začátku této části.

3.  Rozbalte účet úložiště **kontejnery objektů Blob**, a objekt blob jste vytvořili dříve. Vyberte **nahrát** a potom **nahrávání souborů**.

    ![Nahrání souboru do kontejneru objektů blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4.  V dialogovém okně nahrávání souborů vyberte pole soubory. Přejděte k souboru na místním počítači, jako je například soubor obrázku, vyberte ho a vyberte **otevřít** a potom **nahrát**.

5.  Vraťte se do protokolů funkce a ověřte, zda že byl načten objekt blob.

    **Příklad:**

    ![Zobrazte si zprávy v protokolech.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="create-an-azure-stack-storage-account"></a>Vytvoření účtu úložiště Azure Stack

Vytvoření účtu úložiště Azure Stack, který obsahuje objekt blob a fronty.

### <a name="storage-blob--data-archiving"></a>Archivace dat úložiště objektů Blob

Tento účet úložiště bude obsahovat dva kontejnery. Tyto kontejnery se skládají z sloužící k uchování dat archivu jeden objekt blob a fronty pro zpracování dat, které jsou přiřazeny k distribuci hlavním office.

Tyto kroky a použijte nastavení uvedené výše vytvořte další účet a objektů blob v kontejneru úložiště jako náš archiv služby storage.

### <a name="storage-queue--filtered-data-holding"></a>Podniku filtrovat Data ve frontě úložiště

1.  Tyto kroky a použijte nastavení uvedené výše pro přístup k nový účet úložiště.

2.  V části přehledu účtu úložiště vyberte **fronty.**

3.  Vyberte **+ fronta** a vyplnit **název** pole a vyplnit název pro novou frontu.

4.  Vyberte **OK.**

    ![Fronta úložiště](media/azure-stack-solution-staged-data-analytics/image14.png)

    ![Přidejte název pro novou frontu úložiště](media/azure-stack-solution-staged-data-analytics/image15.png)

## <a name="create-a-queue-triggered-function"></a>Vytvoření funkce aktivované frontou

1.  Postupujte podle kroků v části výše uvedené funkce vytvoření k vytvoření další funkce služby Azure Stack s triggerem queue blob aktivační událost Instead of.

2.  Použijte nastavení uvedená v následující tabulce:

    | Nastavení | Navrhovaná hodnota | Popis |
    | ------- | ------- | ------- |
    | Název | Jedinečný název v rámci aplikace Function App | Název této funkce aktivované frontou. |
    | `Path` | \<cesta z výše uvedených umístění úložiště > | Umístění v úložišti, které jsou monitorovány. Název souboru fronty je předán v rámci vazby jako parametr name. |
    | Připojení k účtu úložiště | Připojení aplikace – funkce | Můžete použít připojení k účtu úložiště už používá vaši aplikaci function app, nebo vytvořte novou. |

3.  Vyberte **vytvořit** vytvořte novou funkci.

## <a name="test-the-queue-triggered-function"></a>Funkce aktivovaná testu do fronty

1. Na portálu Azure Stack přejděte do funkce. Rozbalte **protokoly** v dolní části stránky a ujistěte se, není pozastavené streamování protokolů.

2. Otevřete Průzkumníka služby Storage a připojte se k účtu úložiště, vytvořili na začátku této části.

3. Rozbalte účet úložiště **kontejnery objektů Blob**, a objekt blob jste vytvořili dříve. Vyberte **nahrát** a potom **nahrávání souborů.**

   ![Nahrání souboru do kontejneru objektů blob.](media/azure-stack-solution-staged-data-analytics/image12.png)

4. V dialogovém okně nahrávání souborů vyberte pole soubory. Přejděte k souboru na místním počítači, jako je například soubor obrázku, vyberte ho a vyberte **otevřít** a potom **nahrát**.

5. Vraťte se do protokolů funkce a ověřte, zda že byl načten objekt blob.

   **Příklad:**

   ![Zobrazte si zprávy v protokolech.](media/azure-stack-solution-staged-data-analytics/image13.png)

## <a name="securely-stored-and-accessed-compliant-data"></a>Bezpečně uložená a využívaných dat odpovídající

Globální podnikových dat je bezpečně uložená, zpracování, distribuovat a získávat přístup k Azure a Azure Stack připravené datové analýzy a vlastní koncový bod direktivy. Vzdálené kanceláři zaměstnance a strojů aktivity, zařízení data a obchodní metriky jsou neustále zkompilován, uložené, jsou testovány z hlediska dodržování předpisů a distribuovaná podle zásad společnosti a regionální nařízení.

## <a name="next-steps"></a>Další postup

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
